# Architecture Decisions

## Tenancy Model Options

### Option A: Container per User
- **How:** Docker container per customer, orchestrated via K8s or Nomad
- **Pros:** True isolation, easy to scale horizontally, can customize per user
- **Cons:** Higher cost per user, complexity of orchestration
- **Cost:** ~$5-10/month per active user (depending on usage)

### Option B: Shared Multi-Tenant OpenClaw
- **How:** Single OpenClaw instance, namespace isolation per user
- **Pros:** Much cheaper, simpler to manage
- **Cons:** Risk of cross-tenant data leakage, harder to customize
- **Cost:** ~$0.50-2/month per user

### Option C: VM per User (dedicated)
- **How:** Lightweight VM (Firecracker, Kata containers)
- **Pros:** Maximum isolation, feels like "your own server"
- **Cons:** Expensive, slow to provision
- **Cost:** ~$10-20/month per user

### Option D: Shared Cloud + Local LLM (REVISED)
- **How:** One cloud-hosted control plane, shared local LLM for all tenants
- **Pros:** Ultra-low API costs, privacy marketing angle, fast inference
- **Cons:** Hardware cost upfront, scaling complexity, single point of failure
- **Cost:** ~$300-600/month for MVP (revised down)

### Option E: Cloud-Per-User
- **How:** Spin up fresh cloud instance per user (Hetzner, Vultr, etc.)
- **Pros:** True isolation, simple mental model, user owns their instance
- **Cons:** Slower provisioning, per-user overhead, harder to manage at scale
- **Cost:** ~$5-15/month per active user

### Option F: Cloud-Per-10-Users (NEW — Hybrid)
- **How:** One cloud instance hosts 10 tenants (containerized isolation)
- **Pros:** Better utilization than 1:1, still good isolation, simpler than shared LLM cluster
- **Cons:** Noisy neighbor risk, 10x blast radius if instance fails
- **Cost:** ~$1-2/month per user

## Selected: Option F — Cloud-Per-10-Users (Hybrid) with Auto-Scaling

### How It Works

```
┌─────────────────────────────────────────────────────────────┐
│                    HETZNER CPX31                             │
│              (4 vCPU, 16GB RAM, €14.70/month)               │
│                                                              │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐      ┌──────────┐  │
│  │ Tenant 1 │ │ Tenant 2 │ │ Tenant 3 │ ...  │ Tenant 10│  │
│  │ Container│ │ Container│ │ Container│      │ Container│  │
│  │  OpenClaw│ │  OpenClaw│ │  OpenClaw│      │  OpenClaw│  │
│  │  + Qwen  │ │  + Qwen  │ │  + Qwen  │      │  + Qwen  │  │
│  └──────────┘ └──────────┘ └──────────┘      └──────────┘  │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │           SHARED RESOURCES                               │ │
│  │  • Ollama model cache (Qwen2.5-7B loaded once)          │ │
│  │  • Reverse proxy (routes tenant1.execflow.io)           │ │
│  │  • Monitoring/logs                                       │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Instance Spec (Per 10 Users)

| Provider | Instance | CPU | RAM | Storage | Cost/month | Per-User Cost |
|----------|----------|-----|-----|---------|------------|---------------|
| **Hetzner** | CPX31 | 4 vCPU | 16GB | 160GB NVMe | €14.70 (~$16) | ~$1.60 |
| **Hetzner** | CPX41 | 8 vCPU | 32GB | 240GB NVMe | €26.50 (~$29) | ~$2.90 |
| **Vultr** | Cloud 4vCPU | 4 vCPU | 16GB | 100GB | $24 | ~$2.40 |

### Resource Allocation (Per Tenant Container)

| Resource | Allocation | Notes |
|----------|------------|-------|
| CPU | 0.4 vCPU (soft limit) | Burst to 2 vCPU if available |
| RAM | 1.5GB | Qwen2.5-7B ~800MB + OpenClaw overhead |
| Storage | 10GB | Workspace + logs |
| Network | Shared | Fair queueing |

### Shared Model Cache

Instead of each container loading Qwen2.5-7B (800MB x 10 = 8GB), use a shared Ollama instance:

```yaml
# docker-compose.yml
services:
  ollama:
    image: ollama/ollama
    volumes:
      - ollama-cache:/root/.ollama
    # Model loaded once, shared via API
    
  tenant-1:
    image: execflow/openclaw
    environment:
      - LLM_URL=http://ollama:11434
      - TENANT_ID=tenant-1
      
  tenant-2:
    image: execflow/openclaw
    environment:
      - LLM_URL=http://ollama:11434
      - TENANT_ID=tenant-2
  # ... etc
```

**Benefits:**
- Model loaded once (saves ~7GB RAM)
- Faster container startup
- Easy model updates (restart one service)

### Auto-Scaling Strategy

#### Thresholds for New Instance

| Metric | Threshold | Action |
|--------|-----------|--------|
| **Tenant count** | ≥ 8 tenants | Provision new instance (keep 2 slots buffer) |
| **CPU usage** | > 70% for 5min | Migrate 2 tenants to new instance |
| **Memory usage** | > 80% for 5min | Migrate heavy tenant to new instance |
| **Response time** | > 5s p95 | Scale up or migrate |

#### Proactive Scaling

```python
# Auto-scaler logic
async def check_scaling():
    for instance in active_instances:
        metrics = await instance.get_metrics()
        
        # Scale up if approaching limits
        if metrics.tenant_count >= 8:
            await provision_new_instance(warm=True)
            
        # Rebalance if uneven load
        if metrics.cpu_percent > 70:
            heavy_tenants = await find_heavy_tenants(instance)
            for tenant in heavy_tenants[:2]:
                await migrate_tenant(tenant, find_least_loaded_instance())
                
        # Scale down if over-provisioned
        if metrics.tenant_count <= 3 and len(active_instances) > 1:
            await migrate_all_tenants(instance, other_instances)
            await terminate_instance(instance)
```

#### Warm Pool

Keep 1-2 "warm" instances ready:
- Instance created, Docker images pulled
- No tenants assigned
- On signup: instant assignment (< 5 seconds)
- Cost: ~$16-32/month for always-ready capacity

### Load Balancing & Routing

```
User request → Cloudflare → ExecFlow Router → Instance
                                              ↓
                                         Tenant Container
```

**Router logic:**
1. Parse subdomain (`chris.execflow.io`)
2. Look up tenant → instance mapping in Redis
3. Route to correct instance IP + container port
4. If instance unhealthy, migrate tenant to new instance

### Economics

| Metric | Value |
|--------|-------|
| Infrastructure cost | ~$1.60-2.90/month per user |
| Charge user | $10-15/month |
| Gross margin | ~75-85% |
| Users per instance | 8-10 (target), max 12 (burst) |
| Warm pool cost | ~$32/month (2 instances) |

### Provisioning Flow

```python
async def provision_user(user_id):
    # Find instance with capacity (< 8 tenants)
    instance = await db.instances.find_one(
        {"tenant_count": {"$lt": 8}, "status": "active"}
    )
    
    if not instance:
        # Use warm instance or create new
        instance = await get_warm_instance() or await create_new_instance()
    
    # Create tenant container
    await instance.create_container(
        name=f"tenant-{user_id}",
        env={
            "TENANT_ID": user_id,
            "LLM_URL": "http://ollama:11434",
            "WORKSPACE": f"/workspaces/{user_id}"
        }
    )
    
    # Configure Telegram bot
    bot_token = await create_telegram_bot()
    await instance.configure_bot(user_id, bot_token)
    
    # Update routing
    await proxy.add_route(f"{user_id}.execflow.io", instance.ip, container_port)
    
    # Trigger warm pool refill if needed
    if len(warm_instances) < 2:
        await provision_new_instance(warm=True)
    
    return {"bot_username": bot_username, "dashboard": f"https://{user_id}.execflow.io"}
```

### Isolation Strategy

| Layer | Isolation Method |
|-------|------------------|
| **Filesystem** | Docker volume per tenant (`/workspaces/{id}`) |
| **Network** | Separate container namespace, shared bridge |
| **Memory** | cgroup limits (1.5GB soft, 2GB hard) |
| **CPU** | CFS shares (fair scheduling) |
| **Secrets** | Environment variables per container |
| **Database** | SQLite per tenant (in workspace) |

### Monitoring & Alerts

| Alert | Condition | Action |
|-------|-----------|--------|
| Instance full | 8+ tenants | Provision new instance |
| High CPU | > 70% for 5min | Rebalance tenants |
| High memory | > 80% for 5min | Migrate heavy tenant |
| Slow responses | p95 > 5s | Investigate, possibly scale |
| Instance down | Health check fails | Migrate all tenants, terminate |

### Scaling Scenarios

| Users | Instances | Warm Pool | Monthly Cost | Notes |
|-------|-----------|-----------|--------------|-------|
| 1-8 | 1 | 1 | ~$32 | 1 active + 1 warm |
| 9-16 | 2 | 1 | ~$48 | 2 active + 1 warm |
| 17-24 | 3 | 1 | ~$64 | 3 active + 1 warm |
| 25-50 | 5-6 | 2 | ~$128 | Scale up warm pool |
| 50-100 | 10-12 | 2 | ~$224 | Consider larger instances |
| 100+ | Auto-scaling | 3-5 | Variable | K8s or Nomad |

### Pros

- **Good utilization:** 10x cheaper than 1:1 cloud
- **Fast provisioning:** Seconds (container start) vs minutes (VM boot)
- **Better isolation than shared:** Container boundaries, separate OpenClaw processes
- **Simple scaling:** Add instances as you add users
- **Shared model cache:** Efficient GPU/CPU usage
- **Auto-scaling:** Never overloaded, always ready capacity

### Cons

- **Noisy neighbor:** One heavy user affects 9 others (mitigated by thresholds)
- **Blast radius:** Instance crash = 10 users down (mitigated by health checks + migration)
- **Warm pool cost:** $32/month overhead even at low user count
- **Not true isolation:** Same kernel, shared resources

### Comparison

| Approach | Per-User Cost | Isolation | Complexity | Best For |
|----------|---------------|-----------|------------|----------|
| Shared LLM cluster | $2-5 | Low | High | Power users |
| Cloud-per-user | $6-9 | High | Medium | Privacy-focused |
| **Cloud-per-10 + auto-scale** | **$1.60-3.50** | **Medium** | **Medium** | **MVP/Scale** |
| API-only | $0.50-2 | N/A | Low | Validation |

## Verdict

**Cloud-per-10 with auto-scaling is the architecture.**

- 75-85% margins at $10-15/month pricing
- Never overloaded (provision at 8/10 tenants)
- Warm pool = instant signups
- Auto-rebalance when instances get hot
- Can migrate heavy users to dedicated instances later

**Next step:** Build a single CPX31 prototype with auto-scaler logic.

# ExecFlow Infrastructure — Railway-Based

## Overview

Each user gets their own Railway project with OpenClaw + Ollama running in containers.

## Why Railway?

| Feature | Benefit |
|---------|---------|
| **Per-project isolation** | Each user = separate Railway project |
| **Easy provisioning** | API to create projects, deploy services |
| **Built-in domains** | `user-agent.up.railway.app` |
| **Auto-scaling** | CPU/memory scales with usage |
| **Simple pricing** | Pay for usage, no server management |
| **GitHub integration** | Deploy from repo |

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    EXECFLOW CONTROL                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   Web App   │  │  API/Auth   │  │   Provisioning  │  │
│  │  (Next.js)  │  │  (FastAPI)  │  │   (Railway API) │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
│         │                  │                  │          │
│         └──────────────────┴──────────────────┘          │
│                            │                             │
│                     ┌─────────────┐                      │
│                     │  PostgreSQL │                      │
│                     │    Redis    │                      │
│                     └─────────────┘                      │
└─────────────────────────────────────────────────────────┘
                            │
                            │ Railway API
                            ↓
┌─────────────────────────────────────────────────────────┐
│              USER RAILWAY PROJECT (Per User)             │
│                                                          │
│  ┌─────────────────┐    ┌─────────────────┐             │
│  │   OpenClaw      │◄──►│     Ollama      │             │
│  │   Service       │    │    Service      │             │
│  │                 │    │  (Qwen2.5-7B)   │             │
│  │  - Telegram bot │    │                 │             │
│  │  - WhatsApp bot │    │  GPU/CPU infer  │             │
│  │  - Web API      │    │                 │             │
│  └─────────────────┘    └─────────────────┘             │
│           │                      │                       │
│           └──────────────────────┘                       │
│                     │                                    │
│              ┌─────────────┐                             │
│              │  Volume     │                             │
│              │  (workspace)│                             │
│              └─────────────┘                             │
│                                                          │
│  URL: chris-agent.up.railway.app                        │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Railway Pricing (Per User)

| Tier | Resources | Railway Cost | ExecFlow Price | Margin |
|------|-----------|--------------|----------------|--------|
| **Starter** | 2 vCPU, 4GB RAM | ~$10/mo | $25/mo | 60% |
| **Pro** | 4 vCPU, 8GB RAM | ~$20/mo | $45/mo | 55% |
| **Power** | 8 vCPU, 16GB RAM | ~$40/mo | $90/mo | 55% |

## Provisioning Flow

```python
async def provision_user_railway(user_id, tier):
    # Create Railway project
    project = await railway.create_project(
        name=f"execflow-{user_id}",
        description=f"ExecFlow agent for {user_id}"
    )
    
    # Deploy Ollama service
    ollama = await railway.create_service(
        project_id=project.id,
        name="ollama",
        image="ollama/ollama:latest",
        env={"MODEL": tier_model[tier]}
    )
    
    # Deploy OpenClaw service
    openclaw = await railway.create_service(
        project_id=project.id,
        name="openclaw",
        image="execflow/openclaw:latest",
        env={
            "TENANT_ID": user_id,
            "OLLAMA_URL": f"http://{ollama.internal_url}:11434",
            "TELEGRAM_TOKEN": encrypt(bot_token)
        }
    )
    
    # Create volume for persistence
    volume = await railway.create_volume(
        project_id=project.id,
        name="workspace",
        size_gb=tier_storage[tier]
    )
    
    # Generate domain
    domain = f"{user_id}-agent.up.railway.app"
    
    return {
        "project_id": project.id,
        "domain": domain,
        "status": "deploying"
    }
```

## Services Per Project

### 1. Ollama Service
```dockerfile
FROM ollama/ollama
RUN ollama pull qwen2.5:7b
EXPOSE 11434
```

### 2. OpenClaw Service
```dockerfile
FROM execflow/openclaw:latest
COPY config.yaml /workspace/
ENV TENANT_ID=${RAILWAY_ENVIRONMENT}
ENV OLLAMA_URL=${OLLAMA_INTERNAL_URL}
EXPOSE 8080
```

### 3. Volume
- Mounts to `/workspace`
- Stores user data, memory, files
- Persistent across deploys

## Benefits Over Hetzner

| Aspect | Hetzner | Railway |
|--------|---------|---------|
| **Provisioning** | 2-3 minutes | 30-60 seconds |
| **Management** | SSH + scripts | API + UI |
| **Scaling** | Manual | Auto |
| **Downtime** | Reboot for updates | Zero-downtime deploys |
| **Monitoring** | Self-hosted | Built-in |
| **SSL** | Manual (Certbot) | Automatic |
| **Cost** | Fixed | Usage-based |

## Downsides

- Higher cost at scale ($10 vs $6 for Starter)
- Vendor lock-in (Railway-specific)
- Less control over infrastructure

## Hybrid Option

**Start with Railway** (fast, easy)
**Migrate to Hetzner** when:
- 100+ users (volume discounts matter)
- Need GPU support (Railway limited)
- Want multi-region

## Implementation

```yaml
# railway.yaml
services:
  ollama:
    image: ollama/ollama
    env:
      MODEL: qwen2.5:7b
    
  openclaw:
    image: execflow/openclaw
    env:
      TENANT_ID: ${RAILWAY_ENVIRONMENT}
      OLLAMA_URL: http://ollama.railway.internal:11434
    volumes:
      - workspace:/workspace
```

## Summary

Railway is **faster to build, easier to manage** but **more expensive per user**. Perfect for MVP and first 100 users.

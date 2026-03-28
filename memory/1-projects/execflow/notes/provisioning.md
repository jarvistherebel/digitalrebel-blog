# Customer Provisioning Flow

## Overview

User signs up → Payment confirmed → Instance provisioned → Agent ready in < 2 minutes

## Step-by-Step Flow

### 1. User Signs Up

```
Landing page (execflow.io)
    ↓
"Get Your AI Agent" → Email + Password
    ↓
Choose tier: Starter ($25) / Pro ($45) / Power ($90)
    ↓
Stripe checkout
    ↓
Payment confirmed webhook → Provisioning API
```

### 2. Provisioning Service

```python
# Pseudo-code
async def provision_customer(user_id, tier):
    # Select instance type based on tier
    instance_type = {
        "starter": "cpx31",  # 4 vCPU, 8GB
        "pro": "cpx41",      # 8 vCPU, 16GB  
        "power": "cpx51"     # 16 vCPU, 32GB
    }[tier]
    
    # Create cloud instance (Hetzner)
    instance = await hetzner.create_server(
        name=f"execflow-{user_id}",
        server_type=instance_type,
        image="ubuntu-22.04",
        location="nbg1"  # Nuremberg
    )
    
    # Wait for instance ready (30-60 seconds)
    await wait_for_ssh(instance.ip)
    
    # Run provisioning script
    await instance.run_provisioning_script(tier, user_id)
    
    return instance
```

### 3. Instance Setup Script

```bash
#!/bin/bash
# Runs on fresh Ubuntu instance

USER_ID=$1
TIER=$2

# Update system
apt update && apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com | sh
usermod -aG docker ubuntu

# Install Ollama (LLM runtime)
curl -fsSL https://ollama.com/install.sh | sh

# Pull Qwen model based on tier
if [ "$TIER" = "starter" ]; then
    ollama pull qwen2.5:7b
elif [ "$TIER" = "pro" ]; then
    ollama pull qwen2.5:14b
else
    ollama pull qwen2.5:32b
fi

# Create workspace directory
mkdir -p /workspaces/$USER_ID

# Install OpenClaw (latest release)
wget https://github.com/openclaw/openclaw/releases/latest/download/openclaw-linux-amd64 -O /usr/local/bin/openclaw
chmod +x /usr/local/bin/openclaw

# Create OpenClaw config
cat > /workspaces/$USER_ID/config.yaml <<EOF
tenant_id: $USER_ID
tier: $TIER
llm:
  provider: ollama
  model: qwen2.5:7b
  url: http://localhost:11434
workspace: /workspaces/$USER_ID
allowed_tools:
  - message
  - read
  - write
  - exec
  - web_search
EOF

# Start services
systemctl enable docker
systemctl start docker

# Run Ollama in background
ollama serve &

# Run OpenClaw in Docker
docker run -d \
    --name openclaw-$USER_ID \
    -v /workspaces/$USER_ID:/workspace \
    -p 8080:8080 \
    -e TENANT_ID=$USER_ID \
    openclaw/agent:latest

# Setup complete
echo "Provisioning complete for $USER_ID"
```

### 4. Telegram Bot Setup

```python
async def setup_telegram_bot(user_id, instance_ip):
    # Create bot via BotFather API
    bot_token = await telegram.create_bot(
        name=f"ExecFlow_{user_id[:8]}",
        username=f"execflow_{user_id[:8]}_bot"
    )
    
    # Configure webhook to instance
    webhook_url = f"https://{user_id}.execflow.io/telegram"
    await telegram.set_webhook(bot_token, webhook_url)
    
    # Store in instance config
    await instance.update_config({
        "telegram_bot_token": encrypt(bot_token),
        "telegram_webhook": webhook_url
    })
    
    return bot_token
```

### 5. DNS & Routing

```python
async def setup_routing(user_id, instance_ip):
    # Add DNS record
    await cloudflare.add_record(
        name=f"{user_id}.execflow.io",
        type="A",
        value=instance_ip
    )
    
    # Update reverse proxy
    await proxy.add_route(
        domain=f"{user_id}.execflow.io",
        target=f"{instance_ip}:8080"
    )
    
    # Issue SSL certificate
    await certbot.issue(f"{user_id}.execflow.io")
```

### 6. Welcome Flow

```
Instance ready
    ↓
Send email: "Your agent is live!"
    ↓
Include:
- Telegram bot username (@execflow_xxx_bot)
- Web dashboard link (xxx.execflow.io)
- Quick start guide
- First message suggestion: "Hello! What can you do?"
    ↓
First Telegram message sent from agent:
"Hi! I'm your ExecFlow agent. I live on your own server 
(4 vCPU, 8GB RAM). I can read files, run commands, search 
the web, and more. What would you like to work on?"
```

## Timing Breakdown

| Step | Time |
|------|------|
| Payment webhook | < 1s |
| Create instance | 30-60s |
| Run setup script | 60-90s |
| Pull Qwen model | 30-60s (cached after first) |
| Setup Telegram | 5-10s |
| DNS + SSL | 10-20s |
| **Total** | **~2-3 minutes** |

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    EXECFLOW CONTROL PLANE                │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   Web UI    │  │  API/Auth   │  │  Provisioning   │  │
│  │  (Next.js)  │  │  (FastAPI)  │  │   Service       │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
│         │                  │                  │          │
│         └──────────────────┴──────────────────┘          │
│                            │                             │
│                     ┌─────────────┐                      │
│                     │   PostgreSQL │                      │
│                     │   Redis      │                      │
│                     └─────────────┘                      │
└─────────────────────────────────────────────────────────┘
                            │
                            │ Hetzner API
                            ↓
┌─────────────────────────────────────────────────────────┐
│              CUSTOMER INSTANCE (Hetzner)                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   OpenClaw  │  │   Ollama    │  │   Telegram Bot  │  │
│  │   Container │  │   (Qwen)    │  │   Webhook       │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
│         │                  │                  │          │
│         └──────────────────┴──────────────────┘          │
│                            │                             │
│                     ┌─────────────┐                      │
│                     │  /workspace  │                      │
│                     │  (user data) │                      │
│                     └─────────────┘                      │
└─────────────────────────────────────────────────────────┘
```

## Failure Handling

| Failure | Detection | Recovery |
|---------|-----------|----------|
| Instance stuck | Timeout (5min) | Terminate, retry, alert |
| Setup script fail | Exit code ≠ 0 | Log, notify, manual fix |
| Model pull fail | Ollama timeout | Retry, fallback to API |
| Telegram fail | HTTP error | Queue, retry, alert user |
| DNS fail | Propagation timeout | Use IP directly, retry |

## Monitoring

```python
# Health check every 30s
async def health_check(instance):
    checks = {
        "instance": ping(instance.ip),
        "openclaw": http_get(f"{instance.ip}:8080/health"),
        "ollama": http_get(f"{instance.ip}:11434/api/tags"),
        "telegram": check_webhook(instance.bot_token)
    }
    
    if not all(checks.values()):
        await alert_ops(instance, checks)
        await attempt_recovery(instance)
```

## Scaling Considerations

| Users | Instances | Strategy |
|-------|-----------|----------|
| 1-10 | 10 separate | One per user |
| 10-50 | 5x CPX41 (10 tenants each) | Cloud-per-10 |
| 50-200 | 20x CPX41 + load balancer | Auto-scaling group |
| 200+ | Kubernetes cluster | Managed K8s |

## Summary

| Metric | Target |
|--------|--------|
| Time to first message | < 3 minutes |
| Success rate | > 95% |
| Failed provision auto-retry | Yes |
| Manual intervention needed | < 5% |
| User notification on failure | Immediate |

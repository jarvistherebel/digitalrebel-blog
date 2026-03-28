# ExecFlow — AI Agent Platform

**Goal:** B2C SaaS platform — users pay, we provision their OpenClaw instance, they chat via Telegram/WhatsApp, manage via web app

**Deadline:** TBD — need MVP scope

**Status:** 🟡 Architecture planning

## Business Model

- **Pricing:** Subscription tiers (free trial → paid plans)
- **Provisioning:** One OpenClaw instance per customer (isolated)
- **Channels:** Telegram, WhatsApp, Web UI
- **Target:** General public (B2C) — non-technical users who want AI assistance

## Architecture Overview

```
User → Telegram/WhatsApp → ExecFlow API → Customer's OpenClaw Instance
                          ↓
                    Web Dashboard (manage agents, view history, billing)
```

## Key Components

1. **Provisioning Service** — spin up isolated OpenClaw instances
2. **Message Router** — Telegram/WhatsApp → OpenClaw → User
3. **Web Dashboard** — React/Vue frontend for management
4. **Billing System** — Stripe integration, usage tracking
5. **Admin Panel** — our view of all instances, health, support

## Success Criteria

- [ ] User signs up, pays, gets working OpenClaw in < 2 minutes
- [ ] Chat experience feels native (fast, reliable)
- [ ] Web dashboard shows conversations, agents, settings
- [ ] Auto-scaling / cost-efficient per-user instances
- [ ] Support for 1000+ concurrent users

## Open Questions

1. Container per user? VM per user? Shared multi-tenant?
2. How to handle WhatsApp Business API costs?
3. Custom domain support? (user.execflow.io)
4. Agent marketplace or just core OpenClaw?

## Domain Strategy (NEW)

| Domain | Purpose | Target |
|--------|---------|--------|
| **execflow.io** | Main brand, paid tiers, dedicated instances | Power users, professionals |
| **execflow.co** | Free tier, shared resources, limited features | Casual users, trial, students |

### Why Two Domains?

**execflow.io (The Premium Experience)**
- Dedicated container (cloud-per-10 architecture)
- Full tool access (file, exec, shell)
- Priority inference (faster responses)
- Custom Telegram/WhatsApp bot
- Web dashboard with full history
- Pricing: $10-20/month

**execflow.co (The Free Tier)**
- Shared multi-tenant instance (100+ users per server)
- Limited tools (no exec/shell for safety)
- Rate limited (10 messages/hour free, 100/hour paid)
- Slower responses (queued inference)
- Community Telegram bot (shared)
- Basic web dashboard
- Pricing: Free, or $3/month for "co Plus" (higher limits)

### Benefits of Split

1. **Clear positioning:** `.io` = professional, `.co` = casual
2. **Risk isolation:** Free tier abuse doesn't hurt paid users
3. **Upsell funnel:** Free → co Plus → io Pro
4. **SEO:** Two properties, different keywords
5. **Community:** `.co` builds buzz, `.io` monetizes

### User Journey

```
Discovers ExecFlow
       ↓
   execflow.co (free)
       ↓
   Hits rate limit / wants more
       ↓
   Upgrade to co Plus ($3/month)
       ↓
   Wants dedicated instance / full tools
       ↓
   Migrate to execflow.io ($15/month)
```

### Technical Split

| Aspect | execflow.co (Free) | execflow.io (Paid) |
|--------|-------------------|-------------------|
| **Tenancy** | Shared (100+/instance) | Cloud-per-10 |
| **Tools** | message, read only | Full toolset |
| **LLM** | Shared Qwen2.5-7B | Dedicated inference |
| **Speed** | 2-5 sec response | < 1 sec response |
| **Storage** | 100MB | 10GB |
| **History** | 7 days | Unlimited |
| **Custom bot** | No (shared) | Yes |
| **Web dashboard** | Basic | Full |

### Brand Positioning

**execflow.io:**
- Tagline: "Your personal AI assistant infrastructure"
- Audience: Developers, founders, professionals
- Vibe: Serious, powerful, yours

**execflow.co:**
- Tagline: "AI that fits in your pocket"
- Audience: Students, casual users, curious
- Vibe: Friendly, accessible, free

### Competitor Mapping

| Competitor | Our Equivalent |
|------------|---------------|
| ezclaw.io (dedicated) | **execflow.io** |
| ezclaw.co (free tier) | **execflow.co** |
| OpenClaw self-hosted | execflow.io (managed) |
| ChatGPT free | execflow.co (free) |
| ChatGPT Plus | execflow.co Plus / execflow.io |


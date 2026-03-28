# ExecFlow Project Context

**Last Updated:** 2026-03-07

## What We're Building
ExecFlow — a virtual assistant platform where users get their own dedicated AI agent on a private server. Think "Claude Code + OpenClaw + Ollama as a service."

## Current Architecture

### Infrastructure
- **Frontend:** Next.js on Railway (https://web-production-6b45d.up.railway.app)
- **API:** FastAPI on Railway (https://api-production-b3dc.up.railway.app)
- **Database:** Supabase PostgreSQL (migrated from SQLite)
- **Provisioning:** Hetzner CPX31 instances (4vCPU/8GB → 16vCPU/32GB)
- **AI:** Local Ollama (Qwen2.5-7B/14B/32B) + optional OpenAI/Claude API

### Pricing Model (Compute Tiers)
| Tier | Price | Specs | Model |
|------|-------|-------|-------|
| Starter | $25/mo | 4vCPU/8GB/80GB | Qwen2.5-7B |
| Pro | $45/mo | 8vCPU/16GB/160GB | Qwen2.5-14B |
| Power | $90/mo | 16vCPU/32GB/320GB | Qwen2.5-32B |

**Upsells:**
- GPT-4o upgrade: +$20/mo
- Claude 3.5 upgrade: +$25/mo
- Extra storage: $3/10GB/mo
- Custom domain: +$5/mo

**Key decision:** Task-based pricing rejected in favor of compute tiers. Aligns with Hetzner costs, predictable for users.

### Key Messaging
- "Chatbots suggest. ExecFlow does."
- Dedicated server per user (no shared resources)
- EU & US data centers (GDPR compliant)
- Local LLM included, API upgrades optional

### Recent Changes (2026-03-07)
1. **Landing page:** Added comparison table vs ChatGPT/Claude
2. **Pricing page:** Switched from task limits to compute tiers (RAM/vCPU)
3. **Removed Hetzner branding:** Generic "dedicated server" + "EU & US data centers"
4. **Database:** Migrated to Supabase with Hetzner fields
5. **Dashboard:** Fixed TypeScript errors (railway_project_id, google_docs_token)

### Decisions Made
- ❌ No BYO credentials (OAuth/bots) — too complex for MVP
- ✅ BYO OpenAI key — considered but deferred
- ✅ Compute tiers over task-based pricing
- ✅ Hetzner over Railway for provisioning (cost)
- ✅ Supabase for auth + database

### Open Questions
- When to add US data center option (Hetzner has Virginia)
- Whether to offer BYO OpenAI key later
- Skills marketplace timeline

### File Locations
- Frontend: `/execflow/web/`
- API: `/execflow/api/`
- Provisioning scripts: `/execflow/api/hetzner_provisioner.py`
- Database migrations: `/execflow/api/alembic/versions/`

### Live URLs
- Landing: https://web-production-6b45d.up.railway.app
- API: https://api-production-b3dc.up.railway.app
- Railway Dashboard: https://railway.app/project/fbec98f5-e292-420f-8e89-2eb753e1cc51

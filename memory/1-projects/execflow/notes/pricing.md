# ExecFlow Pricing — Clean Tiers (8GB / 16GB / 32GB)

## Hardware Tiers

| Tier | Specs | Cost | Price | Margin |
|------|-------|------|-------|--------|
| **Starter** | 4 vCPU, **8GB RAM**, 80GB NVMe | ~$16 | **$25/mo** | 36% |
| **Pro** | 8 vCPU, **16GB RAM**, 160GB NVMe | ~$29 | **$45/mo** | 36% |
| **Power** | 16 vCPU, **32GB RAM**, 320GB NVMe | ~$60 | **$90/mo** | 33% |

## Local LLM Fit

| Tier | RAM | Local LLM | Speed |
|------|-----|-----------|-------|
| **Starter (8GB)** | ✅ Fits | Qwen2.5-7B Q4 | 5-10 t/s |
| **Pro (16GB)** | ✅ Fits | Qwen2.5-14B Q4 or 7B Q8 | 3-10 t/s |
| **Power (32GB)** | ✅ Fits | Qwen2.5-32B Q4 or multi-model | 2-10 t/s |

## Comparison to EzClaw

| | EzClaw | ExecFlow | Savings |
|---|---|---|---|
| Entry | $69 (4vCPU/8GB) | **$25** (4vCPU/8GB) | **64%** |
| Mid | $79 (8vCPU/16GB) | **$45** (8vCPU/16GB) | **43%** |
| High | $99 (16vCPU/32GB) | **$90** (16vCPU/32GB) | **9%** |

## What's Included (All Tiers)

- Dedicated container (no sharing)
- Local Qwen2.5 LLM (size matches tier)
- Telegram + WhatsApp bot
- Web dashboard
- File system access
- Basic tools (message, read, exec)
- 10GB-40GB-80GB storage

## Upsells (Revenue)

| Upsell | Price | Target Tier |
|--------|-------|-------------|
| **Skills** | $5-50 one-time | All |
| **Model upgrade** (GPT-4o/Claude) | +$20-25/mo | Pro+ |
| **Priority inference** | +$10/mo | Starter+ |
| **Custom domain** | +$5/mo | All |
| **API access** | +$10/mo | Pro+ |
| **Extra storage** | $3/10GB/mo | All |
| **Extra messages** | $2/1000 | All |

## Example Revenue per User

### Starter User
- Base: $25/mo
- + 2 skills ($15)
- + Custom domain ($5)
- **Month 1: $45** | **Ongoing: $30/mo**

### Pro User
- Base: $45/mo
- + 4 skills ($40)
- + GPT-4o upgrade ($20)
- + API access ($10)
- **Month 1: $115** | **Ongoing: $75/mo**

### Power User
- Base: $90/mo
- + 6 skills ($80)
- + Claude 3.5 ($25)
- + Priority + API ($20)
- + Extra storage ($9)
- **Month 1: $224** | **Ongoing: $144/mo**

## Free Tier?

**Option A:** No free tier — $5/mo minimum (shared instance, limited)
**Option B:** Free tier with strict limits — 50 msgs/mo, 7-day history, no local LLM (API only)

Recommendation: **Option B** — funnel to Starter

## Summary

| | Amount |
|---|---|
| Cheaper than EzClaw | 9-64% |
| Base margin | 33-36% |
| With avg upsells | 50-60% blended |
| Local LLM | All tiers |
| Clear upgrade path | 8GB → 16GB → 32GB |

Clean. Simple. Undercuts EzClaw. Makes money on skills.

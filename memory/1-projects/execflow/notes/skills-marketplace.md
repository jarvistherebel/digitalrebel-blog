# Skills Marketplace — Upsell Strategy

## Model

Base OpenClaw = core tools only (message, read, basic exec)

**Skills = installable capabilities** — pay once or subscribe monthly

## Skill Categories

### Productivity ($5-15 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **Calendar** | $5 | Google/Outlook sync, scheduling, reminders |
| **Email** | $5 | Send/receive, draft, summarize inbox |
| **Tasks** | $3 | Todoist/Notion sync, task management |
| **Notes** | $3 | Obsidian/Notion integration |
| **Meeting** | $10 | Zoom/Meet join, transcribe, summarize |

### Communication ($5-20 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **Slack** | $5 | Send messages, read channels, alerts |
| **Discord** | $5 | Bot integration, channel management |
| **Twitter/X** | $10 | Post, reply, DM, analytics |
| **LinkedIn** | $15 | Post, connect, message, job search |
| **WhatsApp Business** | $10 | Beyond basic — business features |

### Research ($10-25 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **Web Search** | $10 | Google, Bing, Perplexity integration |
| **News** | $5 | RSS, newsletters, daily briefing |
| **Research** | $20 | Deep research, PDF analysis, citations |
| **Stocks** | $10 | Market data, alerts, portfolio tracking |
| **Crypto** | $10 | Prices, wallets, DeFi monitoring |

### Development ($15-30 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **GitHub** | $15 | PRs, issues, commits, code review |
| **Code Exec** | $15 | Sandboxed code execution (Python, JS) |
| **Deploy** | $20 | Vercel, Railway, AWS deploys |
| **Testing** | $15 | Run tests, report bugs, screenshots |
| **Database** | $10 | Query SQL, manage data |

### Creative ($10-25 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **Image Gen** | $15 | DALL-E, Midjourney, Stable Diffusion |
| **Video** | $25 | Editing, transcription, clips |
| **Audio** | $10 | TTS, transcription, podcast tools |
| **Design** | $15 | Figma, Canva integration |
| **Writing** | $10 | Long-form, SEO, copywriting |

### Business ($20-50 one-time)

| Skill | Price | What It Does |
|-------|-------|--------------|
| **CRM** | $25 | HubSpot, Salesforce integration |
| **Invoices** | $15 | Create, send, track payments |
| **Analytics** | $20 | GA, Mixpanel, custom dashboards |
| **Support** | $30 | Zendesk, Intercom, ticket management |
| **Sales** | $40 | Outreach sequences, follow-ups, CRM sync |

## Pricing Models

### One-Time Purchase (Default)
- Buy once, own forever
- Updates included for 1 year
- Pay again for major version (v2)

### Subscription Skills (Premium)
| Skill | Monthly | Why Subscription |
|-------|---------|------------------|
| **Research Pro** | $10/mo | API costs (Perplexity, etc.) |
| **Image Gen** | $5/mo | GPU compute costs |
| **Phone/SMS** | $0.05/msg | Telecom costs |
| **Email Send** | $0.01/email | SMTP costs |
| **Stock Data** | $5/mo | Market data fees |

### Bundles (Value)

| Bundle | Skills | Price | Savings |
|--------|--------|-------|---------|
| **Starter Pack** | Calendar + Email + Tasks | $10 | $3 |
| **Creator Pack** | Twitter + Image Gen + Writing | $35 | $10 |
| **Dev Pack** | GitHub + Code Exec + Deploy | $45 | $10 |
| **Business Pack** | CRM + Invoices + Analytics | $55 | $15 |
| **All Access** | Everything | $199 | ~$100 |

## Skill Store Economics

### Revenue Split

| Party | Cut | Notes |
|-------|-----|-------|
| **Skill Developer** | 70% | Creator of the skill |
| **ExecFlow** | 25% | Platform fee |
| **Payment** | 5% | Stripe/processing |

### Example: $15 Skill Sale

| Party | Amount |
|-------|--------|
| Developer | $10.50 |
| ExecFlow | $3.75 |
| Stripe | $0.75 |

### Projected Volume

| Metric | Month 6 | Month 12 |
|--------|---------|----------|
| Paid users | 500 | 2,000 |
| Skills per user | 3 | 5 |
| Avg skill price | $12 | $12 |
| Skill revenue | $18,000 | $120,000 |
| ExecFlow cut (25%) | $4,500 | $30,000 |

## Skill Development Program

### For External Developers

- **Docs:** Full skill development guide
- **SDK:** TypeScript/Python skill framework
- **Review:** Submission + approval process
- **Revenue:** 70% of sales
- **Marketing:** Featured skills, categories, search

### First-Party Skills

ExecFlow builds and maintains:
- Core integrations (Calendar, Email, Slack)
- High-value business skills (CRM, Sales)
- Complex multi-step skills (Research, Code Exec)

**Margin:** 100% (no rev share)

## Discovery & Marketing

### In-App Store

```
[Skills] tab in web dashboard
├── Categories (Productivity, Dev, Business...)
├── Featured (curated, new, trending)
├── Search
├── My Skills (installed, updates available)
└── Recommendations (based on usage)
```

### Promotions

| Type | Example |
|------|---------|
| **Launch discount** | 50% off new skills (first week) |
| **Bundle deals** | "Buy 3, get 1 free" |
| **Seasonal** | "Tax Season Pack" — Invoices + Calendar + Email |
| **Usage-based** | "You use Email a lot — try Calendar?" |

## User Journey with Skills

### New User

```
Sign up → Free tier
    ↓
"Your agent can do more! Browse skills"
    ↓
Installs 1-2 free skills (basic Calendar, Tasks)
    ↓
Hits free tier limit
    ↓
Upgrades to Starter ($5/mo)
    ↓
Buys Email skill ($5)
    ↓
"Want to schedule meetings? Get Calendar Pro"
    ↓
Upgrades Calendar → Calendar Pro ($3)
    ↓
Month 1 total: $5 + $5 + $3 = $13
```

### Power User

```
Pro tier ($25/mo)
    ↓
Installs: GitHub ($15) + Code Exec ($15) + Deploy ($20)
    ↓
"Your agent deployed 10 times this week"
    ↓
Subscribes to Deploy Pro ($10/mo) — unlimited deploys
    ↓
Installs: Research ($20) + Image Gen ($5/mo)
    ↓
Month 6 total: $25 + $50 (skills) + $15 (subs) = $90/mo
```

## Summary

| Revenue Source | % of Total | Margin |
|----------------|-----------|--------|
| Base subscriptions | 30% | 35% |
| Skill sales (first-party) | 25% | 100% |
| Skill sales (marketplace cut) | 15% | 25% |
| Skill subscriptions | 15% | 60% |
| Usage overages | 10% | 80% |
| Premium features | 5% | 90% |

**Skills become the primary revenue driver** after user acquisition.

# Nova System Configuration - March 7, 2026

## Core Identity
- **Name:** Nova
- **Model:** Kimi K2.5 (moonshot/kimi-k2.5)
- **Role:** AI Assistant for Chris Hall
- **Workspace:** /home/digitalrebel/.openclaw/workspace

## Sub-Agent Team

### 1. Coder
- **Runtime:** ACP (Claude Code)
- **Purpose:** Code writing, debugging, refactoring
- **Location:** ~/.openclaw/workspace/subagents/coder/

### 2. Researcher  
- **Runtime:** Standard
- **Purpose:** Deep research, fact-checking, summarization
- **Location:** ~/.openclaw/workspace/subagents/researcher/

### 3. Planner
- **Runtime:** Standard
- **Purpose:** Project planning, breaking down complex tasks
- **Location:** ~/.openclaw/workspace/subagents/planner/

### 4. Reviewer
- **Runtime:** Standard
- **Purpose:** Code review, document review, quality checks
- **Location:** ~/.openclaw/workspace/subagents/reviewer/

### 5. Emma (Email Writer) ⭐ NEW
- **Runtime:** Standard (Kimi K2.5)
- **Purpose:** Cold email copywriting
- **Location:** ~/.openclaw/workspace/subagents/emma/
- **Frameworks:** POCS, SNAP, BREAK
- **Skills:** GWorkspace (Google Sheets)
- **Activation:** "Spawn Emma to write emails for rows X-Y in sheet Z"

## Custom Skills

### GWorkspace
- **Location:** ~/.openclaw/skills/gworkspace/
- **Purpose:** Google Workspace integration (Gmail, Sheets, Docs, Calendar)
- **Token:** ~/.config/gogcli/keyring/jarvistherebel@gmail.com.json
- **Scripts:**
  - sheets-append.sh
  - gmail-send.sh
  - token-refresh.sh

## Active Projects

### 1. Apex AI Cold Email Campaign ✅ COMPLETE
- **Leads:** 2,419 (rows 2-2,420)
- **Sheet:** 11PucFrod0WaMCMjQ7deADcmcTDF9Sjyl5yEYT1MNkXE
- **Output:** Columns AT-AY (6 email fields)
- **Sign-off:** Ash
- **Frameworks:** POCS, SNAP, BREAK
- **Memory:** ~/.openclaw/workspace/memory/1-projects/apex-ai-cold-email-campaign.md

### 2. ExecFlo Platform ✅ COMPLETE
- **Website:** https://web-production-6b45d.up.railway.app
- **API:** https://api-production-b3dc.up.railway.app
- **Features:** Stripe, Hetzner, Telegram/WhatsApp/Slack, Google OAuth
- **Memory:** ~/.openclaw/workspace/memory/2026-03-06.md

## Configuration Files

### Models
- **Primary:** moonshot/kimi-k2.5
- **Fallback:** openai/gpt-5-mini

### Default Model Settings
- Location: ~/.openclaw/openclaw.json
- Primary: moonshot/kimi-k2.5
- Fallbacks: [openai/gpt-5-mini]

## Key Files
- **SOUL.md:** ~/.openclaw/workspace/SOUL.md
- **USER.md:** ~/.openclaw/workspace/USER.md
- **AGENTS.md:** ~/.openclaw/workspace/AGENTS.md
- **TOOLS.md:** ~/.openclaw/workspace/TOOLS.md
- **IDENTITY.md:** ~/.openclaw/workspace/IDENTITY.md

## Memory Structure
- **Daily:** ~/.openclaw/workspace/memory/daily/
- **Projects:** ~/.openclaw/workspace/memory/1-projects/
- **Areas:** ~/.openclaw/workspace/memory/2-areas/
- **Resources:** ~/.openclaw/workspace/memory/3-resources/
- **Archive:** ~/.openclaw/workspace/memory/4-archive/

## Date
March 7, 2026

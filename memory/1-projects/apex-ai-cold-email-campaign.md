# Apex AI Cold Email Campaign - March 7, 2026

## Project Summary
Generated 3-email cold outreach sequences for 2,420 clinic leads using proven frameworks.

## Frameworks Created

### 1. POCS (Pain, Outcome, Credibility, Soft CTA) - Email 1
- Subject: [Specific result] + [timeframe]
- Opening: One sentence pain point (company-type specific)
- Bridge: 2-3 sentences with relevant proof point
- Credibility: One quote or metric
- CTA: Soft question
- Max: 109 words

### 2. SNAP (Shift, New proof, Assumption, Ping) - Email 2  
- Opening: New angle, no reference to Email 1
- Proof: Different metric/clinic, one number
- Assumption: State their cost, don't ask
- CTA: Low friction ping
- Max: 100 words

### 3. BREAK (Brevity, Reframe, Exit, Ask, Keep door open) - Email 3
- Reframe: Provocative question about their business
- Micro proof: Under 15 words
- Exit signal: Easy out, creates urgency
- Ask: Yes/no about receiving info
- Max: 80 words

## Company-Type Proof Points

| Type | Email 1 | Email 2 | Email 3 |
|------|---------|---------|---------|
| Physio | 26 appointments in 2 days | 40 calls/day handled | 74 bookings/month |
| Dental (general) | 18 new patient registrations | 15-20 enquiries/week | 41 new patients/month |
| Dental (implants) | 21 implant consultations | 12-16 high-value/week | 47 implants/month |
| Optician | 22 eye test bookings | 12-15 bookings/week | 47 eye tests/month |
| Chiropractic | 15 new consultations | 10-12 enquiries/week | 36 new patients/month |
| Therapy | 15 new client consultations | 8-10 enquiries/week | 28 new clients/month |
| Aesthetics | 12 new consultations | 8-10 bookings/week | 31 new clients/month |
| Solo practitioner | 8 new bookings | 5-8 enquiries/week | 31 bookings/month |
| Medical supplier | 16 new wholesale accounts | 10-14 enquiries/week | 32 accounts/month |
| Accountant | 12 new clients onboarded | 8-10 enquiries/week | 23 new clients/month |
| Nonprofit | 20 donor calls converted | 10-15 donor enquiries/week | 27 qualified leads/month |
| Parent services | 30 parent enquiries handled | 10-15 parent enquiries/week | 56 connections/month |
| Healthcare provider | 23 patient referrals processed | 12-16 referral calls/week | 52 referrals/month |
| Private practice | 14 new private consultations | 8-12 high-value/week | 36 new patients/month |

## Google Sheet
- **Sheet ID:** 11PucFrod0WaMCMjQ7deADcmcTDF9Sjyl5yEYT1MNkXE
- **Columns:** AT-AY (Email 1-3 Subjects & Bodies)
- **Rows:** 2-2,420 (2,419 leads)
- **Sign-off:** Ash

## Sub-Agents Used
- 8 parallel Emma agents (Claude Code via ACP)
- Each processed 300 leads
- Frameworks stored in: `~/.openclaw/workspace/subagents/emma/SOUL.md`

## Key Rules
- NO em dashes
- NO jargon (transform, revolutionise, etc.)
- NO exclamation marks
- NO opening with "I"
- Company-type-specific proof points only
- Sign-off: "Best,\nAsh"

## Files
- Frameworks: `~/.openclaw/workspace/apex-ai/cold-email-frameworks/`
- Emma SOUL: `~/.openclaw/workspace/subagents/emma/SOUL.md`
- GWorkspace skill: `~/.openclaw/skills/gworkspace/`

## Date
March 7, 2026

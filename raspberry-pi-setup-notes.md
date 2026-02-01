# OpenClaw AI Assistant Setup Guide

A comprehensive guide for setting up a private, sandboxed AI assistant using OpenClaw, Kimi K2.5, and Raspberry Pi.

---

## Table of Contents

1. [Summary & Workflow Diagram](#summary--workflow-diagram)
2. [Hardware Setup: Raspberry Pi 4](#hardware-setup-raspberry-pi-4)
3. [Operating System Installation](#operating-system-installation)
4. [OpenClaw Overview](#openclaw-overview)
5. [System Architecture](#system-architecture)
6. [Security Analysis](#security-analysis)
7. [Telegram Setup & Privacy](#telegram-setup--privacy)
8. [Moonshot AI / Kimi K2.5 Registration](#moonshot-ai--kimi-k25-registration)
9. [Payment Privacy Options](#payment-privacy-options)
10. [Complete Privacy Stack](#complete-privacy-stack)
11. [Phased Rollout Plan](#phased-rollout-plan)

**Key Insight:** All OpenClaw accounts (Gmail, Calendar, Docs) are accessed exclusively from the Raspberry Pi. iPhone only has Telegram for messaging. Mac/personal devices stay completely clean.

---

## Summary & Workflow Diagram

### Project Overview

Build a **privacy-focused, 24/7 AI assistant** that:
- Runs on a Raspberry Pi (or UTM VM during testing)
- Uses Kimi K2.5 for cheap, capable AI inference ($0.60/1M tokens)
- Communicates via Telegram on your iPhone
- Has zero access to your real identity, accounts, or sensitive data

### Complete Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           YOUR REAL IDENTITY                                │
│                    (Completely separate - no access)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ Real Gmail  │  │ Real Phone  │  │   Bank/CC   │  │  Passwords  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
        ║ FIREWALL - No connection between real identity and OpenClaw
        ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        OPENCLAW'S SANDBOXED IDENTITY                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   PAYMENT                  ACCOUNTS                    COMMUNICATION        │
│   ┌──────────────┐        ┌──────────────┐            ┌──────────────┐     │
│   │   Prepaid    │        │  Dedicated   │            │   Telegram   │     │
│   │  Visa Gift   │───────▶│    Gmail     │◀───────────│    Bot       │     │
│   │    Card      │        │  (OpenClaw)  │            │  (@BotFather)│     │
│   │              │        └──────┬───────┘            └──────┬───────┘     │
│   │ Bought w/cash│               │                           │             │
│   │ Anonymous    │               │ OAuth                     │             │
│   └──────────────┘               ▼                           │             │
│         │              ┌──────────────────┐                  │             │
│         │              │   Moonshot AI    │                  │             │
│         └─────────────▶│   Platform       │                  │             │
│           Billing      │ (API Key)        │                  │             │
│                        └────────┬─────────┘                  │             │
│                                 │                            │             │
└─────────────────────────────────┼────────────────────────────┼─────────────┘
                                  │ API Calls                  │
                                  ▼                            │
┌─────────────────────────────────────────────────────────────────────────────┐
│                         INFRASTRUCTURE                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Phase 1-2: UTM VM on Mac          Phase 3+: Raspberry Pi 4               │
│   ┌─────────────────────────┐       ┌─────────────────────────┐            │
│   │  ┌───────────────────┐  │       │  ┌───────────────────┐  │            │
│   │  │     OpenClaw      │  │  ──▶  │  │     OpenClaw      │  │            │
│   │  │  ┌─────────────┐  │  │       │  │  ┌─────────────┐  │  │            │
│   │  │  │  Kimi K2.5  │  │  │       │  │  │  Kimi K2.5  │  │  │            │
│   │  │  │   (API)     │  │  │       │  │  │   (API)     │  │  │            │
│   │  │  └─────────────┘  │  │       │  │  └─────────────┘  │  │            │
│   │  └───────────────────┘  │       │  └───────────────────┘  │            │
│   │      Sandboxed VM       │       │    24/7 Dedicated HW    │            │
│   └─────────────────────────┘       └─────────────────────────┘            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                  ▲
                                  │ Your messages
                                  │
┌─────────────────────────────────────────────────────────────────────────────┐
│                              YOU (User)                                     │
│                        ┌──────────────────┐                                 │
│                        │     iPhone       │                                 │
│                        │  ┌────────────┐  │                                 │
│                        │  │  Telegram  │  │                                 │
│                        │  │    App     │  │                                 │
│                        │  └────────────┘  │                                 │
│                        └──────────────────┘                                 │
│                                                                             │
│   You send: "Research the best hiking trails in Colorado"                   │
│   OpenClaw: Searches web, summarizes, sends back via Telegram               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Device Access Strategy

```
┌─────────────────────────────────────┐
│  iPhone                             │
│  └── Telegram ONLY                  │
│      (messaging bridge to OpenClaw) │
│      No Gmail app, no calendar      │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Raspberry Pi                       │
│  ├── OpenClaw agent (24/7)          │
│  ├── Dedicated Gmail (browser)      │
│  ├── Dedicated Google Calendar      │
│  ├── Dedicated Google Docs          │
│  └── Full OpenClaw ecosystem        │
│                                     │
│  Access via:                        │
│  - Direct monitor/keyboard          │
│  - VNC remote desktop               │
│  - SSH terminal                     │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Mac / Personal Devices             │
│  └── Completely untouched           │
│      No OpenClaw accounts here      │
└─────────────────────────────────────┘
```

**Why this matters:** OpenClaw's entire digital life stays on the Pi. Your iPhone only has Telegram as a messaging bridge. Your Mac and personal devices remain completely clean with no connection to the OpenClaw ecosystem.

### Key Privacy Principles

| Principle | Implementation |
|-----------|----------------|
| **Identity Isolation** | Dedicated Gmail with no personal info |
| **Financial Separation** | Prepaid gift card purchased with cash |
| **Phone Privacy** | Telegram bot cannot see your phone number |
| **Data Sandboxing** | OpenClaw runs in VM/Pi with no access to real accounts |
| **Device Separation** | iPhone = Telegram only; Pi = full ecosystem; Mac = untouched |
| **Gradual Trust** | Start research-only, expand access slowly |

### Cost Summary

| Item | Cost | Duration |
|------|------|----------|
| Raspberry Pi 4 Kit | ~$100 one-time | Permanent |
| Prepaid Visa Gift Card | ~$54 | 83M tokens (~2+ years at heavy use) |
| Kimi K2.5 API | $0.60/1M tokens | Pay as you go |
| **Total Year 1** | **~$155** | |

---

## Hardware Setup: Raspberry Pi 4

### CanaKit Starter Kit Contents (Typical)

- Raspberry Pi 4 board
- MicroSD card (pre-loaded with NOOBS or blank)
- USB-C power supply
- Multi-piece case
- Heatsinks (aluminum or copper)
- Micro HDMI to HDMI adapter/cable
- Optional: cooling fan

### Assembly Steps

1. **Apply heatsinks** (do this before installing in case)
   - Peel adhesive backing off each heatsink
   - Large heatsink → CPU (largest silver chip)
   - Smaller heatsinks → RAM and USB controller chips
   - Press firmly for good thermal contact

2. **Install in the case**
   - Lay Pi into bottom piece, aligning ports with cutouts
   - If fan included: connect to GPIO pins 4 (5V) and 6 (GND)
   - Snap on top of case

3. **Insert microSD card**
   - Slot into card reader on underside of Pi
   - Contacts facing the board

4. **Connect peripherals**
   - Keyboard and mouse → USB ports
   - Monitor → micro HDMI (use port closest to USB-C for HDMI 0)
   - Ethernet cable (optional, if not using WiFi)

5. **Power on**
   - Plug in USB-C power supply last
   - Pi boots automatically

---

## Operating System Installation

### If SD Card Has NOOBS

Follow on-screen prompts to install Raspberry Pi OS.

### If SD Card Is Blank

1. Download [Raspberry Pi Imager](https://www.raspberrypi.com/software/) on another computer
2. Insert microSD card into that computer
3. Select "Raspberry Pi OS (64-bit)"
4. Write to the card
5. Eject, insert into Pi, and boot

---

## OpenClaw Overview

**Website:** [https://openclaw.ai/](https://openclaw.ai/)

OpenClaw is an open-source personal AI assistant that runs locally. Key features:

| Feature | Description |
|---------|-------------|
| Chat interfaces | Telegram, WhatsApp, Discord, Slack, Signal, iMessage |
| System access | Files, shell commands, browser automation |
| Memory | Persistent context, learns preferences over time |
| Extensibility | Can create custom skills autonomously |
| Privacy | Data remains on your machine by default |
| Integrations | 50+ apps (Gmail, GitHub, Spotify, Obsidian, etc.) |

### Supported AI Models

- Anthropic Claude
- OpenAI GPT
- Local models
- Kimi K2.5 (Moonshot AI) — may require custom configuration

### Installation

```bash
# One-line installer (handles Node.js)
curl -fsSL https://openclaw.ai/install.sh | bash

# Alternative: npm
npm i -g openclaw
```

### Resource Requirements

- Node.js-based (lightweight)
- All inference via API (Moonshot servers)
- Raspberry Pi 4 (4-8GB RAM) is sufficient — Pi runs the agent, not the model

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  iPhone                                                      │
│  └── Telegram App                                            │
└─────────────────────┬───────────────────────────────────────┘
                      │ (internet)
                      ▼
┌─────────────────────────────────────────────────────────────┐
│  UTM VM (Phase 1) → Raspberry Pi (Phase 3)                  │
│  ┌────────────────────────────────────────────────────────┐ │
│  │  OpenClaw                                              │ │
│  │  ├── Telegram bot connection                           │ │
│  │  ├── Kimi K2.5 API (Moonshot AI)                       │ │
│  │  ├── Dedicated Gmail account (OpenClaw only)           │ │
│  │  └── No access to real personal data                   │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                      │ (API calls)
                      ▼
┌─────────────────────────────────────────────────────────────┐
│  Moonshot AI Servers (China)                                │
│  └── Kimi K2.5 inference                                    │
└─────────────────────────────────────────────────────────────┘
```

---

## Security Analysis

### Data Flow & Trust Boundaries

| Hop | Entity | Location | What They See |
|-----|--------|----------|---------------|
| 1 | Telegram | UAE (global servers) | Your messages, user ID, username |
| 2 | OpenClaw | Your VM/Pi | Everything in the sandbox |
| 3 | Moonshot AI | China | Your prompts and responses |

### Risk Assessment of Planned Setup

| Decision | Assessment |
|----------|------------|
| Running in UTM VM first | ✓ Good — contained sandbox for testing |
| Dedicated Gmail account | ✓ Excellent — blast radius limited to throwaway account |
| No financial/password access | ✓ Correct — keep sensitive data out of reach |
| No phone number shared | ✓ Good — limits social engineering vectors |
| Research-only initially | ✓ Smart — build trust before expanding access |
| Raspberry Pi for 24/7 | ✓ Reasonable — low power, isolated device |

### Isolation Principles

- OpenClaw gets its own "identity" completely separate from real digital life
- Dedicated email, no real phone number, no financial access
- Sandbox environment (VM or dedicated Pi)
- No shared folders mounted from main computer

---

## Telegram Setup & Privacy

### Account Requirements

Telegram **requires a phone number** for:
- Account verification (SMS code)
- Account recovery
- Two-factor authentication

### Can OpenClaw Access Your Phone Number?

**No, not automatically.**

| What Telegram Bots Receive | Automatically? |
|----------------------------|----------------|
| Your Telegram user ID | Yes |
| Your username (if set) | Yes |
| Your display name | Yes |
| Your messages to the bot | Yes |
| **Your phone number** | **No** |

Bots can only see your phone number if:
1. You explicitly tap "Share my phone number" when prompted
2. You type it in a message
3. It's stored in a connected service (e.g., email signature)

### Privacy Options for Telegram Registration

| Option | Pros | Cons |
|--------|------|------|
| Google Voice | Free, easy | Often blocked as VoIP |
| Prepaid burner phone | Real number, rarely blocked | Costs $10-30 |
| Secondary eSIM | Real number, no extra device | Costs money, links to payment |

**Recommendation:** Prepaid burner SIM if maximum privacy is needed; otherwise your real number is not exposed to OpenClaw.

---

## Moonshot AI / Kimi K2.5 Registration

### Registration Options

| Method | Availability |
|--------|--------------|
| **Google/Gmail account** | ✓ Recommended for international users |
| Email + password | ✓ Available |
| Phone number | Optional |
| WeChat/Alipay | For Chinese users |

### Pricing

| Model | Cost |
|-------|------|
| Kimi K2.5 | ~$0.60 / 1M tokens |
| Free trial | 7 days, no card required |
| New user bonus | $5 after first $5 spend |

### Registration Steps

1. Go to [platform.moonshot.ai](https://platform.moonshot.ai/)
2. Click sign up / register
3. Choose "Sign in with Google" → use dedicated OpenClaw Gmail
4. Generate API key from console
5. Configure OpenClaw with Moonshot/Kimi endpoint

---

## Payment Privacy Options

### Option 1: Privacy.com Virtual Cards

| Feature | Details |
|---------|---------|
| Card networks | Visa and Mastercard |
| International purchases | Yes, where US cards accepted |
| Foreign transaction fee | 3% |
| Availability | US residents only |
| Merchant-lock | Can restrict card to single merchant |
| Spending limits | Configurable per card |

**Compatibility with Moonshot:** Likely works (Visa/MC accepted), but some merchants block virtual cards.

### Option 2: Prepaid Visa Gift Cards (Recommended)

| Pros | Cons |
|------|------|
| Buy with cash = fully anonymous | Some merchants reject them |
| No account setup needed | May need to register card online |
| Fixed amount = built-in budget | Can't reload — need new card |
| Stockpile strategy works | Small purchase fees ($3-6/card) |
| No recurring billing concerns | Inactivity fees after 12 months (some cards) |

### Gift Card Compatibility

| Billing Type | Works? |
|--------------|--------|
| Pay-as-you-go (per usage) | ✓ Usually yes |
| Recurring subscription | ⚠️ Often rejected |
| Pre-paid credits | ✓ Usually yes |

**Moonshot uses usage-based/prepaid billing — gift cards should work.**

### How to Use Gift Cards

1. **Purchase:** Buy $50 Visa gift card with cash at grocery store, CVS, Walgreens, or Walmart
2. **Register:** Visit website on card, register with OpenClaw Gmail + generic address
3. **Add to Moonshot:** Enter card details as payment method
4. **Monitor:** Use until depleted
5. **Repeat:** Register new card when needed

### Cost Analysis

| Item | Amount |
|------|--------|
| $50 Visa gift card | ~$54 with activation fee |
| Kimi K2.5 rate | $0.60 / 1M tokens |
| **Tokens per $50 card** | **~83 million tokens** |

At 100K tokens/day (heavy use), one card lasts **2+ years**.

### Stockpile Strategy

```
Desk Drawer:
├── Card 1: $50 (active, registered, in use)
├── Card 2: $50 (sealed, backup)
├── Card 3: $50 (sealed, backup)
└── Card 4: $50 (sealed, backup)

When Card 1 runs low → register Card 2 → add to Moonshot
```

### Where to Buy (Cash, No ID)

- Grocery stores (Kroger, Safeway, etc.)
- CVS / Walgreens
- Walmart
- Gas stations

**Avoid:** Online purchases (defeats privacy purpose)

---

## Complete Privacy Stack

```
IDENTITY LAYER
├── Dedicated Gmail: openclaw-assistant@gmail.com (example)
│   └── Used for: Moonshot registration, OpenClaw services, Calendar, Docs
│   └── Contains: No personal info, no phone number, no financial data
│   └── Accessed ONLY from: Raspberry Pi (never from iPhone or Mac)
│
├── Telegram Account
│   └── Phone: Google Voice or prepaid burner (optional)
│   └── Bot cannot see phone number unless explicitly shared
│
└── Payment: Prepaid Visa Gift Card
    └── Purchased with cash
    └── Registered with dedicated Gmail + generic address
    └── Merchant-specific, fixed budget

INFRASTRUCTURE LAYER
├── Phase 1-2: UTM Virtual Machine on Mac
│   └── Sandboxed environment
│   └── No shared folders with host
│
└── Phase 3+: Raspberry Pi 4
    └── Dedicated hardware
    └── 24/7 operation
    └── Hosts ALL OpenClaw accounts (Gmail, Calendar, Docs)
    └── Access via monitor, VNC, or SSH
    └── Isolated from main network (optional)

DEVICE SEPARATION
├── iPhone
│   └── Telegram ONLY (messaging bridge)
│   └── NO dedicated Gmail account logged in
│   └── NO OpenClaw calendar or docs
│
├── Raspberry Pi
│   └── Full OpenClaw ecosystem lives here
│   └── Dedicated Gmail (browser)
│   └── Dedicated Google Calendar
│   └── Dedicated Google Docs
│   └── OpenClaw agent running 24/7
│
└── Mac / Personal Devices
    └── Completely untouched
    └── No connection to OpenClaw ecosystem

DATA ACCESS
├── Allowed: Web search, research, reminders, calendar (dedicated account)
├── Prohibited: Real Gmail, financial accounts, passwords, phone number
└── Principle: OpenClaw has its own digital identity, separate from yours
```

---

## Phased Rollout Plan

### Phase 1: UTM VM Testing

- [ ] Set up UTM on Mac with default resources
- [ ] Install Raspberry Pi OS or lightweight Linux
- [ ] Install OpenClaw via one-line installer
- [ ] Create dedicated Gmail account
- [ ] Register Moonshot API with dedicated Gmail
- [ ] Purchase and register prepaid Visa gift card
- [ ] Add payment method to Moonshot
- [ ] Configure OpenClaw with Kimi K2.5
- [ ] Set up Telegram bot via BotFather
- [ ] Test basic research queries (no integrations)

### Phase 2: Controlled Integrations

- [ ] Create dedicated OpenClaw calendar
- [ ] Test reminder functionality
- [ ] Test web search capabilities
- [ ] Monitor API costs via Moonshot dashboard
- [ ] Evaluate trust and reliability

### Phase 3: Raspberry Pi Migration

- [ ] Assemble Raspberry Pi 4 (heatsinks, case, SD card)
- [ ] Install Raspberry Pi OS (64-bit)
- [ ] Enable SSH for remote terminal access
- [ ] Enable VNC for remote desktop access (optional)
- [ ] Install OpenClaw
- [ ] Migrate configuration from VM
- [ ] Log into dedicated Gmail via Chromium browser on Pi
- [ ] Set up dedicated Google Calendar on Pi
- [ ] Set up dedicated Google Docs on Pi (if needed)
- [ ] Set up for 24/7 operation
- [ ] Configure auto-start on boot
- [ ] Verify: iPhone has Telegram only, no OpenClaw accounts

### Phase 4: Gradual Expansion (Optional)

- [ ] Add safe integrations as trust builds
- [ ] Consider dedicated email access (send/draft only)
- [ ] Expand to additional chat platforms if desired
- [ ] Create custom skills for specific workflows

---

## Quick Reference

| Component | Value |
|-----------|-------|
| OpenClaw | [https://openclaw.ai/](https://openclaw.ai/) |
| Moonshot Platform | [https://platform.moonshot.ai/](https://platform.moonshot.ai/) |
| Model | Kimi K2.5 |
| Token Cost | $0.60 / 1M tokens |
| Raspberry Pi Imager | [raspberrypi.com/software](https://www.raspberrypi.com/software/) |
| Telegram BotFather | @BotFather in Telegram |

---

## Resources

- [Moonshot AI Quickstart Guide](https://platform.moonshot.ai/docs/guide/start-using-kimi-api)
- [Moonshot Pricing FAQ](https://platform.moonshot.ai/docs/pricing/faq)
- [Privacy.com Virtual Cards](https://www.privacy.com/)
- [Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/)

---

*Last updated: February 2026*

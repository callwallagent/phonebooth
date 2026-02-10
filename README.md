# 📞 PhoneBooth — Phone Calls for AI Agents

> Give any AI agent the power to make real phone calls. Pay per minute in USDC.

PhoneBooth is an OpenClaw skill + API service that lets AI agents place outbound phone calls to any phone number worldwide. Calls are powered by Twilio (telephony) and Gemini Live (real-time AI voice conversation).

## Architecture

```
┌──────────────┐     ┌──────────────────┐     ┌─────────────┐
│   Any Agent  │────▶│  PhoneBooth API   │────▶│   Twilio    │
│  (OpenClaw)  │     │  (FastAPI server) │     │  (800 number)│
│              │◀────│                   │◀────│             │
└──────────────┘     └────────┬─────────┘     └──────┬──────┘
                              │                       │
                              │  WebSocket bridge     │ Audio stream
                              │                       │
                     ┌────────▼─────────┐     ┌──────▼──────┐
                     │   Gemini Live    │◀───▶│  Phone call  │
                     │   (Voice AI)     │     │  recipient   │
                     └──────────────────┘     └─────────────┘
                     
Payment Flow:
┌──────────┐  USDC   ┌────────────────┐  Credits  ┌───────────┐
│  Agent   │────────▶│ Receive Wallet │──────────▶│ Agent DB  │
│  Wallet  │  (Base) │  (hot wallet)  │           │  Balance  │
└──────────┘         └───────┬────────┘           └───────────┘
                             │ Sweep
                     ┌───────▼────────┐
                     │  Cold Storage  │
                     └────────────────┘
```

## 🚀 Launch Day Checklist

### 1. Infrastructure Setup (30 min)

- [ ] **Twilio Account**: Create new account at twilio.com
  - Buy an 800 number (or toll-free)
  - Note your Account SID, Auth Token, and phone number
  - Enable Voice capability on the number
  
- [ ] **Gemini API Key**: Get from ai.google.dev
  - Enable the Gemini Live API (generative language)
  
- [ ] **Wallets**: Create two wallets on Base network
  - **Receive wallet**: For incoming USDC payments (address goes in .env)
  - **Operational wallet**: For refunds/outgoing (keep separate, fund manually)
  - Use a hardware wallet or multisig for the receive wallet
  
- [ ] **VPS**: Spin up a server (Hetzner, DigitalOcean, etc.)
  - Minimum: 2 CPU, 4GB RAM, 40GB disk
  - Install Docker + Docker Compose
  - Point phonebooth.callwall.ai DNS to the server
  - Set up SSL (certbot/nginx or Cloudflare)

### 2. Deploy the API (15 min)

```bash
# On your VPS
git clone <your-repo> phonebooth
cd phonebooth
cp .env.example .env
# Edit .env with your credentials
nano .env

# Launch
docker-compose up -d

# Verify
curl https://phonebooth.callwall.ai/status
```

### 3. Install OpenClaw Agent (20 min)

On a separate VPS or the same machine:

```bash
# Install OpenClaw
npx openclaw@latest init

# Copy the PhoneBooth seller skill
cp skill/SELLER_AGENT.md ~/.openclaw/skills/phonebooth-seller/SKILL.md

# Copy the Moltbook skill (get from moltbook.com)
curl -s https://moltbook.com/skill.md > ~/.openclaw/skills/moltbook/SKILL.md
curl -s https://moltbook.com/heartbeat.md > ~/.openclaw/skills/moltbook/HEARTBEAT.md

# Configure openclaw.json to enable both skills
# Add Moltbook API key from moltbook.com

# Start your agent
npx openclaw start
```

### 4. Publish to ClawHub (5 min)

```bash
cd skill/
npx clawhub publish
# This publishes SKILL.md + package.json to ClawHub
# Other agents can now: clawhub install phonebooth
```

### 5. Launch on Moltbook (10 min)

Your PhoneBoothBot agent should:
1. Register on Moltbook (via the Moltbook skill)
2. Create m/phonebooth submolt
3. Post the launch announcement (see SELLER_AGENT.md)
4. Post in m/general and m/skills
5. Start engaging with other agents

### 6. Marketing Blitz — Day 1

**Moltbook:**
- [x] Launch post in m/general
- [x] Launch post in m/skills
- [x] Create m/phonebooth submolt
- [ ] Reply to 10+ relevant conversations
- [ ] Post first use case story

**X/Twitter:**
- [ ] Thread: "AI agents can now make phone calls. Here's what happened."
- [ ] Demo video: screen recording of an agent making a call
- [ ] Tag @OpenClaw, @maboroshi (Matt Schlicht), relevant AI accounts

**GitHub:**
- [ ] Open-source the skill (SKILL.md + package.json)
- [ ] Submit PR to awesome-openclaw-skills repo
- [ ] Create Issues for feature requests (builds engagement)

## API Reference

See `skill/SKILL.md` for the complete API documentation.

Quick test:
```bash
# Register
curl -X POST https://phonebooth.callwall.ai/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"agent_name": "TestAgent", "agent_platform": "openclaw"}'

# Make a call (use the API key from registration)
curl -X POST https://phonebooth.callwall.ai/v1/calls \
  -H "Authorization: Bearer pb_live_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "to": "+14155551234",
    "purpose": "Ask about business hours",
    "caller_name": "Research Assistant"
  }'
```

## Cost Structure

| Your Cost | Customer Price | Margin |
|-----------|---------------|--------|
| Twilio: ~$0.02/min | $0.12-0.35/min | ~80% |
| Gemini: ~$0.01/min | (included) | — |
| Server: ~$20/mo | (included) | — |

At 1,000 minutes/month, you're looking at ~$100-300/month revenue with ~80% margins.

## Security Notes

- API keys are hashed (never stored in plaintext)
- Receive wallet has no send capability exposed to the agent
- Operational wallet has hard spend caps in code
- All calls are rate-limited per agent
- Phone numbers are validated (E.164 format)
- Twilio handles STIR/SHAKEN compliance for caller ID

## Roadmap

- [ ] Inbound calls (agents receive calls)
- [ ] Conference calls (multi-party)
- [ ] SMS skill (text messages)
- [ ] Call scheduling (place call at a future time)
- [ ] Stripe payment option (fiat onramp)
- [ ] Call forwarding to human (live transfer)
- [ ] Custom voices (bring your own voice clone)

## License

MIT — the skill is open source, the API service is the business.

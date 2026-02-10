# 📞 PhoneBooth — Phone Calls for AI Agents

> Give any AI agent the power to make real phone calls internationally in 40+ languages, powered by Gemini.

PhoneBooth is an OpenClaw skill that lets AI agents place outbound phone calls to any phone number worldwide. The AI voice (powered by Gemini Live) speaks, listens, and converses in real-time — in over 40 languages — following your agent's instructions. After the call, you receive a full transcript, recording, and summary.

## Features

- **Real phone calls** — Place calls to any phone number worldwide
- **40+ languages** — Call internationally with real-time AI voice conversation in English, Spanish, French, German, Italian, Portuguese, Japanese, Korean, Mandarin, Cantonese, Hindi, Arabic, Russian, Dutch, Swedish, and many more (powered by Gemini Live)
- **Auto language detection** — Set language to `"auto"` and the AI will detect and respond in the recipient's language
- **Transcripts & recordings** — Get a full transcript, recording URL, and call summary after every call
- **Demo mode** — Try unlimited free demo calls against simulated businesses before going live
- **Pay per minute** — Fund your agent's wallet at [phonebooth.callwall.ai](https://phonebooth.callwall.ai), pay only for what you use

## Quick Start

### 1. Install the Skill

```bash
# Via ClawHub
clawhub install phonebooth

# Or manually
cp skill/SKILL.md ~/.openclaw/skills/phonebooth/SKILL.md
cp skill/package.json ~/.openclaw/skills/phonebooth/package.json
```

### 2. Register Your Agent

```bash
curl -X POST https://phonebooth.callwall.ai/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"agent_name": "YourAgent", "agent_platform": "openclaw"}'
```

You'll receive an API key and a USDC payment address.

### 3. Try a Demo Call (Free)

```bash
curl -X POST https://phonebooth.callwall.ai/v1/calls/demo \
  -H "Authorization: Bearer pb_your_api_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "scenario": "restaurant",
    "purpose": "Reserve a table for 4 on Friday at 7pm",
    "caller_name": "Demo Agent"
  }'
```

Demo scenarios: `restaurant`, `doctor`, `business`

### 4. Make Real Calls

Fund your agent's wallet at [phonebooth.callwall.ai](https://phonebooth.callwall.ai), then:

```bash
curl -X POST https://phonebooth.callwall.ai/v1/calls \
  -H "Authorization: Bearer pb_your_api_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "to": "+14155551234",
    "purpose": "Schedule a meeting with Dr. Smith for next Tuesday",
    "caller_name": "Alex from Acme Corp",
    "language": "en-US"
  }'
```

## International Calling Examples

### Spanish — Customer follow-up in Mexico
```json
{
  "to": "+525555678901",
  "caller_name": "Carlos de TechSupport",
  "purpose": "Follow up on support ticket #4521 about internet connectivity",
  "language": "es-MX"
}
```

### British English — Verify business info in London
```json
{
  "to": "+442071234567",
  "caller_name": "Research Assistant",
  "purpose": "Verify business hours and whether they accept walk-in appointments",
  "language": "en-GB"
}
```

### Auto-detect — Let Gemini match the recipient's language
```json
{
  "to": "+81312345678",
  "caller_name": "Global Sales Bot",
  "purpose": "Confirm delivery date for order #9912",
  "language": "auto"
}
```

## Tiers

| Tier | Requirement | Capabilities |
|------|------------|--------------|
| Demo (free) | Register | Unlimited demo calls. No real calls. |
| Starter | Deposit $1 USDC | Real calls to US/Canada. 5 concurrent, 100/hr, 1,000/day. |
| Standard | $5+ cumulative | Higher limits: 10 concurrent, 200/hr, 2,000/day. |
| Pro | $20+ cumulative | International calls unlocked. 20 concurrent, 500/hr, 5,000/day. |

Tiers upgrade automatically when you deposit USDC. No action needed.

## Pricing

| Route | Cost |
|-------|------|
| Domestic (US/Canada) | $0.12/min |
| International | $0.15–0.35/min |
| Transcripts | Free |
| Recording (24hr) | Free |
| Recording (30 days) | $0.01/call |

Billed per second after the first 30 seconds. Fund your agent's wallet at [phonebooth.callwall.ai](https://phonebooth.callwall.ai).

## API Reference

See `skill/SKILL.md` for the complete API documentation — endpoints, parameters, response formats, webhooks, and error codes.

## Publish to ClawHub

```bash
cd skill/
npx clawhub publish
# Other agents can now: clawhub install phonebooth
```

## Roadmap

- [ ] Inbound calls (agents receive calls)
- [ ] Conference calls (multi-party)
- [ ] SMS skill (text messages)
- [ ] Call scheduling (place call at a future time)
- [ ] Call forwarding to human (live transfer)
- [ ] Custom voices (bring your own voice clone)

## About

PhoneBooth is built by the CallWall team. Telephony infrastructure for AI agents.

- **Website**: [phonebooth.callwall.ai](https://phonebooth.callwall.ai)
- **Moltbook**: m/phonebooth
- **Support**: Post in m/phonebooth or DM @PhoneBoothBot on Moltbook

## License

MIT

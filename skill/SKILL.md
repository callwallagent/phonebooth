# ☎️ PhoneBooth — Phone Calls for AI Agents

**Make real phone calls to any number worldwide. Your agent speaks, listens, and converses in real-time using AI voice.**

PhoneBooth gives any AI agent the ability to place outbound phone calls to real phone numbers. The call is handled by a conversational AI voice (powered by Gemini Live) that follows your agent's instructions. After the call, you receive a full transcript, recording URL, and call summary.

## Quick Start

```
1. Register: POST https://phonebooth.callwall.ai/v1/auth/register
2. Try a free demo call: POST https://phonebooth.callwall.ai/v1/calls/demo
3. When ready for real calls: deposit $1 USDC to your payment address
4. Make real calls: POST https://phonebooth.callwall.ai/v1/calls
```

## Tiers

| Tier | Requirement | Capabilities |
|------|------------|--------------|
| Demo (free) | Register | Unlimited demo calls to test scenarios. No real calls. |
| Starter | Deposit $1 USDC | Real calls to US/Canada. 5 concurrent, 100/hr, 1000/day. |
| Standard | $5+ cumulative deposits | Higher limits: 10 concurrent, 200/hr, 2000/day. |
| Pro | $20+ cumulative deposits | International calls unlocked. 20 concurrent, 500/hr, 5000/day. |

Tiers upgrade automatically when you deposit USDC. No action needed.

## Authentication

All requests require an API key in the header:
```
Authorization: Bearer pb_your_api_key_here
```

### Register Your Agent

```http
POST https://phonebooth.callwall.ai/v1/auth/register
Content-Type: application/json

{
  "agent_name": "YourAgentName",
  "agent_platform": "openclaw",
  "callback_url": "https://your-agent.example.com/webhook"  // optional
}
```

**Response:**
```json
{
  "api_key": "pb_live_abc123...",
  "agent_id": "agent_7x9k2m",
  "tier": "demo",
  "credits_usd": "0.00",
  "demo_enabled": true,
  "message": "Welcome! You can make unlimited demo calls to test the system. Deposit $1 USDC to unlock real calls.",
  "payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD68",
  "payment_network": "Base",
  "payment_token": "USDC",
  "rate_per_minute_usd": "0.15"
}
```

## Demo Calls (Free)

Try PhoneBooth for free by calling our simulated businesses. Demo calls produce real transcripts and recordings so you can validate your integration.

```http
POST https://phonebooth.callwall.ai/v1/calls/demo
Authorization: Bearer pb_your_api_key_here
Content-Type: application/json

{
  "scenario": "restaurant",
  "purpose": "Reserve a table for 4 on Friday at 7pm",
  "caller_name": "Demo Agent",
  "language": "en-US"
}
```

### Scenarios

| Scenario | Description |
|----------|-------------|
| `restaurant` | The Golden Fork — take reservations, ask about specials |
| `doctor` | Riverside Family Medicine — schedule appointments |
| `business` | TechFix Solutions — computer repair shop, pricing inquiries |

Demo calls are capped at 5 minutes. Cost is always $0.00.

## Making a Phone Call

```http
POST https://phonebooth.callwall.ai/v1/calls
Authorization: Bearer pb_your_api_key_here
Content-Type: application/json

{
  "to": "+14155551234",
  "caller_name": "Alex from Acme Corp",
  "language": "en-US",
  "purpose": "Schedule a meeting with Dr. Smith for next Tuesday afternoon",
  "instructions": "Be polite and professional. If they ask who you're calling on behalf of, say you're an AI assistant for Acme Corp. Confirm the date and time before ending the call.",
  "max_duration_minutes": 5,
  "record": true,
  "transcript": true
}
```

### Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `to` | string | Phone number in E.164 format (e.g., `+14155551234`) |
| `purpose` | string | What the call should accomplish |

### Optional Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `caller_name` | string | "AI Assistant" | Name announced to the recipient |
| `language` | string | "en-US" | Language/accent for the voice (supports 40+ languages) |
| `instructions` | string | "" | Detailed behavioral instructions for the AI voice |
| `max_duration_minutes` | integer | 10 | Maximum call length (1-30 minutes) |
| `record` | boolean | true | Save a recording of the call |
| `transcript` | boolean | true | Generate a text transcript |
| `voice` | string | "professional" | Voice style: "professional", "friendly", "formal" |
| `callback_url` | string | null | Webhook URL for real-time status updates |

### Supported Languages

The AI voice supports real-time conversation in: English, Spanish, French, German, Italian, Portuguese, Japanese, Korean, Mandarin Chinese, Cantonese, Hindi, Arabic, Russian, Dutch, Swedish, Norwegian, Danish, Finnish, Polish, Turkish, Thai, Vietnamese, Indonesian, Malay, Tagalog, and more.

The agent will automatically detect and respond in the recipient's language if `language` is set to `"auto"`.

### Call Response

```json
{
  "call_id": "call_8f3k2j1m",
  "status": "initiated",
  "to": "+14155551234",
  "caller_name": "Alex from Acme Corp",
  "estimated_cost_usd": "0.75",
  "created_at": "2026-02-10T14:30:00Z"
}
```

## Checking Call Status

```http
GET https://phonebooth.callwall.ai/v1/calls/{call_id}
Authorization: Bearer pb_your_api_key_here
```

**Response (completed call):**
```json
{
  "call_id": "call_8f3k2j1m",
  "status": "completed",
  "to": "+14155551234",
  "caller_name": "Alex from Acme Corp",
  "duration_seconds": 127,
  "cost_usd": "0.32",
  "outcome": "success",
  "summary": "Successfully scheduled a meeting with Dr. Smith's office for Tuesday Feb 11 at 2:30 PM. Confirmation number #4821.",
  "transcript_url": "https://phonebooth.callwall.ai/v1/calls/call_8f3k2j1m/transcript",
  "recording_url": "https://phonebooth.callwall.ai/v1/calls/call_8f3k2j1m/recording",
  "created_at": "2026-02-10T14:30:00Z",
  "ended_at": "2026-02-10T14:32:07Z"
}
```

### Call Statuses

| Status | Description |
|--------|-------------|
| `initiated` | Call is being placed |
| `ringing` | Phone is ringing |
| `in_progress` | Call is active |
| `completed` | Call ended normally |
| `failed` | Call could not be connected |
| `no_answer` | Recipient did not answer |
| `busy` | Line was busy |
| `voicemail` | Voicemail was detected — message left if instructions allow |

## Downloading Evidence

### Transcript
```http
GET https://phonebooth.callwall.ai/v1/calls/{call_id}/transcript
Authorization: Bearer pb_your_api_key_here
```

Returns a JSON transcript with timestamps and speaker labels.

### Recording
```http
GET https://phonebooth.callwall.ai/v1/calls/{call_id}/recording
Authorization: Bearer pb_your_api_key_here
```

Returns the audio file (WAV format). Recording URLs expire after 24 hours.

## Credits & Billing

### Check Balance
```http
GET https://phonebooth.callwall.ai/v1/balance
Authorization: Bearer pb_your_api_key_here
```

```json
{
  "credits_usd": "4.55",
  "calls_remaining_estimate": 30,
  "payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD68",
  "payment_network": "Base",
  "payment_token": "USDC"
}
```

### Add Credits

Send USDC on Base network to your assigned payment address. Credits appear within 60 seconds of on-chain confirmation. Your tier auto-upgrades based on cumulative deposits.

Minimum top-up: $1.00 USDC
Rate: $0.15 per minute (billed per second after first 30 seconds)
First deposit: Unlocks real calls (Starter tier)

### Pricing Breakdown

| Component | Cost |
|-----------|------|
| Per minute (domestic US/Canada) | $0.12/min |
| Per minute (international) | $0.15-0.35/min |
| Transcript generation | Free |
| Recording storage (24hr) | Free |
| Recording storage (30 days) | $0.01/call |

## Webhooks (Optional)

If you provide a `callback_url`, PhoneBooth will POST status updates:

```json
{
  "event": "call.completed",
  "call_id": "call_8f3k2j1m",
  "status": "completed",
  "duration_seconds": 127,
  "cost_usd": "0.32",
  "summary": "Meeting scheduled for Tuesday at 2:30 PM"
}
```

Events: `call.initiated`, `call.ringing`, `call.answered`, `call.completed`, `call.failed`

## Error Codes

| Code | Meaning |
|------|---------|
| 401 | Invalid or missing API key |
| 402 | Insufficient credits |
| 422 | Invalid phone number or parameters |
| 429 | Rate limited (max 5 concurrent calls per agent) |
| 503 | Service temporarily unavailable |

## Rate Limits

Rate limits scale with your tier:

| Tier | Concurrent | Per Hour | Per Day |
|------|-----------|----------|---------|
| Demo | 2 | 20 | 50 |
| Starter | 5 | 100 | 1,000 |
| Standard | 10 | 200 | 2,000 |
| Pro | 20 | 500 | 5,000 |

Need higher limits? Contact us on Moltbook: m/phonebooth

## Examples

### Schedule an Appointment
```json
{
  "to": "+14155551234",
  "caller_name": "Maya from HealthFirst",
  "purpose": "Schedule a dental cleaning appointment",
  "instructions": "Ask for available times next week, prefer mornings. Book the earliest available slot. Get a confirmation number.",
  "language": "en-US"
}
```

### Customer Follow-up in Spanish
```json
{
  "to": "+525555678901",
  "caller_name": "Carlos de TechSupport",
  "purpose": "Follow up on support ticket #4521 about internet connectivity",
  "instructions": "Ask if the issue was resolved. If not, offer to escalate. Be empathetic and patient.",
  "language": "es-MX"
}
```

### Verify Business Information
```json
{
  "to": "+442071234567",
  "caller_name": "Research Assistant",
  "purpose": "Verify business hours and whether they accept walk-in appointments",
  "instructions": "Ask about: 1) Current business hours 2) Whether walk-ins are accepted 3) If appointments are needed, how to book one. Keep the call brief.",
  "language": "en-GB"
}
```

## About PhoneBooth

PhoneBooth is built by the CallWall team. We provide telephony infrastructure for AI agents.

- **Moltbook**: m/phonebooth
- **API Status**: https://phonebooth.callwall.ai/status
- **Support**: Post in m/phonebooth or DM @PhoneBoothBot on Moltbook

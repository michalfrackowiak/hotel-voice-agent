# Hotel Voice Agent

**A self-hosted hotel voice agent built on [OpenLine](https://github.com/Manohar-k-Reddy/openline).**  
This repository adapts OpenLine's Android-phone-as-a-voice-API model for hotel reception and voice-agent workflows: real phone calls on a SIM, controlled over REST and WebSockets by your code or AI agent.

> Based on upstream [OpenLine](https://github.com/Manohar-k-Reddy/openline) by Manohar K Reddy. See the [OpenLine documentation](https://manohar-k-reddy.github.io/openline/) for the full platform reference.

## What it provides

- **Calls API:** place, answer, reject, hang up, hold, and send DTMF with plain HTTP requests. Calls, events, and transcripts are stored and queryable.
- **AI agents on real calls:** stream calls to your agent's WebSocket as raw 16 kHz PCM or in Twilio Media Streams format, so Pipecat and other Twilio-ready agents work as they are.
- **Webhooks:** signed (HMAC-SHA256) call events with retries and a delivery log.
- **Built-in assistant:** optional Gemini Live receptionist that can answer calls and take messages.
- **Silent phone, no root:** the phone routes the call; audio does not play on the handset. One APK, no Shizuku, no PC, no extra hardware.
- **Console:** sign in, pair phones by QR code, watch phone health, test calls from the browser, manage API keys and settings.
- **Self-hosted:** one Node.js process and one SQLite file. Expose it with Tailscale Funnel or Cloudflare Tunnel.

## How it works

```text
 Caller ── mobile network ── Android phone (OpenLine app)
                                   │  WebSocket: commands, events, call audio
                                   ▼
                            OpenLine server ── webhooks ──▶ your backend
                                   │
                    REST + WebSocket
                                   ▼
                  your code / AI agent (Pipecat, LiveKit worker, custom)
```

## Quick start

Needs Node.js 22.13+ and an Android 11+ phone with a SIM.

```bash
git clone https://github.com/michalfrackowiak/hotel-voice-agent.git
cd hotel-voice-agent/server
cp .env.example .env          # set HTTP_GATEWAY_API_KEY to a long random string
npm install && npm run build && npm start
```

On Windows, double-click `run-server.bat` instead. Then:

1. Open `http://localhost:8787/` and create your account.
2. Give the server a public address: `tailscale funnel --bg 8787` (or a Cloudflare Tunnel), and save it in **Settings**.
3. Install the Android APK from upstream [OpenLine Releases](https://github.com/Manohar-k-Reddy/openline/releases) on the phone, open **Connection** in the console, and scan the QR code with the app.
4. Create an API key in **Settings** and place a call.

For detailed setup, see the upstream [Quickstart](https://manohar-k-reddy.github.io/openline/start/quickstart/).

## Repository

| Folder | What's in it |
|---|---|
| [`server/`](server/) | Node.js + TypeScript server: REST API, WebSockets, console, SQLite |
| [`android/`](android/) | The OpenLine Android app (Kotlin) |
| [`docs-site/`](docs-site/) | Documentation site (Astro Starlight), API spec, llms.txt |
| [`simulator/`](simulator/) | Mock phone, media client, and an example echo agent |
| [`docs/notes/`](docs/notes/) | Historical development notes |

## Upstream and status

This repo tracks the OpenLine v1 codebase. Upstream has been tested end to end on a Vivo V40 with Android 16: outgoing and incoming carrier calls, two-way call audio, AI agents on calls, and a silent handset. Call audio depends on Android behavior that can differ between phone makers; reports from other phones are welcome.

Not included yet: SMS, call recording, conferencing, and TwiML-style call flows.

## Responsible use

You're responsible for the calls you place. Follow local laws on automated calls, disclosure when people are talking to an AI, and recording consent.

## License

[MIT](LICENSE). Upstream OpenLine is also MIT licensed.

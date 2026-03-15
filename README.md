# AnonConnect

> Truly anonymous real-time chat & video calls — no database, no accounts, no traces.

![Node.js](https://img.shields.io/badge/Node.js-18+-green) ![Socket.io](https://img.shields.io/badge/Socket.io-4.6-blue) ![WebRTC](https://img.shields.io/badge/WebRTC-P2P-orange) ![License](https://img.shields.io/badge/License-MIT-purple)

---

## What is AnonConnect?

AnonConnect is a production-ready anonymous communication platform. You get a random identity the moment you open the app — no signup, no login, no data stored anywhere. Every message, room, and call lives in server memory only and vanishes on restart.

---

## Features

### 💬 Chat
- Random anonymous usernames auto-generated on connect (e.g. `SilentPanda42`)
- Create or join named rooms — switch freely anytime
- Optional password-protected private rooms
- Last 50 messages synced instantly when you join a room
- Live typing indicators
- Emoji picker
- Message reactions (broadcast live to room)
- Inline image sharing via base64 (< 2MB, no cloud upload)
- Disappearing messages — room-level TTL: `off / 5min / 1hr / 24hr`
- Empty rooms auto-deleted after 5 minutes

### 📹 Voice & Video Calls
- Peer-to-peer WebRTC calls — server only handles signaling
- Up to 4 participants per call room
- Mute / camera toggle with live status broadcast
- Screen sharing
- In-call side chat panel
- Live call duration timer
- 6-character shareable room codes

### 🔒 Security
- `helmet` — secure HTTP headers
- `express-rate-limit` — 100 requests / 15 min per IP
- Per-socket rate limit — 30 messages / min
- Zero persistence — no DB, no logs, no cookies
- Configurable CORS via `CORS_ORIGIN` env variable

---

## Quick Start
```bash
git clone https://github.com/your-username/AnonConnect.git
cd AnonConnect
npm install
cp .env.example .env
npm start
```

Open → **http://localhost:3000**

For development with auto-restart:
```bash
npm run dev
```

---

## Environment Variables

| Variable | Default | Description |
|---|---|---|
| `PORT` | `3000` | Server port |
| `NODE_ENV` | `development` | Runtime mode |
| `CORS_ORIGIN` | `*` | Allowed origin (lock down in production) |

---

## Project Structure
```
AnonConnect/
├── server.js                  # Express + Socket.io backend
├── package.json
├── .env.example
└── public/
    ├── index.html             # Landing page
    ├── chat.html              # Chat room page
    ├── call.html              # Voice/video call page
    ├── css/
    │   ├── main.css           # Global theme + variables
    │   ├── chat.css           # Chat interface
    │   ├── call.css           # Call interface
    │   └── components.css     # Reusable components
    └── js/
        ├── socket-client.js   # Socket.io connection manager
        ├── chat.js            # Chat logic
        ├── webrtc.js          # WebRTC peer connection
        ├── call.js            # Call UI + controls
        └── utils.js           # Helpers + utilities
```

---

## Socket Events

### `/chat` Namespace

| Event | Direction | Description |
|---|---|---|
| `welcome` | server → client | Assigns random username on connect |
| `join-room` | client → server | Join a named room |
| `join-room-error` | server → client | Wrong password or room not found |
| `send-message` | client → server | Send text message to room |
| `send-file` | client → server | Send base64 image to room |
| `add-reaction` | client → server | Add emoji reaction to a message |
| `reaction-updated` | server → room | Reactions changed on a message |
| `message-deleted` | server → room | TTL expired, message removed |
| `typing-start` | client → server | User started typing |
| `typing-stop` | client → server | User stopped typing |
| `get-rooms` | client → server | Fetch active room list |
| `create-room` | client → server | Create new room with options |
| `online-count` | server → all | Total connected users updated |
| `rate-limit-hit` | server → client | Message rate limit exceeded |

### `/call` Namespace

| Event | Direction | Description |
|---|---|---|
| `join-call-room` | client → server | Join call room by code |
| `webrtc-offer` | client → server | Forward SDP offer to peer |
| `webrtc-answer` | client → server | Forward SDP answer to caller |
| `webrtc-ice-candidate` | client → server | Relay ICE candidate between peers |
| `call-request` | client → server | Notify room of incoming call |
| `call-accepted` | client → server | Callee accepted the call |
| `call-rejected` | client → server | Callee rejected the call |
| `call-ended` | client → server | End call for all participants |
| `mute-toggle` | client → server | Broadcast mic mute state |
| `video-toggle` | client → server | Broadcast camera state |

---

## Architecture
```
Browser ──── Socket.io ────► Node.js Server
   │          (signaling)     (in-memory only)
   │                               │
   └──── WebRTC P2P ───────────────┘
         (direct media,
          no server relay)
```

---

## Production Notes

- **HTTPS required** — WebRTC won't work on plain HTTP in modern browsers
- **TURN servers** — Add Coturn for users behind strict NATs/firewalls
- **Scaling** — Use `@socket.io/redis-adapter` for multi-instance deployments
- **CORS** — Set `CORS_ORIGIN` to your exact domain, never leave `*` public

---

## License

MIT — do whatever you want with it.

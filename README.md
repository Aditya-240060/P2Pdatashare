# P2P Web Share 🔗

> **Serverless, zero-storage, peer-to-peer file sharing — directly from your browser.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-p2pproject.vercel.app-blue?style=for-the-badge&logo=vercel)](https://p2-pproject.vercel.app/)
[![Frontend](https://img.shields.io/badge/Frontend-React%20%2B%20Vite-61DAFB?style=for-the-badge&logo=react)](https://p2-pproject.vercel.app/)
[![Backend](https://img.shields.io/badge/Backend-Node.js%20%2B%20Socket.io-339933?style=for-the-badge&logo=node.js)](https://p2pproject.onrender.com)
[![License](https://img.shields.io/badge/License-ISC-green?style=for-the-badge)](LICENSE)

---

## 🌐 Live Demo

👉 **[https://p2-pproject.vercel.app/](https://p2-pproject.vercel.app/)**

No login. No cloud storage. No file size limits imposed by a server. Just share a link and transfer.

---

## 📖 What is P2P Web Share?

**P2P Web Share** is a browser-based file sharing application that uses **WebRTC** to transfer files directly between two peers — with zero involvement from any server in the actual data transfer. The signaling server only helps the two browsers find each other; after that, all file data travels through an encrypted peer-to-peer channel.

### How it Works

```
Sender                  Signaling Server              Receiver
  |                          |                            |
  |--- Create Room --------->|                            |
  |<-- Room ID --------------|                            |
  |                          |<-- Join Room (via link) ---|
  |<-- WebRTC Offer -------->|---- WebRTC Answer -------->|
  |<===================== P2P Data Channel =============>|
  |<========= File chunks transfer directly (no server) =>|
```

1. **Sender** drops a file and gets a unique shareable link.
2. **Receiver** opens the link in any modern browser.
3. A **WebRTC data channel** is negotiated via the signaling server.
4. The file is **chunked, hashed, and streamed** directly peer-to-peer.
5. The receiver's browser **verifies the SHA-256 hash** before triggering the download.

---

## ✨ Features

- 🔒 **End-to-end encrypted transfer** — WebRTC DTLS/SRTP encryption by default
- 🚫 **Zero server storage** — the signaling server never touches your file data
- ✅ **SHA-256 integrity verification** — file hash checked on receiver side before download
- ⚡ **Chunked streaming** — files split into 64 KB chunks for efficient transfer
- 📊 **Live progress tracking** — real-time transfer speed and progress bar
- 📱 **Responsive UI** — works on desktop and mobile browsers
- 🛡️ **Rate limiting** — DoS protection on the signaling server
- 🚀 **No install required** — runs entirely in the browser

---

## 🏗️ Architecture

This project follows a **client-server hybrid architecture** where the server acts purely as a WebRTC signaling and matchmaking service.

```
┌─────────────────────────────────────────────────────┐
│                     CLIENT (Vercel)                  │
│  React 19 + Vite + TypeScript + TailwindCSS          │
│  ┌──────────┐  ┌──────────┐  ┌────────────────────┐ │
│  │  Pages   │  │  Hooks   │  │  Zustand Store     │ │
│  │ HomePage │  │useWebRTC │  │  fileSlice         │ │
│  │ RoomPage │  │useRoom   │  │  roomSlice         │ │
│  └──────────┘  │useSocket │  │  transferSlice     │ │
│                └──────────┘  └────────────────────┘ │
│  ┌───────────────────────────────────────────────┐   │
│  │  lib/ — chunker · hasher · downloader         │   │
│  └───────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
                        │  Socket.io (signaling only)
┌─────────────────────────────────────────────────────┐
│                     SERVER (Render)                  │
│  Node.js + Express + Socket.io + TypeScript          │
│  ┌──────────────┐  ┌────────────┐  ┌─────────────┐  │
│  │  RoomStore   │  │  Handlers  │  │  Middleware  │  │
│  │  (in-memory) │  │ (signaling)│  │  (security) │  │
│  └──────────────┘  └────────────┘  └─────────────┘  │
└─────────────────────────────────────────────────────┘
```

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend Framework | React 19 + TypeScript |
| Build Tool | Vite 8 |
| Styling | TailwindCSS 4 |
| State Management | Zustand 5 |
| WebRTC | SimplePeer |
| Real-time Signaling | Socket.io 4 |
| Routing | React Router 7 |
| Icons | Lucide React |
| Toasts | Sonner |
| Backend Runtime | Node.js + Express |
| Frontend Tests | Vitest + Testing Library |
| Backend Tests | Jest + ts-jest |
| Frontend Deploy | Vercel |
| Backend Deploy | Render |

---

## 🔐 Security

| Feature | Details |
|---------|---------|
| **Zero Server Storage** | Files travel P2P only; server routes WebRTC SDP offers/answers |
| **Transport Encryption** | WebRTC DTLS encrypts the data channel end-to-end |
| **SHA-256 Integrity** | File hash computed by sender, verified by receiver before download |
| **Timing-Safe Comparison** | Hash verification uses constant-time comparison to prevent timing attacks |
| **Rate Limiting** | `express-rate-limit` protects the signaling server from DoS |
| **Helmet** | HTTP security headers set via `helmet` |
| **CORS** | Strict origin allowlist configured per environment |

---

## 🚀 Getting Started (Local Development)

### Prerequisites

- **Node.js** v18 or higher
- **npm** v9 or higher

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/P2Pproject.git
cd P2Pproject
```

### 2. Install Dependencies

**Server:**
```bash
cd server
npm install
```

**Client:**
```bash
cd ../client
npm install
```

### 3. Configure Environment Variables

**Server** — copy and edit:
```bash
cd server
cp .env.example .env
```

Edit `server/.env`:
```env
PORT=3001
CORS_ORIGIN=http://localhost:5173
NODE_ENV=development
```

**Client** — copy and edit:
```bash
cd ../client
cp .env.example .env
```

Edit `client/.env`:
```env
VITE_SOCKET_URL=http://localhost:3001
VITE_MAX_FILE_SIZE_MB=50
VITE_CHUNK_SIZE_BYTES=65536
```

### 4. Run the Development Servers

Open **two terminals**:

**Terminal 1 — Backend:**
```bash
cd server
npm run dev
```
> Server starts at `http://localhost:3001`

**Terminal 2 — Frontend:**
```bash
cd client
npm run dev
```
> App opens at `http://localhost:5173`

### 5. Test it Locally

1. Open `http://localhost:5173` in **Browser Tab A** (Sender).
2. Drop a file and copy the generated share link.
3. Open the share link in **Browser Tab B** (Receiver).
4. Watch the file transfer peer-to-peer!

---

## 🧪 Running Tests

**Client (Vitest):**
```bash
cd client
npm test
```

**Server (Jest):**
```bash
cd server
npm test
```

Tests cover:
- `chunker.ts` — file chunking logic
- `hasher.ts` — SHA-256 hashing
- `format.ts` — file size formatting utilities
- `useTransferProgress` hook
- `FileDropZone` and `TransferProgressBar` components
- `roomStore` — room lifecycle logic
- Socket `handlers` — signaling event handling
- `validation` — room/socket input validation

---

## 📦 Project Structure

```
P2Pproject/
├── client/                        # React frontend (deployed on Vercel)
│   ├── src/
│   │   ├── components/            # UI components (panels, progress bar, drop zone…)
│   │   ├── pages/                 # Route-level pages (Home, Room, 404…)
│   │   ├── hooks/                 # Custom React hooks (WebRTC, socket, room, transfer)
│   │   ├── store/                 # Zustand state slices (file, room, transfer)
│   │   ├── lib/                   # Core utilities (chunker, hasher, downloader…)
│   │   ├── utils/                 # Formatting & feature detection helpers
│   │   └── types/                 # Shared TypeScript types
│   ├── .env.example
│   ├── vite.config.ts
│   ├── tailwind.config.js
│   └── vercel.json
│
└── server/                        # Node.js signaling server (deployed on Render)
    ├── src/
    │   ├── index.ts               # Entry point
    │   ├── config.ts              # Environment config & validation
    │   ├── middleware/security.ts # Helmet + rate limiting
    │   ├── routes/health.ts       # Health check endpoint
    │   ├── socket/
    │   │   ├── handlers.ts        # Socket.io event handlers (signaling)
    │   │   ├── roomStore.ts       # In-memory room state management
    │   │   └── validation.ts      # Input validation for socket events
    │   └── types/index.ts         # Server-side TypeScript types
    ├── .env.example
    └── render.yaml
```

---

## ☁️ Deployment

### Frontend — Vercel

The `client/vercel.json` is pre-configured for React SPA routing (all paths fallback to `index.html`).

1. Import the repository into [Vercel](https://vercel.com).
2. Set the **Root Directory** to `client`.
3. Add the environment variable:
   ```
  VITE_SOCKET_URL=https://p2pdatashare-3.onrender.com
   ```
4. Deploy — Vercel handles the build automatically.

### Backend — Render

The `server/render.yaml` is pre-configured for zero-config deployment.

1. Import the repository into [Render](https://render.com).
2. Set the **Root Directory** to `server`.
3. Add the environment variable:
   ```
  CORS_ORIGIN=https://p2-pdatashare.vercel.app
   ```
4. Deploy — Render runs `npm run build && npm start`.

---

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **ISC License**.

---

<div align="center">
  <strong>Built with ❤️ using WebRTC, React, and Node.js</strong><br/>
  <a href="https://p2-pproject.vercel.app/">🔗 Try it live → p2-pproject.vercel.app</a>
</div>
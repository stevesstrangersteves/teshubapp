# Server-Side License Verification System

## Overview

This document describes the server-side license verification system for the ElevenLabs Desktop App. The system uses a dedicated server for license key validation so the private key never ships with the client.

## Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Client App    │────▶│  License Server │────▶│  (Optional)     │
│  (Electron)     │◀────│  (Node.js)      │◀────│  Database       │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │
        │ 1. Activate key       │
        │──────────────────────▶│
        │                       │ 2. Verify with private key
        │                       │───────────────┐
        │                       │               │
        │                       │◀──────────────┘
        │ 3. Return session token
        │◀──────────────────────│
        │
        │ 4. Periodic validation (every 24h)
        │──────────────────────▶│
```

## Security Improvements

| Threat | Before (Client-Side) | After (Server-Side) |
|--------|---------------------|---------------------|
| Private key extraction | **HIGH** - Key in client code | **NONE** - Key never ships |
| Verification bypass | **HIGH** - Patch local function | **LOW** - Requires server access |
| License generation | **HIGH** - Extract key, generate | **NONE** - Server only |
| Replay attacks | **MEDIUM** - No expiration | **LOW** - Session tokens |
| Offline exploitation | **N/A** - Full access | **LOW** - Grace period only |

## Project Structure

The license server is now in a dedicated folder for easy maintenance and deployment:

```
elevenlabs-app/
├── server/                    # ← Dedicated server folder
│   ├── src/
│   │   ├── index.ts          # Main server code
│   │   └── keygen.ts         # Key pair generator
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env.example
│   └── README.md
├── electron/
│   ├── license-api.ts        # Client HTTP library
│   ├── license-manager.ts    # Client license manager
│   └── ...
└── docs/
    └── SERVER_SIDE_LICENSE.md
```

## Server Quick Start

See `server/README.md` for complete deployment instructions.

```bash
cd server

# Install dependencies
npm install

# Generate keypair
npm run generate-keypair

# Run development server
npm run dev
```

## Client Configuration

Set the server URL via environment variable:

```bash
export LICENSE_SERVER_URL=https://license.yourdomain.com
```

Or in the app: Settings → License → Server URL

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/api/license/verify` | POST | Verify license key |
| `/api/license/validate-session` | POST | Validate session token |
| `/api/license/revoke` | POST | Revoke license (admin) |
| `/api/license/stats` | GET | Server stats (admin) |
| `/api/license/public-key` | GET | Get public key |

## Deployment Options

- **Railway** - Easy Node.js hosting
- **Cloudflare Workers** - Serverless deployment
- **VPS** - Full control with systemd
- **Docker** - Container deployment

See `server/README.md` for detailed deployment guides.

## Files Reference

### Server Files
- `server/src/index.ts` - Main server implementation
- `server/src/keygen.ts` - Key pair generator CLI
- `server/README.md` - Complete server documentation

### Client Files
- `electron/license-api.ts` - HTTP client for server communication
- `electron/license-manager.ts` - License manager with offline support
- `electron/license-keygen-standalone.ts` - Development-only key generator

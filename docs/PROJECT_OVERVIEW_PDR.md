# TeSHub Desktop - Project Overview (PDR)

## Project Summary

**Project Name:** TeSHub Desktop
**Version:** 1.0.0
**Type:** Cross-platform Desktop Application
**Framework:** Electron + React + TypeScript + Vite
**Package Manager:** Bun

**Description:** A feature-rich desktop application for TeSHub AI voice services, providing Text-to-Speech (TTS), Speech-to-Text (STT), Sound Effects generation, and Music composition with advanced API key pool management.

---

## Table of Contents

1. [Core Features](#core-features)
2. [Technical Architecture](#technical-architecture)
3. [Project Structure](#project-structure)
4. [Key Components](#key-components)
5. [State Management](#state-management)
6. [API Integration](#api-integration)
7. [Development Setup](#development-setup)
8. [Build & Deployment](#build--deployment)

---

## Core Features

### 1. Text-to-Speech (TTS)
- Convert text to lifelike speech using TeSHub voices
- Adjustable voice settings: stability, similarity, style, speed
- Speaker boost enhancement
- Support for multiple voices and models
- Character limit: 5,000 characters per request

**File:** `src/pages/TextToSpeech.tsx`

### 2. Parallel TTS
- Process multiple sentences simultaneously
- Per-sentence voice settings
- Automatic API key rotation for parallel requests
- Efficient batch processing

**File:** `src/pages/ParallelTTS.tsx`

### 3. Speech-to-Text (STT)
- Transcribe audio to text
- Speaker diarization support
- Language detection and specification
- Word-level timestamps
- Keyterms for domain-specific vocabulary

**File:** `src/pages/SpeechToText.tsx`

### 4. Sound Effects
- Generate sound effects from text descriptions
- Configurable duration
- Loop support for seamless playback
- Prompt influence tuning

**File:** `src/pages/SoundEffects.tsx`

### 5. Music Generation
- Compose instrumental music from prompts
- Configurable music length
- AI-powered composition

**File:** `src/pages/Music.tsx`

### 6. API Key Pool Manager ⭐
- **Multi-key management** - Add, remove, and organize multiple API keys
- **Usage tracking** - Monitor character usage per key
- **Automatic rotation** - Distribute requests across keys
- **Selection strategies:**
  - Least-used (percentage-based)
  - Round-robin
  - Random
- **Health monitoring** - Auto-detect exhausted or invalid keys
- **Quota management** - Track limits and reset dates
- **Bulk import** - Import keys from CSV or Excel files
- **Encrypted storage** - AES-256-GCM encryption for key security
- **Usage history** - Historical snapshots of key usage

**File:** `electron/key-pool.ts`, `src/pages/KeyManager.tsx`

---

## Technical Architecture

### Application Stack

```
┌─────────────────────────────────────────────────────────────┐
│                    TeSHub Desktop                        │
├─────────────────────────────────────────────────────────────┤
│  Frontend (Renderer Process)                                │
│  ├─ React 18.3.1                                            │
│  ├─ TypeScript 5.5.4                                        │
│  ├─ Vite 5.4.0 (Build tool)                                 │
│  ├─ React Router 6.26.0                                     │
│  ├─ Zustand 4.5.4 (State management)                        │
│  ├─ Tailwind CSS 3.4.10                                     │
│  └─ Radix UI Components                                     │
├─────────────────────────────────────────────────────────────┤
│  Backend (Main Process)                                     │
│  ├─ Electron 31.3.1                                         │
│  ├─ Node.js                                                 │
│  ├─ ElevenLabs SDK 2.21.0                                   │
│  └─ Key Pool Manager (Custom)                               │
├─────────────────────────────────────────────────────────────┤
│  IPC Communication                                          │
│  └─ Secure context-isolated messaging                       │
└─────────────────────────────────────────────────────────────┘
```

### Process Architecture

```
┌─────────────────┐         ┌─────────────────┐
│  Main Process   │◄───────►│  Renderer       │
│  (Electron)     │   IPC   │  (React App)    │
│                 │         │                 │
│  • Window mgmt  │         │  • UI rendering │
│  • IPC handlers │         │  • User input   │
│  • Key pool     │         │  • Audio player │
│  • API calls    │         │  • State mgmt   │
└─────────────────┘         └─────────────────┘
         │
         ▼
┌─────────────────┐
│  ElevenLabs API │
│  (Cloud)        │
└─────────────────┘
```

---

## Project Structure

```
teshub-desktop/
├── electron/                    # Main process code
│   ├── main.ts                  # Electron entry point, IPC handlers
│   ├── key-pool.ts              # API key management system
│   └── preload.ts               # Preload script (context bridge)
│
├── src/                         # Renderer process (React app)
│   ├── App.tsx                  # Root component with routing
│   ├── main.tsx                 # React entry point
│   ├── types/
│   │   └── electron.d.ts        # TypeScript definitions
│   ├── components/              # Reusable UI components
│   │   ├── AudioPlayer.tsx      # Audio playback component
│   │   ├── KeyUsageChart.tsx    # Usage visualization
│   │   ├── SliderControl.tsx    # Settings slider
│   │   ├── ModelSelector.tsx    # Model dropdown
│   │   ├── VoiceSelector.tsx    # Voice dropdown
│   │   └── Sidebar.tsx          # Navigation sidebar
│   ├── pages/                   # Page components
│   │   ├── TextToSpeech.tsx     # TTS page
│   │   ├── ParallelTTS.tsx      # Parallel TTS page
│   │   ├── SpeechToText.tsx     # STT page
│   │   ├── SoundEffects.tsx     # SFX page
│   │   ├── Music.tsx            # Music generation page
│   │   └── KeyManager.tsx       # API key management page
│   └── store/                   # Zustand stores
│       └── keyPoolStore.ts      # Key pool state
│
├── docs/                        # Documentation
│   ├── CI_CD_INDEX.md           # CI/CD documentation index
│   ├── CI_CD_PIPELINE_FLOW.md   # Pipeline flow diagrams
│   ├── CODE_SIGNING_SETUP.md    # Code signing guide
│   ├── GITHUB_ACTIONS_QUICKSTART.md  # Quick start guide
│   └── PROJECT_OVERVIEW_PDR.md  # This file
│
├── .github/                     # GitHub configuration
│   └── workflows/               # GitHub Actions workflows
│       ├── build.yml            # Build & release workflow
│       ├── ci.yml               # CI quality checks
│       └── README.md            # Workflow documentation
│
├── assets/                      # Application assets
│   ├── icon.icns                # macOS icon
│   ├── icon.ico                 # Windows icon
│   ├── icon.png                 # Linux icon
│   └── tray-icon.png            # System tray icon
│
├── .claude/                     # Claude configuration
│   ├── rules/                   # Development rules
│   └── skills/                  # Claude skills
│
├── package.json                 # Dependencies & scripts
├── tsconfig.json                # TypeScript config
├── vite.config.ts               # Vite configuration
├── electron-builder.yml         # Electron builder config
├── tailwind.config.js           # Tailwind config
├── postcss.config.js            # PostCSS config
└── bun.lock                     # Bun lockfile
```

---

## Key Components

### Main Process (`electron/main.ts`)

**Responsibilities:**
- Window creation and management
- System tray integration
- IPC handler registration
- API key rotation logic
- ElevenLabs SDK client caching

**IPC Handlers:**
| Handler | Purpose |
|---------|---------|
| `tts:synthesize` | Text-to-speech with key rotation |
| `tts:parallel-sentences` | Parallel TTS processing |
| `stt:transcribe` | Speech-to-text transcription |
| `sfx:generate` | Sound effects generation |
| `music:generate` | Music composition |
| `keys:*` | Key management operations |
| `voices:list` | Fetch available voices |

### Key Pool Manager (`electron/key-pool.ts`)

**Features:**
- AES-256-GCM encrypted storage
- Machine-specific encryption key derivation
- Usage percentage tracking
- Automatic key health monitoring
- Error handling and retry logic

**Key Statuses:**
- `active` - Ready for use
- `exhausted` - Quota reached (95%+ used)
- `error` - Authentication or API errors
- `disabled` - Manually disabled

**Selection Strategies:**
```typescript
type SelectionStrategy =
  | 'least-used'      // Select by lowest usage percentage
  | 'round-robin'     // Rotate through keys sequentially
  | 'random'          // Random selection from active keys
```

### Renderer Components

#### Sidebar (`src/components/Sidebar.tsx`)
- Navigation menu
- Route switching
- App branding

#### AudioPlayer (`src/components/AudioPlayer.tsx`)
- Audio playback from ArrayBuffer
- Download functionality
- Player controls

#### VoiceSelector (`src/components/VoiceSelector.tsx`)
- Fetch and display available voices
- Voice preview capability
- Selection dropdown

#### ModelSelector (`src/components/ModelSelector.tsx`)
- Model selection (e.g., `eleven_multilingual_v2`)
- Model information display

#### SliderControl (`src/components/SliderControl.tsx`)
- Reusable slider component
- Configurable min/max/step
- Label display

#### KeyUsageChart (`src/components/KeyUsageChart.tsx`)
- Visualize API key usage
- Usage history graphs
- Recharts-based charts

---

## State Management

### Key Pool Store (`src/store/keyPoolStore.ts`)

**Zustand Store Structure:**
```typescript
interface KeyPoolState {
  keys: ApiKeyPublic[]      // List of API keys
  strategy: string          // Current selection strategy
  loading: boolean          // Loading state

  // Actions
  fetchKeys: () => Promise<void>
  addKey: (key, label) => Promise<void>
  removeKey: (id) => Promise<void>
  syncAll: () => Promise<void>
  setStrategy: (strategy) => Promise<void>
}
```

### Component State

Each page manages its own local state using React's `useState`:
- Form inputs (text, settings)
- Loading states
- Error states
- Audio buffer

---

## API Integration

### ElevenLabs SDK

**Client:** `@elevenlabs/elevenlabs-js` v2.21.0

**Usage Pattern:**
```typescript
const client = new ElevenLabsClient({ apiKey })

// Text-to-Speech
const stream = await client.textToSpeech.convert(voiceId, {
  text,
  modelId,
  outputFormat,
  voiceSettings
})

// Speech-to-Text
const result = await client.speechToText.convert({
  file,
  modelId,
  diarize,
  languageCode
})

// Sound Effects
const stream = await client.textToSoundEffects.convert({
  text,
  durationSeconds,
  loop
})

// Music
const stream = await client.music.compose({
  prompt,
  musicLengthMs
})
```

### API Endpoints (Direct)

Some operations use direct HTTP calls:
- `/v1/voices` - List voices
- `/v1/user/subscription` - Get usage stats

---

## Development Setup

### Prerequisites

- **Node.js:** v20+ (via Bun)
- **Bun:** v1.1.38+
- **Git:** For version control

### Installation

```bash
# Clone repository
git clone <repository-url>
cd teshub-desktop

# Install dependencies
bun install

# Start development
bun run dev
```

### Development Scripts

| Script | Command | Description |
|--------|---------|-------------|
| Dev | `bun run dev` | Run Vite + Electron |
| Dev Vite | `bun run dev:vite` | Run Vite dev server only |
| Dev Electron | `bun run dev:electron` | Run Electron (waits for Vite) |
| Build | `bun run build` | TypeScript compile + Vite build |
| Build (macOS) | `bun run electron:build:mac` | Build macOS app |
| Build (Windows) | `bun run electron:build:win` | Build Windows installer |
| Build (Linux) | `bun run electron:build:linux` | Build Linux packages |

### Environment

- **Development:** `NODE_ENV=development`
  - Loads from `http://localhost:5173`
  - DevTools enabled
- **Production:** `NODE_ENV=production`
  - Loads from `dist/index.html`

---

## Build & Deployment

### Build Configuration

**electron-builder.yml:**
```yaml
appId: com.teshub-desktop.app
productName: TeSHub Desktop

mac:
  target: dmg (x64, arm64)
  hardenedRuntime: true

win:
  target: nsis (x64)

linux:
  target: AppImage, deb
```

### CI/CD Pipeline

**GitHub Actions Workflows:**

1. **CI Workflow** (`ci.yml`)
   - Trigger: Pull requests
   - Type check, lint, test, build verify
   - Runtime: 5-10 minutes

2. **Build Workflow** (`build.yml`)
   - Trigger: Push to main, version tags
   - Parallel builds for macOS, Windows, Linux
   - Auto-release on version tags
   - Runtime: 15-25 minutes

### Release Process

```bash
# 1. Update version in package.json
# 2. Tag release
git tag v1.0.0
git push origin v1.0.0

# 3. GitHub Actions builds all platforms
# 4. Release created automatically with artifacts
```

### Distribution

- **GitHub Releases:** Primary distribution channel
- **Artifacts:** `.dmg`, `.exe`, `.AppImage`, `.deb`
- **Retention:** 14 days for CI builds, permanent for releases

---

## Security

### API Key Storage

- **Encryption:** AES-256-GCM
- **Key Derivation:** scrypt with machine-specific salt
- **Storage:** Encrypted JSON in user data directory
- **Exposure:** Keys never sent to renderer (masked display only)

### Electron Security

- **Context Isolation:** Enabled
- **Node Integration:** Disabled in renderer
- **Sandbox:** Enabled
- **Preload Script:** Secure context bridge

---

## Related Documentation

- [CI/CD Index](./CI_CD_INDEX.md) - Complete CI/CD documentation
- [Code Signing Setup](./CODE_SIGNING_SETUP.md) - Certificate procurement
- [Pipeline Flow](./CI_CD_PIPELINE_FLOW.md) - Visual workflow diagrams
- [Quick Start Guide](./GITHUB_ACTIONS_QUICKSTART.md) - 5-minute setup

---

**Last Updated:** 2026-03-03
**Maintained By:** Development Team

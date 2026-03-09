# Codebase Summary

Quick reference guide to the TeSHub Desktop codebase structure and key files.

---

## Quick Navigation

| Category | Location | Description |
|----------|----------|-------------|
| **Entry Points** | `electron/main.ts`, `src/main.tsx` | App startup |
| **Pages** | `src/pages/` | All UI pages |
| **Components** | `src/components/` | Reusable UI |
| **State** | `src/store/` | Zustand stores |
| **Types** | `src/types/` | TypeScript defs |
| **Key Logic** | `electron/key-pool.ts` | API key management |
| **Config** | `package.json`, `vite.config.ts` | Build config |
| **Docs** | `docs/` | Documentation |

---

## File Tree (Simplified)

```
teshub-desktop/
│
├── 📄 package.json                 # Dependencies & scripts
├── 📄 tsconfig.json                # TypeScript config
├── 📄 vite.config.ts               # Vite bundler config
├── 📄 electron-builder.yml         # Electron build config
├── 📄 bun.lock                     # Bun lockfile
│
├── 📁 electron/                    # Main process (Node.js)
│   ├── main.ts                     # ⭐ Entry point, IPC handlers
│   ├── key-pool.ts                 # ⭐ API key pool manager
│   └── preload.ts                  # Context bridge
│
├── 📁 src/                         # Renderer process (React)
│   ├── main.tsx                    # React entry
│   ├── App.tsx                     # ⭐ Root component, routing
│   │
│   ├── 📁 pages/                   # Page components
│   │   ├── TextToSpeech.tsx        # TTS page
│   │   ├── ParallelTTS.tsx         # Parallel TTS
│   │   ├── SpeechToText.tsx        # STT page
│   │   ├── SoundEffects.tsx        # SFX generation
│   │   ├── Music.tsx               # Music generation
│   │   └── KeyManager.tsx          # API key management
│   │
│   ├── 📁 components/              # Reusable components
│   │   ├── AudioPlayer.tsx         # Audio playback
│   │   ├── VoiceSelector.tsx       # Voice dropdown
│   │   ├── ModelSelector.tsx       # Model dropdown
│   │   ├── SliderControl.tsx       # Settings slider
│   │   ├── KeyUsageChart.tsx       # Usage charts
│   │   └── Sidebar.tsx             # Navigation
│   │
│   ├── 📁 store/                   # Zustand stores
│   │   └── keyPoolStore.ts         # Key pool state
│   │
│   └── 📁 types/                   # TypeScript types
│       └── electron.d.ts           # IPC type defs
│
├── 📁 docs/                        # Documentation
│   ├── PROJECT_OVERVIEW_PDR.md     # ⭐ Project overview
│   ├── CODE_STANDARDS.md           # Coding standards
│   ├── SYSTEM_ARCHITECTURE.md      # Architecture diagrams
│   ├── CI_CD_INDEX.md              # CI/CD docs index
│   ├── CI_CD_PIPELINE_FLOW.md      # Pipeline flow
│   ├── CODE_SIGNING_SETUP.md       # Code signing guide
│   └── GITHUB_ACTIONS_QUICKSTART.md # Quick start
│
├── 📁 .github/workflows/           # GitHub Actions
│   ├── build.yml                   # Build & release
│   ├── ci.yml                      # CI quality checks
│   └── README.md                   # Workflow docs
│
└── 📁 assets/                      # App icons & resources
    ├── icon.icns                   # macOS icon
    ├── icon.ico                    # Windows icon
    ├── icon.png                    # Linux icon
    └── tray-icon.png               # Tray icon
```

---

## Key Files Explained

### Core Application Files

| File | Lines | Purpose |
|------|-------|---------|
| `electron/main.ts` | ~400 | Main process, IPC handlers, TTS/STT/SFX/music logic |
| `electron/key-pool.ts` | ~570 | API key management, encryption, rotation |
| `electron/preload.ts` | ~50 | Context bridge for IPC |
| `src/App.tsx` | ~30 | Router setup, page navigation |
| `src/main.tsx` | ~20 | React DOM render |

### Page Files

| File | Purpose | Key Features |
|------|---------|--------------|
| `src/pages/TextToSpeech.tsx` | TTS generation | Voice/model selection, settings |
| `src/pages/ParallelTTS.tsx` | Batch TTS | Multiple sentences, per-sentence settings |
| `src/pages/SpeechToText.tsx` | Audio transcription | File upload, diarization |
| `src/pages/SoundEffects.tsx` | SFX generation | Text prompts, duration control |
| `src/pages/Music.tsx` | Music composition | AI music generation |
| `src/pages/KeyManager.tsx` | API key management | Add/remove keys, usage stats, import |

### Component Files

| File | Purpose |
|------|---------|
| `src/components/Sidebar.tsx` | Navigation menu |
| `src/components/VoiceSelector.tsx` | Voice selection dropdown |
| `src/components/ModelSelector.tsx` | Model selection |
| `src/components/SliderControl.tsx` | Reusable slider input |
| `src/components/AudioPlayer.tsx` | Audio playback & download |
| `src/components/KeyUsageChart.tsx` | Usage visualization |

### Store Files

| File | Purpose |
|------|---------|
| `src/store/keyPoolStore.ts` | Global state for API keys |

---

## Dependencies

### Production

```json
{
  "@elevenlabs/elevenlabs-js": "^2.21.0",  // ElevenLabs SDK
  "react": "^18.3.1",                       // UI framework
  "react-dom": "^18.3.1",
  "react-router-dom": "^6.26.0",            // Routing
  "zustand": "^4.5.4",                      // State management
  "@radix-ui/*":                            // UI components
  "lucide-react": "^0.441.0",               // Icons
  "recharts": "^3.7.0",                     // Charts
  "papaparse": "^5.4.1",                    // CSV parsing
  "exceljs": "^4.4.0",                      // Excel parsing
  "clsx": "^2.1.1"                          // Class names
}
```

### Development

```json
{
  "electron": "^31.3.1",                    // Desktop framework
  "electron-builder": "^24.13.3",           // Build tool
  "vite": "^5.4.0",                         // Bundler
  "vite-plugin-electron": "^0.28.7",        // Vite + Electron
  "typescript": "^5.5.4",                   // TypeScript
  "tailwindcss": "^3.4.10",                 // CSS framework
  "concurrently": "^8.2.2"                  // Parallel commands
}
```

---

## Scripts Reference

```bash
# Development
bun run dev                    # Start Vite + Electron
bun run dev:vite              # Start Vite only
bun run dev:electron          # Start Electron only

# Building
bun run build                 # TypeScript + Vite build
bun run electron:build        # Build for current platform
bun run electron:build:mac    # Build macOS
bun run electron:build:win    # Build Windows
bun run electron:build:linux  # Build Linux

# Type checking
bunx tsc                      # TypeScript compile
bunx tsc --noEmit             # Type check only
```

---

## IPC API Reference

### Main → Renderer Events

None currently (all communication is request/response)

### Renderer → Main IPC Calls

```typescript
// TTS
window.elevenlabs.synthesize({ text, voiceId, modelId, settings })

// Parallel TTS
window.elevenlabs.parallelSentences({ sentences })

// STT
window.elevenlabs.transcribe({ audioBuffer, modelId, languageCode })

// Sound Effects
window.elevenlabs.generateSFX({ text, durationSeconds })

// Music
window.elevenlabs.generateMusic({ prompt, musicLengthMs })

// Keys
window.elevenlabs.keys.list()
window.elevenlabs.keys.add(key, label)
window.elevenlabs.keys.remove(id)
window.elevenlabs.keys.sync()
window.elevenlabs.keys.setStrategy(strategy)
window.elevenlabs.keys.importFile()
window.elevenlabs.keys.healthCheck()
window.elevenlabs.keys.getUsageHistory(keyId)

// Voices
window.elevenlabs.voices.list()
```

---

## Data Models

### ApiKey

```typescript
interface ApiKey {
  id: string
  key: string                    // Encrypted in storage
  label: string
  status: 'active' | 'exhausted' | 'error' | 'disabled'
  usedCharacters: number
  charLimit: number
  requestCount: number
  lastUsed: Date | null
  errorCount: number
  resetDate: Date | null
  addedAt: Date
  usageHistory: UsageSnapshot[]
}
```

### ApiKeyPublic (Renderer-safe)

```typescript
interface ApiKeyPublic {
  id: string
  keyMasked: string              // "sk_...1234"
  label: string
  status: KeyStatus
  usedCharacters: number
  charLimit: number
  requestCount: number
  lastUsed: Date | null
  errorCount: number
  resetDate: Date | null
  addedAt: Date
  usageHistory: UsageSnapshot[]
}
```

### UsageSnapshot

```typescript
interface UsageSnapshot {
  timestamp: Date
  usedCharacters: number
  charLimit: number
  requestCount: number
}
```

---

## Configuration Files

### package.json

```json
{
  "name": "teshub-desktop",
  "version": "1.0.0",
  "main": "dist-electron/main.js",
  "scripts": { ... }
}
```

### electron-builder.yml

```yaml
appId: com.teshub-desktop.app
productName: TeSHub Desktop
directories:
  output: release
mac:
  target: dmg
win:
  target: nsis
linux:
  target: [AppImage, deb]
```

### vite.config.ts

```typescript
export default defineConfig({
  plugins: [
    react(),
    electron([/* main, preload */]),
    renderer()
  ]
})
```

---

## Environment Variables

| Variable | Purpose | Values |
|----------|---------|--------|
| `NODE_ENV` | Build mode | `development`, `production` |

---

## Build Outputs

### Development

```
dist/                    # Vite build output
dist-electron/           # Electron main process
```

### Production

```
release/
├── ElevenLabs-Desktop-1.0.0.dmg      # macOS
├── ElevenLabs-Desktop-Setup-1.0.0.exe # Windows
├── ElevenLabs-Desktop-1.0.0.AppImage  # Linux
└── teshub-desktop_1.0.0_amd64.deb # Linux Debian
```

---

## Testing

No tests currently configured. Recommended additions:

```bash
# Add to package.json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage"
  }
}
```

---

## Related Documentation

- [Project Overview](./PROJECT_OVERVIEW_PDR.md) - Full project description
- [Code Standards](./CODE_STANDARDS.md) - Coding guidelines
- [System Architecture](./SYSTEM_ARCHITECTURE.md) - Architecture diagrams
- [CI/CD Index](./CI_CD_INDEX.md) - Build pipeline docs

---

**Last Updated:** 2026-03-03
**Maintained By:** Development Team

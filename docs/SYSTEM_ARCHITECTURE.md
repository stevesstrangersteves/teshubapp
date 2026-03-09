# System Architecture

Detailed architectural documentation for the TeSHub Desktop application.

---

## Table of Contents

1. [High-Level Architecture](#high-level-architecture)
2. [Electron Architecture](#electron-architecture)
3. [Data Flow](#data-flow)
4. [Component Architecture](#component-architecture)
5. [State Architecture](#state-architecture)
6. [Security Architecture](#security-architecture)
7. [API Architecture](#api-architecture)
8. [Build Architecture](#build-architecture)

---

## High-Level Architecture

### System Context Diagram

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         TeSHub Desktop                                │
│  Cross-platform desktop application for TeSHub AI voice services    │
└──────────────────────────────────────────────────────────────────────────┘
           │                                    │
           │ 1. User interacts with UI         │
           │ 2. IPC requests to Main process   │
           ▼ 3. API calls to ElevenLabs        ▼
┌─────────────────┐                    ┌─────────────────┐
│     User        │                    │  ElevenLabs API │
│                 │                    │  (Cloud)        │
└─────────────────┘                    └─────────────────┘
           │                                    │
           │ 4. Audio playback                  │ 5. Streaming response
           ▼                                    ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         Local Resources                                   │
│  • Encrypted key store  • Audio cache  • Configuration files             │
└──────────────────────────────────────────────────────────────────────────┘
```

### Container Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                     TeSHub Desktop Container                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌──────────────────────┐          ┌──────────────────────┐         │
│  │  Main Process        │          │  Renderer Process    │         │
│  │  (Electron)          │◄────────►│  (React App)         │         │
│  │                      │   IPC    │                      │         │
│  │  • Window mgmt       │          │  • UI components     │         │
│  │  • IPC handlers      │          │  • State management  │         │
│  │  • Key pool manager  │          │  • Audio playback    │         │
│  │  • API client cache  │          │  • User input        │         │
│  └──────────────────────┘          └──────────────────────┘         │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                    Persistent Storage                         │   │
│  │  • keypool.enc.json (AES-256-GCM encrypted)                  │   │
│  │  • Electron preferences                                       │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Electron Architecture

### Process Model

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Electron Application                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Main Process (Node.js)                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  electron/main.ts                                            │   │
│  │  ├─ BrowserWindow creation                                   │   │
│  │  ├─ System tray management                                   │   │
│  │  ├─ IPC handler registration                                 │   │
│  │  │  ├─ tts:synthesize                                       │   │
│  │  │  ├─ tts:parallel-sentences                               │   │
│  │  │  ├─ stt:transcribe                                       │   │
│  │  │  ├─ sfx:generate                                         │   │
│  │  │  ├─ music:generate                                       │   │
│  │  │  └─ keys:* ( CRUD operations)                             │   │
│  │  └─ KeyPoolManager                                           │   │
│  │     ├─ Key selection strategies                              │   │
│  │     ├─ Usage tracking                                        │   │
│  │     ├─ Error handling & rotation                             │   │
│  │     └─ Encrypted storage                                     │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  Renderer Process (Chromium)                                        │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  React Application                                           │   │
│  │  ├─ App.tsx (Router)                                         │   │
│  │  ├─ Pages                                                    │   │
│  │  │  ├─ TextToSpeech                                          │   │
│  │  │  ├─ ParallelTTS                                           │   │
│  │  │  ├─ SpeechToText                                          │   │
│  │  │  ├─ SoundEffects                                          │   │
│  │  │  ├─ Music                                                 │   │
│  │  │  └─ KeyManager                                            │   │
│  │  └─ Components                                               │   │
│  │     ├─ VoiceSelector                                         │   │
│  │     ├─ ModelSelector                                         │   │
│  │     ├─ SliderControl                                         │   │
│  │     ├─ AudioPlayer                                           │   │
│  │     └─ KeyUsageChart                                         │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  Preload Script (Isolated)                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  electron/preload.ts                                         │   │
│  │  └─ contextBridge.exposeInMainWorld('elevenlabs', ...)       │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### IPC Communication Flow

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   Renderer   │      │    Preload   │      │     Main     │
│  (React UI)  │      │   (Bridge)   │      │  (Electron)  │
└──────┬───────┘      └──────┬───────┘      └──────┬───────┘
       │                     │                      │
       │  invoke('tts:...)   │                      │
       ├────────────────────►│                      │
       │                     │  ipcMain.handle      │
       │                     ├─────────────────────►│
       │                     │                      │
       │                     │                      │  KeyPool
       │                     │                      │  Select Key
       │                     │                      ├───────┐
       │                     │                      │       │
       │                     │                      │◄──────┘
       │                     │                      │
       │                     │                      │  ElevenLabs
       │                     │                      │  API Call
       │                     │                      ├───────┐
       │                     │                      │       │
       │                     │                      │◄──────┘
       │                     │                      │
       │                     │  return audio        │
       │                     ◄──────────────────────┤
       │  resolve promise    │                      │
       │◄────────────────────┤                      │
       │                     │                      │
       ▼                     ▼                      ▼
```

---

## Data Flow

### Text-to-Speech Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        TTS Data Flow                                 │
└─────────────────────────────────────────────────────────────────────┘

1. User Input
   │
   ▼
┌─────────────────┐
│ Text Input      │ "Hello world"
│ Voice Selection │ "21m00Tcm4TlvDq8ikWAM"
│ Settings        │ { stability: 0.5, speed: 1.0, ... }
└────────┬────────┘
         │
         ▼
2. IPC Call
   ┌──────────────────────────────────────────┐
   │ window.elevenlabs.synthesize(params)     │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
3. Main Process Handler
   ┌──────────────────────────────────────────┐
   │ ipcMain.handle('tts:synthesize', ...)    │
   │ 1. Validate inputs                        │
   │ 2. Select API key from pool               │
   │ 3. Call ElevenLabs SDK                    │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
4. Key Selection (Least-Used Strategy)
   ┌──────────────────────────────────────────┐
   │ KeyPoolManager.selectKeyForOperation()   │
   │ - Filter active keys                     │
   │ - Calculate usage %                      │
   │ - Select lowest usage                    │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
5. API Request
   ┌──────────────────────────────────────────┐
   │ client.textToSpeech.convert()            │
   │ POST /v1/text-to-speech/{voice_id}       │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
6. Streaming Response
   ┌──────────────────────────────────────────┐
   │ ReadableStream<Uint8Array>               │
   │ Chunks collected to ArrayBuffer          │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
7. Usage Recording
   ┌──────────────────────────────────────────┐
   │ keyPool.recordUsage(keyId, charsUsed)    │
   │ - Update character count                 │
   │ - Increment request count                │
   │ - Update last used timestamp             │
   │ - Check for quota exhaustion             │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
8. Return to Renderer
   ┌──────────────────────────────────────────┐
   │ Promise.resolve(audioBuffer)             │
   └────────────────┬─────────────────────────┘
                    │
                    ▼
9. Audio Playback
   ┌──────────────────────────────────────────┐
   │ AudioPlayer component                    │
   │ - Create Blob from ArrayBuffer           │
   │ - Play via HTML5 Audio                   │
   │ - Download capability                    │
   └──────────────────────────────────────────┘
```

### Key Pool Rotation Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Key Rotation Flow                                 │
└─────────────────────────────────────────────────────────────────────┘

Request arrives
     │
     ▼
┌─────────────────┐
│ Select best key │ ← Least-used percentage
└────────┬────────┘
         │
         ▼
    ┌─────────────┐
    │ Make API    │
    │ call        │
    └──────┬──────┘
           │
     ┌─────┴─────┐
     │           │
   Success     Error
     │           │
     │           ▼
     │    ┌──────────────┐
     │    │ Error type?  │
     │    └──────┬───────┘
     │           │
     │    ┌──────┼──────┐
     │    │      │      │
     │   429    401   Other
     │ (Quota) (Auth)  │
     │    │      │      │
     │    │      │      ▼
     │    │      │  Mark error
     │    │      │  count++
     │    │      │
     │    ▼      ▼
     │  Mark as 'exhausted' or 'error'
     │  Update store
     │    │
     │    ▼
     │  Any more active keys?
     │    │
     │   Yes ──► Retry with next key
     │    │
     │    No
     │    │
     │    ▼
     │  Throw "All keys exhausted"
     │
     ▼
Record usage
Update statistics
Return result
```

---

## Component Architecture

### Component Hierarchy

```
App
├── Sidebar (Navigation)
│   ├── Logo
│   ├── Nav links
│   └── Status indicator
│
└── Routes
    ├── /tts → TextToSpeech
    │   ├── TextArea
    │   ├── Settings Panel
    │   │   ├── VoiceSelector
    │   │   ├── ModelSelector
    │   │   └── SliderControl (×4)
    │   └── AudioPlayer
    │
    ├── /parallel-tts → ParallelTTS
    │   ├── SentenceList
    │   ├── BulkSettings
    │   └── ResultsGrid
    │
    ├── /stt → SpeechToText
    │   ├── AudioRecorder
    │   ├── FileUploader
    │   └── TranscriptionResult
    │
    ├── /sfx → SoundEffects
    │   ├── PromptInput
    │   ├── SFXSettings
    │   └── AudioPlayer
    │
    ├── /music → Music
    │   ├── PromptInput
    │   ├── MusicSettings
    │   └── AudioPlayer
    │
    └── /keys → KeyManager
        ├── KeyList
        ├── KeyUsageChart
        ├── ImportExport
        └── HealthStatus
```

### Component Communication

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Component Communication                           │
└─────────────────────────────────────────────────────────────────────┘

Parent → Child: Props
┌──────────────┐
│  Parent      │
│  Component   │
└──────┬───────┘
       │ props
       ▼
┌──────────────┐
│   Child      │
│  Component   │
└──────────────┘

Child → Parent: Callback functions
┌──────────────┐
│  Parent      │
│  (onChange)  │
└──────▲───────┘
       │ callback
       │
┌──────────────┐
│   Child      │
│ (calls fn)   │
└──────────────┘

Global State: Zustand Store
┌──────────────────────────────┐
│   KeyPoolStore               │
│   ├─ keys: ApiKey[]          │
│   ├─ strategy: string        │
│   └─ actions                 │
└──────────────────────────────┘
       ▲          ▲
       │          │
┌──────┴───┐  ┌───┴────────┐
│Component1│  │ Component2 │
└──────────┘  └────────────┘
```

---

## State Architecture

### Global State (Zustand)

```typescript
// src/store/keyPoolStore.ts

interface KeyPoolState {
  // Data
  keys: ApiKeyPublic[]
  strategy: string
  loading: boolean

  // Actions
  fetchKeys: () => Promise<void>
  addKey: (key: string, label: string) => Promise<void>
  removeKey: (id: string) => Promise<void>
  syncAll: () => Promise<void>
  setStrategy: (strategy: string) => Promise<void>
}

// Store creation
export const useKeyPoolStore = create<KeyPoolState>((set) => ({
  keys: [],
  strategy: 'least-used',
  loading: false,

  fetchKeys: async () => {
    set({ loading: true })
    const keys = await window.elevenlabs.keys.list()
    set({ keys, loading: false })
  },

  // ... other actions
}))
```

### Local State (React)

```typescript
// src/pages/TextToSpeech.tsx

interface TextToSpeechState {
  text: string
  voiceId: string
  modelId: string
  speed: number
  stability: number
  similarity: number
  style: number
  speakerBoost: boolean
  audioBuffer: ArrayBuffer | null
  generating: boolean
  error: string | null
}

// Individual state slices with useState
const [text, setText] = useState('')
const [voiceId, setVoiceId] = useState('21m00Tcm4TlvDq8ikWAM')
const [generating, setGenerating] = useState(false)
```

### State Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        State Management                              │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────┐         ┌─────────────────┐
│  Global State   │         │   Local State   │
│  (Zustand)      │         │   (useState)    │
├─────────────────┤         ├─────────────────┤
│ • API keys      │         │ • Form inputs   │
│ • Strategy      │         │ • UI state      │
│ • Loading       │         │ • Errors        │
└────────┬────────┘         └─────────────────┘
         │
         │ Selectors
         ▼
┌─────────────────────────────────────────────────┐
│              React Components                    │
│                                                  │
│  ┌───────────────┐      ┌───────────────────┐  │
│  │ KeyManager    │      │ TextToSpeech      │  │
│  │ uses: keys    │      │ uses: local state │  │
│  └───────────────┘      └───────────────────┘  │
└─────────────────────────────────────────────────┘
```

---

## Security Architecture

### Encryption Layer

```
┌─────────────────────────────────────────────────────────────────────┐
│                    API Key Encryption                                │
└─────────────────────────────────────────────────────────────────────┘

User adds API key
     │
     ▼
┌─────────────────┐
│ sk_abc123...    │  Plain text key
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ AES-256-GCM     │  Encryption algorithm
│ Key: derived    │  scrypt(machine secret)
│ IV: random      │  Per-encryption
│ Tag: auth       │  Integrity verification
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ {"iv":"...",    │  Encrypted JSON
│  "tag":"...",   │
│  "data":"..."}  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ keypool.enc.    │  Written to disk
│ json            │  User data directory
└─────────────────┘
```

### Security Boundaries

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Electron Security Model                           │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────┐         ┌─────────────────────┐
│   Renderer Process  │         │    Main Process     │
│   (Untrusted)       │         │    (Trusted)        │
│                     │         │                     │
│  • No Node.js       │         │  • Full Node.js     │
│  • No direct FS     │         │  • File system      │
│  • No direct API    │         │  • API keys         │
│                     │         │                     │
│  Context Isolation  │         │  IPC Handlers       │
│  Sandbox enabled    │         │  Validation         │
└──────────┬──────────┘         └──────────┬──────────┘
           │                               │
           │         ┌────────────┐        │
           │         │   Preload  │        │
           │         │  (Bridge)  │        │
           │         │  Whitelist │        │
           │         └────────────┘        │
           └───────────────┬───────────────┘
                           │
                    Controlled IPC
```

---

## API Architecture

### ElevenLabs SDK Integration

```
┌─────────────────────────────────────────────────────────────────────┐
│                  ElevenLabs SDK Client Cache                         │
└─────────────────────────────────────────────────────────────────────┘

Client Cache (Map<string, ElevenLabsClient>)
┌───────────────────────────────────────────────────┐
│                                                   │
│  apiKey1 → ElevenLabsClient(apiKey1)             │
│  apiKey2 → ElevenLabsClient(apiKey2)             │
│  apiKey3 → ElevenLabsClient(apiKey3)             │
│                                                   │
│  Benefits:                                       │
│  • Reuse clients across requests                 │
│  • Reduce instantiation overhead                 │
│  • Maintain connection pools                     │
└───────────────────────────────────────────────────┘
```

### API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/v1/text-to-speech/{voice_id}` | POST | Generate speech |
| `/v1/speech-to-text` | POST | Transcribe audio |
| `/v1/text-to-sound-effects` | POST | Generate SFX |
| `/v1/music/compose` | POST | Generate music |
| `/v1/voices` | GET | List voices |
| `/v1/user/subscription` | GET | Get usage stats |

---

## Build Architecture

### Build Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Build Process                                     │
└─────────────────────────────────────────────────────────────────────┘

1. TypeScript Compilation
   ┌─────────────────┐
   │   .ts / .tsx    │
   └────────┬────────┘
            │ tsc
            ▼
   ┌─────────────────┐
   │   Type-checked  │
   │   JavaScript    │
   └────────┬────────┘
            │
            ▼
2. Vite Bundle
   ┌─────────────────┐
   │   Vite Build    │
   │   - Code split  │
   │   - Minify      │
   │   - Optimize    │
   └────────┬────────┘
            │
            ▼
   ┌─────────────────┐
   │   dist/         │
   │   index.html    │
   │   assets/       │
   └────────┬────────┘
            │
            ▼
3. Electron Build
   ┌─────────────────┐
   │   electron-     │
   │   builder       │
   │   - Package     │
   │   - Sign        │
   │   - Notarize    │
   └────────┬────────┘
            │
            ▼
   ┌─────────────────┐
   │   release/      │
   │   .dmg (macOS)  │
   │   .exe (Win)    │
   │   .AppImage     │
   └─────────────────┘
```

### CI/CD Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GitHub Actions Pipeline                           │
└─────────────────────────────────────────────────────────────────────┘

Push to main / Tag v*
         │
         ▼
┌───────────────────────────────────────────────────────────────┐
│                   Parallel Build Jobs                          │
├──────────────────┬──────────────────┬─────────────────────────┤
│   macOS Build    │  Windows Build   │    Linux Build          │
│   macos-latest   │  windows-latest  │    ubuntu-latest        │
│                  │                  │                         │
│   1. Checkout    │  1. Checkout     │    1. Checkout          │
│   2. Setup Bun   │  2. Setup Bun    │    2. Setup Bun         │
│   3. Cache       │  3. Cache        │    3. Cache             │
│   4. Install     │  4. Install      │    4. Install           │
│   5. Build       │  5. Build        │    5. Build             │
│   6. Sign        │  6. Sign         │    6. Package           │
│   7. Upload      │  7. Upload       │    7. Upload            │
└──────────────────┴──────────────────┴─────────────────────────┘
         │                   │                    │
         └───────────────────┼────────────────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │  Version tag?       │
                  └──────────┬──────────┘
                             │
                    ┌────────┴────────┐
                    │                 │
                   Yes               No
                    │                 │
                    ▼                 ▼
           ┌─────────────────┐  ┌─────────────┐
           │  Create Release │  │  Artifacts  │
           │  Attach files   │  │  available  │
           └─────────────────┘  └─────────────┘
```

---

## Related Documentation

- [Project Overview](./PROJECT_OVERVIEW_PDR.md) - Project summary and features
- [Code Standards](./CODE_STANDARDS.md) - Coding guidelines
- [CI/CD Index](./CI_CD_INDEX.md) - Build pipeline documentation

---

**Last Updated:** 2026-03-03
**Maintained By:** Development Team

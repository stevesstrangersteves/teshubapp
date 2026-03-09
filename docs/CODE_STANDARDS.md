# Code Standards

This document defines the coding standards and best practices for the TeSHub Desktop project.

---

## Table of Contents

1. [TypeScript Standards](#typescript-standards)
2. [React Standards](#react-standards)
3. [Electron Standards](#electron-standards)
4. [File Naming Conventions](#file-naming-conventions)
5. [Code Organization](#code-organization)
6. [Error Handling](#error-handling)
7. [Security Standards](#security-standards)
8. [Testing Standards](#testing-standards)
9. [Documentation Standards](#documentation-standards)

---

## TypeScript Standards

### Type Safety

**Always use explicit types for:**
- Function parameters and return types
- Interface and type definitions
- State declarations

```typescript
// ✅ Good
interface VoiceSettings {
  stability: number
  similarity_boost: number
  speed: number
}

async function synthesize(text: string, settings: VoiceSettings): Promise<ArrayBuffer> {
  // implementation
}

// ❌ Avoid
const synthesize = async (text, settings) => {
  // implementation
}
```

### Interface Definitions

Define interfaces for all data structures:

```typescript
// electron/key-pool.ts
export interface ApiKey {
  id: string
  key: string
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

### Type Guards

Use type guards for runtime type checking:

```typescript
// ✅ Good
function isApiKey(data: any): data is ApiKey {
  return (
    typeof data.id === 'string' &&
    typeof data.key === 'string' &&
    typeof data.label === 'string'
  )
}
```

### Null Safety

Use optional chaining and nullish coalescing:

```typescript
// ✅ Good
const limit = key?.charLimit ?? 0
const status = key?.status === 'active' ? 'Ready' : 'Unavailable'

// ❌ Avoid
const limit = key ? key.charLimit : 0
```

---

## React Standards

### Component Structure

**Functional Components with Hooks:**

```typescript
import { useState, useEffect } from 'react'

interface Props {
  value: string
  onChange: (value: string) => void
}

export default function VoiceSelector({ value, onChange }: Props) {
  const [voices, setVoices] = useState<Voice[]>([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    loadVoices()
  }, [])

  const loadVoices = async () => {
    // implementation
  }

  return (
    <div>
      {/* JSX */}
    </div>
  )
}
```

### Component File Organization

1. Imports
2. Types/Interfaces
3. Component definition
4. Hooks and state
5. Event handlers
6. Render

```typescript
// ✅ Good order
import { useState } from 'react'
import { clsx } from 'clsx'

interface SliderControlProps {
  label: string
  min: number
  max: number
  value: number
  onChange: (value: number) => void
}

export default function SliderControl({ label, min, max, value, onChange }: SliderControlProps) {
  // State
  const [localValue, setLocalValue] = useState(value)

  // Handlers
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    onChange(Number(e.target.value))
  }

  // Render
  return <div>{/* ... */}</div>
}
```

### Props Destructuring

Always destructure props in the function signature:

```typescript
// ✅ Good
export default function VoiceSelector({ value, onChange }: Props) {
  // implementation
}

// ❌ Avoid
export default function VoiceSelector(props: Props) {
  const { value, onChange } = props
}
```

### Event Handlers

Type event handlers properly:

```typescript
// ✅ Good
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setText(e.target.value)
}

const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
  e.preventDefault()
  // implementation
}
```

---

## Electron Standards

### IPC Communication

**Define IPC Types:**

```typescript
// src/types/electron.d.ts
export interface IElevenLabsAPI {
  synthesize: (params: SynthesizeParams) => Promise<ArrayBuffer>
  transcribe: (audioBuffer: ArrayBuffer) => Promise<TranscriptionResult>
  keys: {
    list: () => Promise<ApiKeyPublic[]>
    add: (key: string, label: string) => Promise<ApiKeyPublic[]>
    remove: (id: string) => Promise<void>
  }
}
```

**Preload Script (Context Bridge):**

```typescript
// electron/preload.ts
import { contextBridge, ipcRenderer } from 'electron'

contextBridge.exposeInMainWorld('elevenlabs', {
  synthesize: (params: any) => ipcRenderer.invoke('tts:synthesize', params),
  transcribe: (buffer: ArrayBuffer) => ipcRenderer.invoke('stt:transcribe', { audioBuffer: buffer }),
  keys: {
    list: () => ipcRenderer.invoke('keys:list'),
    add: (key: string, label: string) => ipcRenderer.invoke('keys:add', { key, label }),
    remove: (id: string) => ipcRenderer.invoke('keys:remove', { id })
  }
})
```

### Main Process Handlers

**Validate IPC Inputs:**

```typescript
// electron/main.ts
ipcMain.handle('tts:synthesize', async (_, { text, voiceId, modelId, settings }) => {
  // Validate inputs
  if (typeof text !== 'string' || text.length > 5000) {
    throw new Error('Invalid text')
  }
  if (typeof voiceId !== 'string') {
    throw new Error('Invalid voiceId')
  }

  return ttsWithRotation(text, voiceId, modelId, settings)
})
```

### Security

**Never expose sensitive data to renderer:**

```typescript
// ✅ Good - Return masked key
toPublic(k: ApiKey): ApiKeyPublic {
  const { key, ...rest } = k
  return {
    ...rest,
    keyMasked: key.slice(0, 3) + '...' + key.slice(-4)
  }
}

// ❌ Avoid - Never send raw keys to renderer
```

---

## File Naming Conventions

### General Rules

- **Kebab-case** for all file names
- **Descriptive names** that indicate purpose
- **Component files** match component name

```
✅ Good:
- VoiceSelector.tsx
- key-pool.ts
- text-to-speech.tsx
- audio-player.tsx

❌ Avoid:
- voice_selector.tsx
- KeyPool.ts
- TTS.tsx
- player.tsx
```

### Component Files

Name components after their primary export:

```typescript
// src/components/VoiceSelector.tsx
export default function VoiceSelector() {
  // implementation
}
```

### Page Files

Pages match route names:

```
src/pages/
├── TextToSpeech.tsx      # /tts route
├── SpeechToText.tsx      # /stt route
├── ParallelTTS.tsx       # /parallel-tts route
├── SoundEffects.tsx      # /sfx route
├── Music.tsx             # /music route
└── KeyManager.tsx        # /keys route
```

---

## Code Organization

### Directory Structure

```
src/
├── components/    # Reusable UI components
├── pages/         # Route-level components
├── store/         # Zustand stores
└── types/         # TypeScript type definitions
```

### Import Order

1. React imports
2. Third-party libraries
3. Internal imports (components, store, types)
4. Type imports
5. Styles/CSS

```typescript
import { useState } from 'react'
import { clsx } from 'clsx'
import VoiceSelector from '../components/VoiceSelector'
import { useKeyPoolStore } from '../store/keyPoolStore'
import type { ApiKeyPublic } from '../types/electron'
import './styles.css'
```

### Component Size

**Keep components under 200 lines:**
- Extract complex logic to custom hooks
- Move large JSX to sub-components
- Split multi-responsibility components

```typescript
// ❌ Too long - Split into sub-components
export default function TextToSpeech() {
  // 300 lines of code
}

// ✅ Better - Extract settings panel
export default function TextToSpeech() {
  return (
    <>
      <TextArea text={text} onChange={setText} />
      <SettingsPanel settings={settings} onChange={setSettings} />
      <AudioPlayer audio={audio} />
    </>
  )
}
```

---

## Error Handling

### Try-Catch Pattern

Always handle errors in async operations:

```typescript
// ✅ Good
const generate = async () => {
  try {
    setGenerating(true)
    const audio = await window.elevenlabs.synthesize({ text, voiceId })
    setAudioBuffer(audio)
    setError(null)
  } catch (err: any) {
    setError(err.message || 'Generation failed')
  } finally {
    setGenerating(false)
  }
}
```

### Error Boundaries

Use error boundaries for React components:

```typescript
// Consider adding error boundary wrapper
class ErrorBoundary extends React.Component {
  state = { hasError: false }

  static getDerivedStateFromError() {
    return { hasError: true }
  }

  render() {
    if (this.state.hasError) {
      return <FallbackUI />
    }
    return this.props.children
  }
}
```

### IPC Error Handling

Handle IPC errors gracefully:

```typescript
// electron/main.ts
ipcMain.handle('tts:synthesize', async (_, params) => {
  try {
    return await ttsWithRotation(params)
  } catch (err: any) {
    // Log error for debugging
    console.error('TTS error:', err)

    // Re-throw with user-friendly message
    throw new Error(`Speech generation failed: ${err.message}`)
  }
})
```

### Key Pool Error Recovery

Implement automatic error recovery:

```typescript
// electron/key-pool.ts
async function ttsWithRotation(text: string, voiceId: string): Promise<ArrayBuffer> {
  let attempts = 0

  while (attempts < activeCount) {
    const key = keyPool.selectKeyForOperation('tts')
    attempts++

    try {
      // Make API call
      return await client.textToSpeech.convert(voiceId, { text })
    } catch (err: any) {
      const status = err?.statusCode

      // Handle specific errors
      if (status === 429 || status === 402) {
        keyPool.handleQuotaError(key.id)
        continue // Try next key
      }

      if (status === 401) {
        keyPool.handleApiError(key.id, 401)
      }

      // Re-throw if no more keys
      if (attempts >= activeCount) throw err
    }
  }
}
```

---

## Security Standards

### API Key Storage

**Encrypt all sensitive data:**

```typescript
// electron/key-pool.ts
import * as crypto from 'crypto'

const ALGORITHM = 'aes-256-gcm'

function encrypt(text: string, secret: string): string {
  const key = deriveKey(secret)
  const iv = crypto.randomBytes(16)
  const cipher = crypto.createCipheriv(ALGORITHM, key, iv)
  const enc = Buffer.concat([cipher.update(text, 'utf8'), cipher.final()])
  const tag = cipher.getAuthTag()
  return JSON.stringify({
    iv: iv.toString('hex'),
    tag: tag.toString('hex'),
    data: enc.toString('hex')
  })
}
```

### Machine-Specific Encryption

Derive encryption key from machine-specific data:

```typescript
private getMachineSecret(): string {
  return crypto.createHash('sha256')
    .update(app.getPath('userData'))
    .digest('hex')
}
```

### Context Isolation

Always enable context isolation in Electron:

```typescript
// electron/main.ts
const win = new BrowserWindow({
  webPreferences: {
    nodeIntegration: false,      // Never enable
    contextIsolation: true,      // Always enable
    sandbox: true,               // Enable sandbox
    preload: path.join(__dirname, 'preload.js')
  }
})
```

### Input Validation

Validate all inputs from renderer:

```typescript
ipcMain.handle('tts:synthesize', async (_, { text, voiceId, modelId, settings }) => {
  // Validate text
  if (typeof text !== 'string' || text.length > 5000) {
    throw new Error('Invalid text')
  }

  // Validate voiceId
  if (typeof voiceId !== 'string') {
    throw new Error('Invalid voiceId')
  }

  // Validate settings
  if (settings && typeof settings.stability !== 'number') {
    throw new Error('Invalid stability setting')
  }
})
```

---

## Testing Standards

### Unit Tests

Write unit tests for utility functions:

```typescript
// __tests__/key-pool.test.ts
describe('KeyPoolManager', () => {
  describe('selectKeyForOperation', () => {
    it('should select key with lowest usage percentage', () => {
      // Test implementation
    })

    it('should throw when no active keys', () => {
      // Test implementation
    })
  })

  describe('recordUsage', () => {
    it('should update character count', () => {
      // Test implementation
    })
  })
})
```

### Integration Tests

Test IPC communication:

```typescript
// __tests__/ipc.test.ts
describe('IPC Handlers', () => {
  it('should synthesize speech', async () => {
    const audio = await window.elevenlabs.synthesize({
      text: 'Hello world',
      voiceId: 'test-voice'
    })
    expect(audio).toBeInstanceOf(ArrayBuffer)
  })
})
```

---

## Documentation Standards

### Code Comments

**Use JSDoc for public APIs:**

```typescript
/**
 * Selects the best API key for a specific operation type.
 * Uses percentage-based selection for optimal distribution.
 *
 * @param operation - The operation type ('tts', 'stt', 'sfx', 'music')
 * @returns The selected API key
 * @throws Error when no active keys are available
 */
selectKeyForOperation(operation: 'tts' | 'stt' | 'sfx' | 'music'): ApiKey {
  // implementation
}
```

**Use inline comments for complex logic:**

```typescript
// Calculate usage percentage for each key
// Keys with lower percentage are preferred for load balancing
const bestKeys = this.store.keys
  .filter(k => k.status === 'active')
  .sort((a, b) => {
    const aPct = a.usedCharacters / a.charLimit
    const bPct = b.usedCharacters / b.charLimit
    return aPct - bPct
  })
```

### README Documentation

Every major module should have documentation:

```markdown
# Module Name

## Purpose
Brief description of what the module does.

## Usage
Code examples showing how to use the module.

## API
List of exported functions, classes, and types.

## Examples
Practical examples of common use cases.
```

---

## Git Commit Standards

### Conventional Commits

Use conventional commit format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code style changes (formatting)
- `refactor:` Code refactoring
- `test:` Test additions
- `chore:` Build/config changes

**Examples:**
```bash
feat(tts): add parallel sentence processing
fix(key-pool): handle 401 authentication errors
docs: update CI/CD setup guide
refactor(store): simplify Zustand store actions
chore: bump Bun version to 1.1.38
```

### Commit Best Practices

- **One commit per feature/fix**
- **Clear, descriptive messages**
- **Reference issues in footer**

---

## Code Review Checklist

Before submitting code:

- [ ] TypeScript types are explicit
- [ ] Error handling is implemented
- [ ] Security best practices followed
- [ ] Component size < 200 lines
- [ ] File names follow kebab-case
- [ ] Imports are organized
- [ ] JSDoc comments for public APIs
- [ ] No console.log in production code
- [ ] Tests pass locally
- [ ] Commit message follows convention

---

## Linting & Formatting

### ESLint Configuration

Follow the project ESLint rules (when configured):

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended"
  ]
}
```

### TypeScript Configuration

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

---

## Related Documentation

- [Project Overview](./PROJECT_OVERVIEW_PDR.md) - Project architecture
- [System Architecture](./system-architecture.md) - Detailed system design
- [Deployment Guide](./deployment-guide.md) - Build and deployment process

---

**Last Updated:** 2026-03-03
**Maintained By:** Development Team

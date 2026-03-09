# Google Analytics Integration Guide

## Overview

The ElevenLabs Desktop App includes built-in Google Analytics 4 (GA4) integration for tracking user behavior across main and renderer processes.

## Features

- **App launches** - Track when users open the app
- **Feature usage** - Monitor which features are used most
- **TTS generations** - Track text-to-speech usage (voice, model, character count)
- **User behavior** - Page views, button clicks, settings changes
- **Error tracking** - Capture application errors
- **User consent** - Opt-in/opt-out mechanism with encrypted client ID storage

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Renderer Process                        │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  analytics.ts (RendererAnalytics)                     │  │
│  │  - Track events via IPC                               │  │
│  │  - No direct GA4 calls from renderer                  │  │
│  └───────────────────────────────────────────────────────┘  │
│                          │                                  │
│                          │ IPC Handlers                     │
│                          ▼                                  │
└─────────────────────────────────────────────────────────────┘
                         │
                         │ contextBridge.exposeInMainWorld
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                      Main Process                           │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  analytics-handler.ts (MainProcessAnalytics)          │  │
│  │  - Encrypted client ID storage                        │  │
│  │  - Opt-out management                                 │  │
│  │  - GA4 Measurement Protocol API                       │  │
│  └───────────────────────────────────────────────────────┘  │
│                          │                                  │
│                          ▼                                  │
│              ┌────────────────────────┐                     │
│              │  GA4 API (HTTPS)       │                     │
│              │  google-analytics.com  │                     │
│              └────────────────────────┘                     │
└─────────────────────────────────────────────────────────────┘
```

## Setup

### 1. Create GA4 Property

1. Go to [Google Analytics](https://analytics.google.com/)
2. Create a new GA4 property for your app
3. Get your **Measurement ID** (starts with `G-`)
4. Get your **API Secret** from Data Streams → Measurement Protocol API

### 2. Configure Environment Variables

Create a `.env` file in the project root:

```bash
# Google Analytics 4 Configuration
GA4_MEASUREMENT_ID=G-XXXXXXXXXX
GA4_API_SECRET=your_api_secret_here
```

### 3. Build Configuration

The analytics service is already integrated into the build process. No additional dependencies needed.

## Usage

### Main Process

```typescript
import { mainAnalytics } from './electron/analytics-handler'

// Track custom event
mainAnalytics.track('custom_event', {
  property1: 'value1',
  property2: 123
})

// Track TTS generation
mainAnalytics.trackTTSGeneration({
  voiceId: 'xyz123',
  modelId: 'eleven_multilingual_v2',
  charCount: 500
})

// Track feature usage
mainAnalytics.trackFeature('kimi-optimization')

// Track settings change
mainAnalytics.trackSettingsChange('audio', 'volume', 75)

// Track voice selection
mainAnalytics.trackVoiceSelection('Rachel')

// Track page view
mainAnalytics.trackPageView('Settings')

// Track button click
mainAnalytics.trackButtonClick('upgrade', 'sidebar')

// Track error
mainAnalytics.trackError('TTS', 'Quota exceeded', false)
```

### Renderer Process

```typescript
import { analytics } from './src/services/analytics'

// Initialize (call once on app mount)
await analytics.init()

// Get status
const enabled = await analytics.getStatus()

// Set enabled/disabled (user consent)
await analytics.setEnabled(false) // User opts out

// Track custom event
analytics.track('custom_event', {
  property1: 'value1',
  property2: 123
})

// Track TTS generation
analytics.trackTTSGeneration({
  voiceId: 'xyz123',
  modelId: 'eleven_multilingual_v2',
  charCount: 500,
  success: true
})

// Track STT transcription
analytics.trackSTTTranscription({
  audioDuration: 30,
  language: 'vi',
  success: true
})

// Track music generation
analytics.trackMusicGeneration({
  prompt: 'Upbeat electronic music',
  duration: 30,
  success: true
})

// Track feature usage
analytics.trackFeature('parallel-tts')

// Track settings change
analytics.trackSettingsChange('proxy', 'enabled', true)

// Track voice selection
analytics.trackVoiceSelection('Adam')

// Track page view
analytics.trackPageView('KeyManager')

// Track button click
analytics.trackButtonClick('add-key', 'key-manager')

// Track error
analytics.trackError('STT', 'Invalid audio format', false)

// Track license events
analytics.trackLicense('activate', { plan: 'PRO' })
```

### Using the Global API (from preload)

```typescript
// In React components
window.elevenlabs.analytics.track('button_click', {
  button_name: 'generate',
  location: 'tts-page'
})

// Track TTS
window.elevenlabs.analytics.trackTTS({
  voiceId: 'xyz',
  modelId: 'eleven_multilingual_v2',
  charCount: 100
})

// Get status
const enabled = await window.elevenlabs.analytics.getStatus()

// Set enabled
await window.elevenlabs.analytics.setEnabled(false)
```

## Privacy & Consent

### User Opt-Out

Users can opt out of analytics tracking via the Settings page. The preference is stored encrypted in the app data directory.

```typescript
// Check if user opted in
const enabled = await analytics.getStatus()

// User opts out
await analytics.setEnabled(false)

// User opts back in
await analytics.setEnabled(true)
```

### Data Storage

- **Client ID**: Stored encrypted in `analytics-client-id.dat`
- **Opt-out status**: Stored in `analytics-opt-out.dat`
- **Location**: App user data directory (`~/.elevenlabs-app/` or similar)

### What's Tracked

✅ **Tracked:**
- Event names (app_launch, tts_generation, etc.)
- Event parameters (voice ID, model ID, character count)
- App version, platform, architecture
- Plan type (FREE, PRO, TRIAL)

❌ **NOT Tracked:**
- Personal information (name, email, etc.)
- API keys
- License keys
- Text content sent to TTS
- Audio files

## Events Reference

### App Events

| Event | Description | Parameters |
|-------|-------------|------------|
| `app_launch` | App started | `app_version`, `platform`, `first_open` |
| `app_close` | App closed | `session_duration_seconds` |
| `page_view` | Page/screen view | `page_title`, `page_location` |

### Feature Events

| Event | Description | Parameters |
|-------|-------------|------------|
| `tts_generation` | TTS audio generated | `voice_id`, `model_id`, `char_count`, `duration_seconds` |
| `stt_transcription` | STT transcription | `model`, `audio_duration_seconds`, `language` |
| `music_generation` | Music generated | `prompt_length`, `audio_duration_seconds` |
| `sfx_generation` | Sound effect generated | `prompt_length`, `audio_duration_seconds` |
| `feature_usage` | Feature used | `feature_name` |

### User Events

| Event | Description | Parameters |
|-------|-------------|------------|
| `voice_selected` | Voice changed | `voice_id` |
| `settings_changed` | Setting modified | `category`, `setting`, `value` |
| `button_click` | Button clicked | `button_name`, `location` |
| `license_event` | License action | `event_type`, `plan` |
| `error` | Error occurred | `error_category`, `error_description`, `fatal` |

## Debugging

### Enable Debug Logging

The analytics service logs to console by default. Look for:

```
[MainAnalytics] Initialized { clientId: "...", enabled: true }
[MainAnalytics] Event sent: tts_generation
[RendererAnalytics] User opted out
```

### Check GA4 DebugView

1. Go to GA4 → Admin → Data Streams
2. Select your stream
3. Enable "DebugView"
4. Events will appear in real-time

### Test Mode

Events won't be sent if credentials are not configured:

```
[MainAnalytics] Not configured - skipping event: tts_generation
```

## Troubleshooting

### Events not showing in GA4

1. Check credentials are set in `.env`
2. Verify GA4 property is active
3. Wait up to 24 hours for standard reports (DebugView shows in real-time)
4. Check firewall/proxy settings

### Opt-out not working

1. Client ID and opt-out files are in user data directory
2. Delete `analytics-opt-out.dat` to reset
3. Restart app after changes

### Build errors

The analytics service uses only Node.js built-in modules:
- `crypto` - Encryption
- `fs` - File system
- `path` - Path utilities
- `https` - HTTP requests

No external dependencies required.

## Migration from Old Implementation

If you have an existing analytics implementation:

1. **Remove old files** (if any):
   - `src/services/analytics.ts` (old)
   - `electron/analytics-handler.ts` (old)

2. **Update imports**:
   ```typescript
   // Old
   import { analytics } from './old-analytics'

   // New
   import { analytics } from './src/services/analytics'
   import { mainAnalytics } from './electron/analytics-handler'
   ```

3. **Update API calls** (if needed):
   - Most methods are backward compatible
   - Check event parameter names match your GA4 custom dimensions

## Security

- Client ID is encrypted using AES-256-GCM
- Encryption key is derived from machine-specific path
- No PII (Personally Identifiable Information) is sent
- All communication is via HTTPS
- Opt-out is respected across app restarts

## Performance

- Analytics calls are non-blocking
- Failed requests don't disrupt app functionality
- IPC communication is asynchronous
- No impact on TTS/STT generation speed

## Resources

- [GA4 Measurement Protocol API](https://developers.google.com/analytics/devguides/collection/protocol/ga4)
- [GA4 Event Parameters](https://developers.google.com/analytics/devguides/collection/ga4/reference/events)
- [Electron IPC](https://www.electronjs.org/docs/latest/tutorial/ipc)

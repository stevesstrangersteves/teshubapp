# TeSHub Desktop API Server - Documentation

## Overview

The TeSHub Desktop API Server provides a local HTTP endpoint that exposes ElevenLabs API functionality. This allows external services like **n8n** to integrate with the TeSHub Desktop app through a tunnel connection.

## Key Features

- **No Authentication Required**: The server trusts local connections and automatically manages API keys from your key pool
- **ElevenLabs-Compatible API**: Uses the same endpoint format as ElevenLabs API
- **Swagger Documentation**: Interactive API docs at `/api-docs`
- **Automatic Key Rotation**: Uses your configured key pool with rotation strategies

## Quick Start

### 1. Start the API Server

1. Open TeSHub Desktop app
2. Go to **Settings** → **Tunnel Server** tab
3. Click **Start Server** (default port: 8080)
4. Server starts at `http://localhost:8080`

### 2. Start Tunnel (for external access)

1. In the same tab, select your tunnel provider (Cloudflare recommended)
2. Click **Start Tunnel**
3. Copy the public URL (e.g., `https://abc123.trycloudflare.com`)

### 3. Access Swagger Docs

Open in browser: `http://localhost:8080/api-docs`

Or for tunnel: `https://abc123.trycloudflare.com/api-docs`

## API Endpoints

### Health Check

```http
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2026-03-04T10:30:00.000Z",
  "tunnel": {
    "enabled": true,
    "url": "https://abc123.trycloudflare.com"
  },
  "keys": {
    "active": 3,
    "total": 5
  },
  "server": {
    "port": 8080,
    "uptime": 1234.56
  }
}
```

### Text-to-Speech

```http
POST /v1/text-to-speech/:voiceId
Content-Type: application/json
```

**Request Body:**
```json
{
  "text": "Hello, this is a test",
  "model_id": "eleven_multilingual_v2",
  "output_format": "mp3_44100_128",
  "voice_settings": {
    "stability": 0.5,
    "similarity_boost": 0.75,
    "style": 0,
    "use_speaker_boost": true,
    "speed": 1.0
  }
}
```

**Response:** Audio stream (`audio/mpeg`)

### Speech-to-Text

```http
POST /v1/speech-to-text
Content-Type: application/json
```

**Request Body:**
```json
{
  "file": "<base64 audio data or binary>",
  "model_id": "scribe_v2",
  "language_code": "en",
  "keyterms": ["ElevenLabs", "API"],
  "diarize": false
}
```

**Response:**
```json
{
  "text": "Transcribed text here...",
  "words": [
    { "word": "Hello", "start": 0.0, "end": 0.5, "confidence": 0.98 }
  ],
  "languageCode": "en"
}
```

### Text-to-Sound-Effects

```http
POST /v1/text-to-sound-effects
Content-Type: application/json
```

**Request Body:**
```json
{
  "text": "Heavy rain on a tin roof",
  "duration_seconds": 5,
  "prompt_influence": 0.3,
  "loop": false
}
```

**Response:** Audio stream (`audio/mpeg`)

### Music Composition

```http
POST /v1/music/compose
Content-Type: application/json
```

**Request Body:**
```json
{
  "prompt": "Cinematic orchestral piece with strings and brass",
  "music_length_ms": 30000
}
```

**Response:** Audio stream (`audio/mpeg`)

### List Voices

```http
GET /v1/voices
```

**Response:**
```json
{
  "voices": [
    {
      "voice_id": "21m00Tcm4TlvDq8ikWAM",
      "name": "Rachel",
      "category": "premade",
      "description": "American, warm, versatile"
    }
  ]
}
```

## n8n Integration

### Using with n8n HTTP Request Node

1. **Add HTTP Request Node** to your workflow
2. **Configure:**
   - Method: `POST`
   - URL: `https://YOUR-TUNNEL-URL.trycloudflare.com/v1/text-to-speech/21m00Tcm4TlvDq8ikWAM`
   - Headers:
     - `Content-Type: application/json`
   - Body (JSON):
     ```json
     {
       "text": "Your text here",
       "model_id": "eleven_multilingual_v2",
       "voice_settings": {
         "stability": 0.5,
         "similarity_boost": 0.75
       }
     }
     ```
3. **Handle Response:** The response is binary audio data - save it to a file or pass to another node

### Example n8n Workflow

```json
{
  "nodes": [
    {
      "parameters": {
        "method": "POST",
        "url": "https://YOUR-TUNNEL-URL.trycloudflare.com/v1/text-to-speech/21m00Tcm4TlvDq8ikWAM",
        "sendBody": true,
        "bodyParameters": {
          "parameters": [
            {
              "name": "text",
              "value": "Hello from n8n!"
            },
            {
              "name": "model_id",
              "value": "eleven_multilingual_v2"
            }
          ]
        },
        "options": {
          "headerParameters": {
            "parameters": [
              { "name": "Content-Type", "value": "application/json" }
            ]
          }
        }
      },
      "name": "ElevenLabs TTS",
      "type": "n8n-nodes-base.httpRequest"
    }
  ]
}
```

## Rate Limiting

The server implements rate limiting to prevent abuse:
- **100 requests per minute** per IP address
- Returns `429 Too Many Requests` when exceeded

## Error Handling

All errors follow this format:

```json
{
  "error": {
    "message": "Error description",
    "type": "error_type"
  }
}
```

**Common Error Types:**
- `bad_request` - Invalid request parameters (400)
- `tts_error` - Text-to-speech failure (500)
- `stt_error` - Speech-to-text failure (500)
- `sfx_error` - Sound effect generation failure (500)
- `music_error` - Music composition failure (500)
- `voices_error` - Voice list fetch failure (500)
- `rate_limit_error` - Rate limit exceeded (429)
- `not_found` - Endpoint not found (404)

## Security Notes

⚠️ **Important Security Considerations:**

1. **No Authentication**: The server does not require authentication. Only expose it through trusted tunnels.
2. **Local Trust Model**: Designed for local/trusted network use only.
3. **Tunnel Security**: Use Cloudflare Tunnel or ngrok for controlled public exposure.
4. **Rate Limiting**: Enabled by default to prevent abuse.
5. **API Key Management**: Keys are stored encrypted in the app's key pool.

## Troubleshooting

### Server Won't Start
- Check if port 8080 is already in use
- Try a different port in Settings

### Tunnel Won't Connect
- Ensure cloudflared/ngrok is installed
- Check firewall settings
- Verify tunnel provider status

### API Calls Fail
- Check that you have active API keys in the key pool
- Verify proxy settings if using a proxy
- Check rate limit status

### Swagger UI Not Loading
- Navigate to `http://localhost:8080/api-docs`
- Ensure server is running
- Clear browser cache

## API Reference

Full interactive API documentation is available at:
- **Local**: `http://localhost:8080/api-docs`
- **Tunnel**: `https://YOUR-TUNNEL-URL/api-docs`

The Swagger UI provides:
- Interactive endpoint testing
- Request/response schemas
- Example values
- Try-it-out functionality

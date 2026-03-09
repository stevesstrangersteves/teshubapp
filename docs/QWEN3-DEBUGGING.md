# Qwen3-TTS Production Debugging Guide

## Python 3.11 Requirement

**CRITICAL:** Qwen3-TTS requires **Python 3.11.x** specifically. Other versions (3.9, 3.10, 3.12+) have compatibility issues.

### Auto-Install Python 3.11

The app now automatically installs Python 3.11 if not found:

**macOS:**
1. First tries `pyenv` (if installed)
2. Then tries `homebrew` (if installed)
3. Falls back to python.org installer (opens GUI installer)

**Windows:**
1. Downloads and silently installs python.org installer

### Manual Python 3.11 Installation

**macOS (Homebrew):**
```bash
brew install python@3.11
python3.11 --version  # Should show 3.11.x
```

**macOS (pyenv):**
```bash
pyenv install 3.11.7
pyenv global 3.11.7
```

**Windows:**
Download from: https://www.python.org/ftp/python/3.11.7/python-3.11.7-amd64.exe

## Auto-Setup Feature

**New in latest version:** The app now automatically detects missing Python dependencies and triggers the environment setup screen when needed.

### How it works:

1. **On app startup** - The UI can check `qwen3:needs-environment-setup` to detect if setup is needed
2. **On first synthesis** - If dependencies are missing, the app automatically:
   - Detects the `MISSING_DEPENDENCY` error
   - Sends `qwen3:needs-setup` event to the UI
   - Triggers `pythonEnvironmentManager.setupEnvironment()`
   - Retries the synthesis after setup completes

### Installation Details

The `qwen-tts` package has **pinned dependencies**:
- `transformers==4.57.3`
- `accelerate==1.12.0`
- `numpy`, `soundfile`, `librosa`, `torchaudio`, etc.

**Important:** The setup now:
1. Uninstalls any existing conflicting qwen-tts versions
2. Uses `--force-reinstall` for clean installation
3. Recreates the entire venv if installation fails

### Manual Cleanup & Reinstall (if auto-setup fails)

If the auto-setup fails with dependency conflicts, run these commands:

```bash
# 1. Delete the existing venv completely
rm -rf ~/Library/Application\ Support/teshub-desktop/python/venv

# 2. Run the app and let it recreate everything
# The app will detect the missing venv and create a fresh one
```

Or manually install:

```bash
# Navigate to venv
cd ~/Library/Application\ Support/teshub-desktop/python

# Delete old venv
rm -rf venv

# Create fresh venv
python3 -m venv venv

# Activate and install qwen-tts
source venv/bin/activate
pip install --upgrade pip
pip install --no-cache-dir --force-reinstall qwen-tts

# Verify
python -c "import qwen_tts; print(qwen_tts.__version__)"
```

### Known Issue: Conflicting Versions

If you see this error:
```
ERROR: Cannot install qwen-tts==0.0.2, qwen-tts==0.0.3, ... conflicting dependencies
```

This means multiple qwen-tts versions are cached. The fix:
1. The app now automatically handles this by uninstalling first
2. If it still fails, use the manual cleanup commands above

## Log File Location

The production build now includes comprehensive logging. Log files are written to:

**macOS:**
```
~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log
```

**To view logs in terminal:**
```bash
# Tail the log file in real-time
tail -f "~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log"

# View last 100 lines
tail -n 100 "~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log"

# Full log content
cat "~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log"
```

## State Files

Worker state is persisted to:
```
~/Library/Application Support/TeSHub Desktop/python-worker-qwen3-state.json
```

This file contains:
- Worker status (stopped/starting/running/error)
- Health check results
- Startup timing information
- GPU/CUDA availability

## Common Issues

### 1. Worker Not Found

**Log message:**
```
[Qwen3Bridge] ERROR: Worker not found. Searched: <paths>
```

**Cause:** The `qwen3-worker.py` file is not in the expected location.

**Solution:**
- Check that `dist/qwen3-worker.py` exists after build
- Verify `electron-builder.yml` has `extraResources` configured
- In production, worker should be at `process.resourcesPath/qwen3-worker.py`

### 2. Python Not Found

**Log message:**
```
[Qwen3Bridge] Python not found in any location
```

**Cause:** No Python 3.9+ installation detected.

**Solution:**
- Install Python 3.9+ from python.org or Homebrew
- Or use the built-in environment via `python-environment-manager`
- Check log for which paths were searched

### 3. Worker Startup Timeout

**Log message:**
```
[Qwen3Bridge] ERROR: Worker startup timeout after 120s
```

**Cause:** Model loading or Python environment initialization is slow.

**Solution:**
- First startup can take 2-5 minutes (model download + initialization)
- Subsequent startups should be faster (cached models)
- Check stderr logs for Python errors or missing dependencies

### 4. Synthesis Timeout

**Log message:**
```
[Qwen3Bridge] ERROR: Request X timeout after 5 minutes
```

**Cause:** Model inference is slow (especially on CPU).

**Solution:**
- GPU acceleration significantly speeds up synthesis
- First synthesis after startup includes model loading
- Consider using smaller models (0.6B vs 1.7B)

## Log Format

```
[ISO_TIMESTAMP] [LEVEL] Message
```

**Levels:**
- `[LOG]` - General information
- `[WARN]` - Warnings
- `[ERROR]` - Errors
- `[Qwen3Bridge]` - Bridge-specific logs

**Example:**
```
[2026-03-07T10:00:00.000Z] [Qwen3Bridge] === Starting Worker Process ===
[2026-03-07T10:00:00.001Z] [Qwen3Bridge] Environment: production
[2026-03-07T10:00:00.002Z] [Qwen3Bridge] Checking worker paths:
[2026-03-07T10:00:00.003Z] [Qwen3Bridge]   ✓ /Applications/TeSHub Desktop.app/Contents/Resources/qwen3-worker.py
```

## Building for Production

```bash
# Build macOS app
npm run electron:build:mac

# Output location
release/TeSHub Desktop-1.0.0-mac.zip        # Intel
release/TeSHub Desktop-1.0.0-arm64-mac.zip  # Apple Silicon
```

## Testing Production Build

1. **Extract the zip:**
```bash
cd release
unzip "TeSHub Desktop-1.0.0-mac.zip"
```

2. **Run the app:**
```bash
open "TeSHub Desktop.app"
```

3. **Monitor logs:**
```bash
tail -f "~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log"
```

## Worker Path Resolution

In production, the worker path is resolved in this order:

1. `process.resourcesPath/qwen3-worker.py` (packed by electron-builder)
2. `__dirname/qwen3-worker.py` (fallback)
3. `app.getAppPath()/electron/qwen3-worker.py` (dev)
4. `process.cwd()/electron/qwen3-worker.py` (dev)
5. `process.cwd()/dist/qwen3-worker.py` (dev test)

The log file shows which paths were checked and which one was used.

## Clearing State

To reset worker state (for debugging):

```bash
# Delete state file
rm "~/Library/Application Support/TeSHub Desktop/python-worker-qwen3-state.json"

# Delete log file
rm "~/Library/Application Support/TeSHub Desktop/qwen3-bridge.log"

# Delete entire app data (nuclear option)
rm -rf "~/Library/Application Support/TeSHub Desktop"
```

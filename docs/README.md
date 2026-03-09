# TeSHub Desktop - Documentation

Welcome to the TeSHub Desktop application documentation.

---

## 📚 Documentation Index

### Getting Started

| Document | Description | When to Read |
|----------|-------------|--------------|
| [Codebase Summary](./CODEBASE_SUMMARY.md) | Quick reference to files and structure | First day on project |
| [Project Overview](./PROJECT_OVERVIEW_PDR.md) | Complete project description and features | Understanding the app |
| [CI/CD Quick Start](./GITHUB_ACTIONS_QUICKSTART.md) | Setup CI/CD in 5-60 minutes | Setting up builds |

---

### Development Documentation

| Document | Description | When to Read |
|----------|-------------|--------------|
| [Code Standards](./CODE_STANDARDS.md) | Coding guidelines and best practices | Before writing code |
| [System Architecture](./SYSTEM_ARCHITECTURE.md) | Architecture diagrams and data flow | Understanding design |
| [API Reference](./CODEBASE_SUMMARY.md#ipc-api-reference) | IPC handlers and data models | Implementing features |

---

### DevOps Documentation

| Document | Description | When to Read |
|----------|-------------|--------------|
| [CI/CD Index](./CI_CD_INDEX.md) | Complete CI/CD documentation hub | Setting up pipelines |
| [Pipeline Flow](./CI_CD_PIPELINE_FLOW.md) | Visual workflow diagrams | Understanding builds |
| [Code Signing Setup](./CODE_SIGNING_SETUP.md) | Certificate procurement guide | Production releases |
| [Workflow README](../.github/workflows/README.md) | Workflow configuration | Modifying workflows |

---

## 📖 Documentation by Role

### For New Developers

1. **Start Here:** [Codebase Summary](./CODEBASE_SUMMARY.md)
2. **Then Read:** [Project Overview](./PROJECT_OVERVIEW_PDR.md)
3. **Before Coding:** [Code Standards](./CODE_STANDARDS.md)
4. **For Understanding:** [System Architecture](./SYSTEM_ARCHITECTURE.md)

### For DevOps Engineers

1. **Start Here:** [CI/CD Index](./CI_CD_INDEX.md)
2. **For Setup:** [Quick Start Guide](./GITHUB_ACTIONS_QUICKSTART.md)
3. **For Certificates:** [Code Signing Setup](./CODE_SIGNING_SETUP.md)
4. **For Details:** [Pipeline Flow](./CI_CD_PIPELINE_FLOW.md)

### For Contributors

1. **Review:** [Code Standards](./CODE_STANDARDS.md)
2. **Understand:** [System Architecture](./SYSTEM_ARCHITECTURE.md)
3. **Reference:** [Codebase Summary](./CODEBASE_SUMMARY.md)

---

## 🗂️ Documentation Structure

```
docs/
├── README.md (this file)              ← You are here
├── CODEBASE_SUMMARY.md                ← Quick reference
├── PROJECT_OVERVIEW_PDR.md            ← Project description
├── CODE_STANDARDS.md                  ← Coding guidelines
├── SYSTEM_ARCHITECTURE.md             ← Architecture diagrams
├── CI_CD_INDEX.md                     ← CI/CD hub
├── CI_CD_PIPELINE_FLOW.md             ← Pipeline visuals
├── CODE_SIGNING_SETUP.md              ← Certificate guide
└── GITHUB_ACTIONS_QUICKSTART.md       ← Quick start
```

---

## 📋 Quick Reference

### Project Info

- **Name:** TeSHub Desktop
- **Version:** 1.0.0
- **Framework:** Electron + React + TypeScript
- **Package Manager:** Bun
- **Build Tool:** Vite + electron-builder

### Key Features

- Text-to-Speech (TTS)
- Parallel TTS Processing
- Speech-to-Text (STT)
- Sound Effects Generation
- Music Composition
- API Key Pool Management

### Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React 18, TypeScript, Tailwind CSS |
| State | Zustand |
| Routing | React Router v6 |
| UI Components | Radix UI |
| Charts | Recharts |
| Backend | Electron, Node.js |
| SDK | @elevenlabs/elevenlabs-js |
| Build | Vite, TypeScript |
| Packaging | electron-builder |

### Project Structure

```
teshub-desktop/
├── electron/           # Main process
├── src/                # Renderer (React app)
│   ├── pages/          # Page components
│   ├── components/     # Reusable components
│   ├── store/          # Zustand stores
│   └── types/          # TypeScript types
├── docs/               # Documentation
├── .github/workflows/  # CI/CD
└── assets/             # Icons & resources
```

---

## 🔧 Common Tasks

### Development

```bash
# Install dependencies
bun install

# Start development
bun run dev

# Build
bun run build

# Build for platform
bun run electron:build:mac
bun run electron:build:win
bun run electron:build:linux
```

### Type Checking

```bash
# Type check only
bunx tsc --noEmit

# Full build
bun run build
```

### Git Workflows

```bash
# Conventional commit
git commit -m "feat(tts): add parallel sentence processing"

# Create release
git tag v1.0.0
git push origin v1.0.0
```

---

## 🎯 Feature Documentation

### Text-to-Speech

**Files:**
- `src/pages/TextToSpeech.tsx` - UI
- `electron/main.ts` - IPC handler
- `electron/key-pool.ts` - Key rotation

**Documentation:** [Project Overview → Core Features](./PROJECT_OVERVIEW_PDR.md#core-features)

### Parallel TTS

**Files:**
- `src/pages/ParallelTTS.tsx` - UI
- `electron/main.ts` - IPC handler (parallel processing)

**Documentation:** [Project Overview → Core Features](./PROJECT_OVERVIEW_PDR.md#core-features)

### Speech-to-Text

**Files:**
- `src/pages/SpeechToText.tsx` - UI
- `electron/main.ts` - IPC handler

**Documentation:** [Project Overview → Core Features](./PROJECT_OVERVIEW_PDR.md#core-features)

### Sound Effects

**Files:**
- `src/pages/SoundEffects.tsx` - UI
- `electron/main.ts` - IPC handler

**Documentation:** [Project Overview → Core Features](./PROJECT_OVERVIEW_PDR.md#core-features)

### Music Generation

**Files:**
- `src/pages/Music.tsx` - UI
- `electron/main.ts` - IPC handler

**Documentation:** [Project Overview → Core Features](./PROJECT_OVERVIEW_PDR.md#core-features)

### API Key Pool ⭐

**Files:**
- `electron/key-pool.ts` - Core logic
- `src/pages/KeyManager.tsx` - UI
- `src/store/keyPoolStore.ts` - State

**Documentation:** [Project Overview → Key Pool Manager](./PROJECT_OVERVIEW_PDR.md#6-api-key-pool-manager-)

---

## 🔐 Security Documentation

### API Key Encryption

- **Algorithm:** AES-256-GCM
- **Key Derivation:** scrypt with machine-specific salt
- **Storage:** Encrypted JSON in user data directory

**Documentation:** [System Architecture → Security Architecture](./SYSTEM_ARCHITECTURE.md#security-architecture)

### Electron Security

- **Context Isolation:** Enabled
- **Node Integration:** Disabled
- **Sandbox:** Enabled
- **Preload:** Secure context bridge

**Documentation:** [Code Standards → Security Standards](./CODE_STANDARDS.md#security-standards)

---

## 📊 CI/CD Documentation

### Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `ci.yml` | Pull requests | Quality gates |
| `build.yml` | Push to main, tags | Build & release |

### Build Matrix

| Platform | Runner | Artifacts |
|----------|--------|-----------|
| macOS | macos-latest | .dmg, .zip |
| Windows | windows-latest | .exe, .msi |
| Linux | ubuntu-latest | .AppImage, .deb |

**Documentation:** [CI/CD Index](./CI_CD_INDEX.md)

---

## 📞 Support

### Internal Resources

- [Codebase Summary](./CODEBASE_SUMMARY.md) - File reference
- [Code Standards](./CODE_STANDARDS.md) - Coding guidelines
- [System Architecture](./SYSTEM_ARCHITECTURE.md) - Design docs

### External Resources

- [ElevenLabs SDK Docs](https://elevenlabs.io/docs)
- [Electron Docs](https://www.electronjs.org/docs)
- [React Docs](https://react.dev)
- [TypeScript Docs](https://www.typescriptlang.org/docs)
- [Vite Docs](https://vitejs.dev/guide)

---

## 📝 Documentation Maintenance

### When to Update

| Change | Documents to Update |
|--------|---------------------|
| New feature | PROJECT_OVERVIEW_PDR.md, CODEBASE_SUMMARY.md |
| Architecture change | SYSTEM_ARCHITECTURE.md |
| New component | CODEBASE_SUMMARY.md, CODE_STANDARDS.md |
| CI/CD change | CI_CD_*.md files |
| Code standards update | CODE_STANDARDS.md |

### Review Schedule

- **Monthly:** Verify links work
- **Quarterly:** Update screenshots and examples
- **Annually:** Full documentation review

---

## 🚀 Getting Started Path

### Day 1

1. Read [Codebase Summary](./CODEBASE_SUMMARY.md)
2. Read [Project Overview](./PROJECT_OVERVIEW_PDR.md)
3. Setup development environment

### Week 1

1. Read [Code Standards](./CODE_STANDARDS.md)
2. Read [System Architecture](./SYSTEM_ARCHITECTURE.md)
3. Complete first feature/bug fix

### Month 1

1. Understand full architecture
2. Contribute to multiple features
3. Help maintain documentation

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-03 | Initial documentation |

---

**Last Updated:** 2026-03-03
**Maintained By:** Development Team

---

## Quick Links

- 📄 [Codebase Summary](./CODEBASE_SUMMARY.md)
- 📘 [Project Overview](./PROJECT_OVERVIEW_PDR.md)
- 📐 [Code Standards](./CODE_STANDARDS.md)
- 🏗️ [System Architecture](./SYSTEM_ARCHITECTURE.md)
- 🚀 [CI/CD Index](./CI_CD_INDEX.md)
- 🔐 [Code Signing Guide](./CODE_SIGNING_SETUP.md)

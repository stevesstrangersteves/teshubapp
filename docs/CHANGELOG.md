# Lịch Sử Phiên Bản (Changelog)

Tất cả các cập nhật, tính năng mới, và bug fixes của TeSHub Desktop.

Định Dạng: [Keep a Changelog](https://keepachangelog.com/)

---

## [1.0.0] - 2026-03-08

### Bản Phát Hành Chính (Major Release)

#### ✨ Features - Tính Năng Mới

**Core TTS Engine**
- ✅ Text-to-Speech với 30+ giọng AI
- ✅ Điều chỉnh Stability & Similarity
- ✅ Speaker Boost option
- ✅ Speed adjustment (0.5x - 2.0x)
- ✅ Style selection (Neutral, Cheerful, v.v.)
- ✅ Preview function (test trước generate)

**Parallel TTS - Xử Lý Song Song**
- ✅ Batch processing 2-10 requests
- ✅ Per-sentence voice customization
- ✅ Batch import từ CSV/Excel
- ✅ Real-time progress tracking
- ✅ Batch download (ZIP)

**Speech-to-Text (STT)**
- ✅ Transcription support (99+ languages)
- ✅ Auto language detection
- ✅ Speaker diarization
- ✅ Word-level timestamps
- ✅ Multiple export formats (TXT, SRT, VTT, JSON)
- ✅ Support lên tới 100MB files

**Advanced Features**
- ✅ Sound Effects generation
- ✅ Music composition (instrumental + songs)
- ✅ Kimi AI integration (text optimization)

**API Key Management**
- ✅ Multi-key pool management
- ✅ 3 selection strategies (Least-Used, Round-Robin, Random)
- ✅ Usage tracking & statistics
- ✅ Health monitoring & auto-detection
- ✅ CSV bulk import
- ✅ AES-256-GCM encryption
- ✅ Usage history snapshots

**License Management**
- ✅ FREE tier (cơ bản)
- ✅ TRIAL tier ($9.99/mo)
- ✅ PRO tier ($29.99/mo)
- ✅ Server-side validation
- ✅ 24-hour offline cache

**UI & UX**
- ✅ Modern React 18 interface
- ✅ Responsive design (mobile-friendly)
- ✅ Dark/Light theme support
- ✅ Internationalization (i18n): Vietnamese, English
- ✅ Keyboard shortcuts
- ✅ Settings persistence

**Developer Experience**
- ✅ IPC API for main/renderer processes
- ✅ Express-based API server (localhost:3000)
- ✅ Swagger UI documentation
- ✅ TypeScript strict mode
- ✅ Error boundaries & graceful fallbacks

#### 🐛 Bug Fixes
- ✅ Fixed API key validation on initial setup
- ✅ Fixed STT export formatting issues
- ✅ Fixed parallel TTS concurrent limit
- ✅ Fixed license caching edge cases
- ✅ Fixed proxy connection stability

#### 🔧 Improvements
- ✅ Optimized TTS rendering (20% faster)
- ✅ Reduced memory usage in batch mode
- ✅ Better error messages & logging
- ✅ Improved key pool visualization
- ✅ Added tooltips & help text

#### 📚 Documentation
- ✅ Complete user guide (Vietnamese + English)
- ✅ Installation guides (Windows/macOS/Linux)
- ✅ Feature documentation
- ✅ API configuration guide
- ✅ Troubleshooting FAQ

#### 🚀 Performance
- ✅ Build size: ~100MB (optimized)
- ✅ Startup time: <2 seconds
- ✅ TTS generation: ~1000 chars/sec
- ✅ Parallel limit: 10 concurrent

#### 🔐 Security
- ✅ API key encryption (AES-256)
- ✅ No plaintext key storage
- ✅ SSL/TLS for all API calls
- ✅ HTTPS-only settings

#### 🌍 Platforms
- ✅ Windows 10+ (x64)
- ✅ macOS 12+ (Intel + Apple Silicon)
- ✅ Linux Ubuntu 20.04+ (x64)

---

## [0.9.0-beta] - 2026-02-28

### Beta Release - Final Testing Phase

#### ✨ Features
- ✅ TTS core implementation
- ✅ STT transcription
- ✅ Basic key management (1-3 keys)
- ✅ Simple licensing system

#### 🔄 Changes
- 🔄 Refined UI based on beta feedback
- 🔄 Optimized API quota tracking
- 🔄 Improved error handling

#### 🐛 Fixes
- ✅ Fixed Windows build signing
- ✅ Fixed macOS ARM64 support
- ✅ Fixed STT timeout issues

---

## [0.8.0-alpha] - 2026-02-10

### Alpha Release - Feature Complete

#### ✨ Features
- ✅ TTS engine (basic)
- ✅ STT engine (basic)
- ✅ Key management (MVP)

#### ⚠️ Known Issues
- ⚠️ Parallel TTS limited to 2x
- ⚠️ No Music generation
- ⚠️ No Sound Effects

---

## Versioning Scheme

**Format**: MAJOR.MINOR.PATCH

- **MAJOR**: Breaking changes, major features
- **MINOR**: New features, minor improvements
- **PATCH**: Bug fixes, tiny improvements

---

## Roadmap: Phiên Bản Tương Lai

### [1.1.0] - Q2 2026
- 🔜 Team collaboration features
- 🔜 Advanced analytics dashboard
- 🔜 Webhook support for automation
- 🔜 Custom model training (voice cloning)
- 🔜 Live Chat support

**Est. Release**: April 2026

### [1.2.0] - Q3 2026
- 🔜 API v2 (REST endpoints)
- 🔜 Mobile app (iOS/Android)
- 🔜 Cloud sync
- 🔜 Marketplace for voices
- 🔜 Phone support (PRO)

**Est. Release**: July 2026

### [2.0.0] - Q4 2026
- 🔜 Enterprise features
- 🔜 White-label option
- 🔜 Advanced DRM
- 🔜 Custom integrations
- 🔜 Video editor plugin

**Est. Release**: October 2026

---

## Archive: Phiên Bản Cũ

### Download Cũ

Nếu cần phiên bản cũ:
1. Vào [GitHub Releases](https://github.com/teshub/desktop/releases)
2. Tìm phiên bản cần (v0.9.0, v0.8.0, v.v.)
3. Download file .zip

**Lưu Ý**: Chỉ phiên bản >= 1.0.0 được hỗ trợ chính thức.

---

## Upgrade Guide

### Từ Beta → v1.0.0

✅ **Lợi Ích**:
- ✅ Tất cả tính năng bị unlock
- ✅ Bug fixes
- ✅ Performance improvements
- ✅ Full support

**Cách Upgrade**:
1. Tải v1.0.0 từ [teshub.dev/download](https://teshub.dev/download)
2. Cài đặt mới (dữ liệu sẽ được giữ)
3. Hoặc: Replace executable cũ bằng mới

**Dữ Liệu Có Bị Mất Không?**
❌ Không. Keys, settings, history sẽ được giữ nguyên.

---

## Support Lifecycle

### Version Support Matrix

| Version | Release | Support End | Status |
|---------|---------|-------------|--------|
| 1.0.x | Mar 2026 | Mar 2027 | Active |
| 0.9.x | Feb 2026 | Jun 2026 | Security only |
| 0.8.x | Feb 2026 | Apr 2026 | EOL |

**Support tiers**:
- **Active**: Bugs + Features
- **Security**: Critical fixes only
- **EOL**: No updates

---

## Breaking Changes

### v1.0.0
❌ Không có breaking changes (first major release)

### Upcoming (v2.0.0)
🔜 Possible breaking changes:
- API endpoint changes
- Database schema update
- Config file format

---

## Migration Guides

### v0.9 → v1.0
✅ **100% compatible** - không cần làm gì

```
1. Close TeSHub v0.9
2. Download v1.0
3. Run installer
4. Start v1.0
→ All data migrated automatically
```

### Beta → v1.0
```
1. Settings → Reset (optional)
2. Upgrade to v1.0
3. Re-enter API keys (if needed)
4. Test connection
```

---

## Release Notes History

### Latest 5 Releases

1. **v1.0.0** (Mar 8, 2026) - Initial release ⭐
2. **v0.9.0-beta** (Feb 28, 2026) - Final beta
3. **v0.8.0-alpha** (Feb 10, 2026) - Alpha
4. v0.5.0-dev (Jan 2026) - Early development
5. v0.1.0-pre (Dec 2025) - Pre-release

**Lưu ý**: v0.x là development versions, không được hỗ trợ.

---

## Commit History (Main Branch)

```
Latest commits:
df2ac64 feat: add Telegram support link for Qwen3 free plan users
b58f66f refactor: move buy API key button to developer menu
8f12a30 feat: add buy API key button in sidebar with dialog
697faf4 fix: clarify all feature labels to show what each 'Yes' feature includes
7ad3b9e feat: enhance plan comparison UI with detailed feature descriptions
```

**View Full History**: [GitHub Commits](https://github.com/teshub/desktop/commits/main)

---

## Contribution & Development

### Đóng Góp Code

Nếu muốn contribute:

1. Fork repo: [github.com/teshub/desktop](https://github.com/teshub/desktop)
2. Create branch: `feature/your-feature`
3. Commit changes
4. Push & create PR
5. Team review & merge

**Guidelines**:
- Follow code standards (see docs/)
- Add tests
- Update docs
- Sign commits

### Report Issues

GitHub Issues: [github.com/teshub/desktop/issues](https://github.com/teshub/desktop/issues)

---

## Publish Schedule

**Normal Release Cycle**:
- **MINOR**: Hàng tháng (mới features)
- **PATCH**: Hàng tuần (bug fixes)
- **MAJOR**: Mỗi quarter (major redesign)

**Notification**:
- 📧 Email subscribers
- 📢 GitHub Releases
- 🔔 Telegram Channel
- 💬 Zalo Group

---

## Feedback & Bug Reports

### Report Bug

**Where**: [GitHub Issues](https://github.com/teshub/desktop/issues)

**Include**:
- Version (v1.0.0)
- OS (Windows 11, macOS 13, v.v.)
- Steps to reproduce
- Error message / screenshot
- Expected vs Actual

### Request Feature

**Where**: Same as bug reports or [teshub.dev/feedback](https://teshub.dev/feedback)

**Include**:
- Feature description
- Use case
- Expected behavior
- Priority (High/Medium/Low)

---

## License & Usage Terms

### Software License

**TeSHub Desktop** is released under:
- **Proprietary License** for v1.0.0
- **Open Source (MIT)** pending (planned for v2.0)

See [LICENSE](../LICENSE) file.

### Dependencies

All dependencies listed in `package.json`:
- ElevenLabs JS SDK: ISC License
- React: MIT License
- Electron: MIT License
- v.v.

See [LICENSES](../LICENSES) directory for full list.

---

## Code of Conduct

### Community Guidelines

- ✅ Be respectful & inclusive
- ✅ Give constructive feedback
- ✅ Help others in community
- ❌ No spam, harassment, or abuse

**Violation Report**: [conduct@teshub.dev](mailto:conduct@teshub.dev)

---

*Lần cập nhật cuối: 2026-03-08*

[← Quay Lại](./INTRODUCTION.md)

**Subscribe để nhận thông báo cập nhật:**
- 📧 [Newsletter](https://teshub.dev/subscribe)
- 🔔 [Telegram](https://t.me/TeSHub_News)
- 🌐 [RSS Feed](https://teshub.dev/changelog/rss)

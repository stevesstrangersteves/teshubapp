# Câu Hỏi Thường Gặp (FAQ)

Câu trả lời nhanh cho những câu hỏi phổ biến về TeSHub Desktop.

---

## 🎯 Câu Hỏi Chung

### Tệp TeSHub Desktop là gì?
**A**: Là ứng dụng desktop (Electron) giúp bạn tạo âm thanh AI mà không cần code:
- Chuyển đổi text → giọng nói (TTS)
- Phiên âm audio → text (STT)
- Tạo SFX, nhạc, v.v.

Hoàn toàn miễn phí (FREE plan) hoặc $9.99/tháng (TRIAL/PRO).

### Có phí giấy phép không?
**A**:
- ✅ **FREE**: Hoàn toàn miễn phí
- ✅ **TRIAL**: $9.99 USD/tháng
- ✅ **PRO**: $29.99 USD/tháng

Không bắt buộc - FREE plan đủ sử dụng cơ bản.

### Tớ có cần API key ElevenLabs không?
**A**: **Có**. TeSHub Desktop là giao diện cho ElevenLabs API. Bạn cần:
1. Tài khoản ElevenLabs (miễn phí)
2. API key từ [elevenlabs.io](https://elevenlabs.io)
3. Dán vào TeSHub Settings

Quota dùng từ ElevenLabs, không riêng từ TeSHub.

### Dữ liệu của tôi được lưu ở đâu?
**A**:
- **API Key**: Mã hóa AES-256, lưu locally (máy bạn)
- **Settings**: Máy bạn (`~/.config/TeSHub Desktop` hoặc `%APPDATA%\`)
- **Audio files**: Downloads folder (do bạn chọn)
- **Không có**: Cloud sync (dữ liệu không upload server)

### TeSHub có theo dõi/collect dữ liệu không?
**A**:
- ✅ **Anonymous analytics** (dùng Google Analytics)
  - Track: App crash, feature usage (không có nội dung)
  - Bị tắt nếu bạn chọn "Disable Analytics"
- ❌ **Không collect**: Audio, API keys, text content
- ❌ **Không sell data** cho bất kỳ ai

Xem chi tiết: [Privacy Policy](https://teshub.dev/privacy)

---

## 🚀 Getting Started

### Tôi bắt đầu từ đâu?
**A**: Theo 3 bước:
1. **Cài đặt** TeSHub: [Installation Guide](./INSTALLATION.md)
2. **Nhập API key** ElevenLabs: [API Config](./API_CONFIGURATION.md)
3. **Tạo audio đầu tiên**: [Getting Started](./GETTING_STARTED.md)

Tất cả chỉ mất 5 phút.

### Tôi không có API key, sao?
**A**:
1. Tạo tài khoản [elevenlabs.io](https://elevenlabs.io) (miễn phí)
2. Vào **Profile** → **API Key**
3. Copy key (format: `sk_...`)
4. Dán vào TeSHub Settings

**Free plan ElevenLabs**: ~10,000 ký tự/tháng (đủ test).

### TeSHub cần internet không?
**A**:
- ✅ **Cần**: Khi generate audio (call API ElevenLabs)
- ❌ **Không cần**: Khi sử dụng settings, open file, v.v.

Nếu mất internet lúc generate → error.

### Các hệ thống đã được test?
**A**:
- ✅ **Windows**: 10, 11 (32GB RAM tested)
- ✅ **macOS**: 12, 13, 14 (Intel + Apple Silicon)
- ✅ **Linux**: Ubuntu 20.04 LTS + Fedora

Minimum: Windows 10, macOS 12, Ubuntu 20.04.

---

## 💵 Giá Cả & Plan

### Sự khác biệt giữa FREE, TRIAL, PRO là gì?
**A**:
| Tính Năng | FREE | TRIAL | PRO |
|-----------|------|-------|-----|
| TTS | ✅ | ✅ | ✅ |
| STT | ❌ | ✅ | ✅ |
| Parallel TTS | ❌ | 2x | 10x |
| Max Keys | 1 | 5 | ∞ |
| Giá | Free | $9.99/mo | $29.99/mo |

Xem chi tiết: [Licensing Guide](./LICENSING.md)

### Nâng cấp từ FREE → TRIAL dễ không?
**A**: **Rất dễ**:
1. Settings → **Upgrade**
2. Chọn **TRIAL**
3. Thanh toán (Credit card, PayPal, v.v.)
4. Kích hoạt ngay

Không cần reinstall, tất cả dữ liệu giữ lại.

### Có thể hủy subscription bất cứ lúc nào?
**A**: **Có**. Anytime, không phí hủy:
1. Settings → Billing
2. Nhấp **Cancel Subscription**
3. Downgrade tự động về FREE

Bạn sẽ không bị charge tiếp.

### Refund được không?
**A**: **Có chính sách 30 ngày**. Nếu không hài lòng:
1. Email: [billing@teshub.dev](mailto:billing@teshub.dev)
2. Yêu cầu refund (trong 30 ngày)
3. Team kiểm tra & xử lý

---

## 🔧 Troubleshooting - Xử Lý Lỗi

### Ứng dụng không khởi động
**A**: Thử các bước:

1. **Restart máy**
2. **Cài đặt lại TeSHub**:
   - Gỡ cài đặt hoàn toàn
   - Download phiên bản mới
   - Cài đặt lại
3. **Kiểm tra yêu cầu hệ thống**:
   - Windows 10+, macOS 12+, v.v.
   - RAM 4GB+
   - 2GB ổ cứng trống

Nếu vẫn lỗi → [Contact Support](./CONTACT_SUPPORT.md)

### "Invalid API Key" error
**A**:

1. **Kiểm tra key**:
   - Vào [elevenlabs.io](https://elevenlabs.io)
   - Settings → API Key
   - Copy key lại (full, không thiếu ký tự)

2. **Dán lại TeSHub**:
   - Settings → ElevenLabs API
   - Xóa key cũ
   - Dán key mới
   - Nhấp **Test Connection**

3. **Nếu vẫn lỗi**:
   - Có thể key bị revoked
   - Tạo key mới trên ElevenLabs
   - Dán vào TeSHub

### "Quota Exceeded" error
**A**: Nghĩa là đã dùng hết dung lượng tháng này.

**Giải pháp**:
1. **Chờ tháng mới reset** (tự động reset theo ngày đó)
2. **Nâng cấp plan ElevenLabs**:
   - Vào [elevenlabs.io/pricing](https://elevenlabs.io/pricing)
   - Chọn Starter, Professional, v.v.
   - Thanh toán
3. **Dùng key khác** (nếu có multiple keys)

### Audio không phát
**A**:

1. **Kiểm tra loa/headphone**:
   - Plug vào
   - Test volume hệ thống

2. **Thử file khác**:
   - Generate audio TTS mới
   - Test lại

3. **Kiểm tra file output**:
   - Vào Downloads
   - Đúng file MP3 không?
   - Đúng dung lượng không?

4. **Xem browser/player**:
   - File có chơi được bằng VLC/Media Player không?
   - Nếu không → File bị lỗi, tạo lại

### TTS generate rất chậm
**A**:

1. **Kiểm tra internet**:
   - Speed test: [speedtest.net](https://speedtest.net)
   - Cần tối thiểu 2Mbps

2. **Giảm độ dài text**:
   - Text dài → tách thành mấy phần
   - 1000 ký tự tốt nhất

3. **Dùng Parallel TTS**:
   - Nếu TRIAL/PRO, dùng Parallel mode
   - Nhanh hơn 5-10x

4. **Server ElevenLabs overload**:
   - Chờ 5-10 phút
   - Thử lại

### STT không chính xác
**A**:

1. **Kiểm tra chất lượng audio**:
   - Audio rõ không? Có tiếng ồn không?
   - Rất ồn → STT sai

2. **Chọn ngôn ngữ đúng**:
   - Vietnamese (vi-VN)
   - English (en-US)
   - v.v.

3. **Upload file hợp lệ**:
   - Format: MP3, WAV, M4A, FLAC, OPUS
   - Size: < 100MB
   - Duration: < 12 hours

4. **Thêm keywords**:
   - Nếu có từ chuyên ngành
   - VD: "Python", "API", "AI"
   - STT sẽ chính xác hơn

---

## 🔑 API Key Management

### Cần bao nhiêu API key?
**A**:
- **1 key**: Đủ cho hầu hết (free plan)
- **3-5 keys**: Dự án nhiều (TRIAL plan cho 5)
- **10+ keys**: Team lớn (PRO plan cho unlimited)

Mục đích: Chia sẻ quota, A/B testing, redundancy.

### Làm sao nhập hàng loạt keys?
**A**: Dùng CSV import:

1. **Chuẩn bị CSV** (Excel hoặc Text):
   ```csv
   key,label
   sk_xxx...,Project A
   sk_yyy...,Project B
   sk_zzz...,Backup
   ```

2. **Mở TeSHub** → Tab **Key Manager**

3. **Nhấp "Import from File"**

4. **Chọn file CSV**

5. **Review & Nhấp "Import"**

Done! Tất cả keys thêm vào.

### Key nào được dùng khi generate?
**A**: Phụ thuộc **Selection Strategy**:
- **Least-Used**: Key với dung lượng dư nhiều nhất
- **Round-Robin**: Quay vòng (Key 1 → 2 → 3 → 1)
- **Random**: Chọn ngẫu nhiên

Chọn trong **Settings**.

### Key bị hết dung lượng, sao?
**A**:

1. **Status**: Sẽ thay đổi thành "EXHAUSTED"
2. **Tự động skip**: Không dùng key này
3. **Dùng key khác**: Nếu có

**Để fix**:
- Nâng cấp quota ElevenLabs
- Hoặc xóa/disable key cũ

---

## 🎵 TTS & Audio

### TTS output format là gì?
**A**:
- **Format**: MP3 (192 kbps, high quality)
- **Codec**: MPEG-1 Layer III
- **Sample Rate**: 44.1 kHz
- **Bitrate**: 192 kbps

Compatible với hầu hết players & editors.

### Tôi có thể export sang WAV không?
**A**:
- **Hiện tại**: Chỉ MP3
- **Planned**: WAV export (v1.1.0)

**Workaround**: Dùng Audacity convert MP3 → WAV (miễn phí).

### Giọng nên chọn như thế nào?
**A**: Tùy nội dung:

| Nội Dung | Giọng Nam | Giọng Nữ |
|----------|-----------|----------|
| Podcast | Liam, Patrick | Rachel, Sarah |
| Audiobook | Anthony, Matthew | Olivia, Stella |
| Video | Chris, Ethan | Ava, Freya |
| Formal | George, Daniel | Rachel, Olivia |

**Tips**: Test từ 2-3 giọng, chọn cái thích.

### Có thể tạo custom voice không?
**A**:
- **Hiện tại**: Không (dùng voices ElevenLabs)
- **Planned**: Voice cloning (v2.0.0)

Tạm thời: Chọn giọng tương tự nhất.

---

## 🔒 Bảo Mật & Privacy

### API key có bị leak không?
**A**:
- ✅ **Mã hóa AES-256**: Lưu encrypted locally
- ✅ **Không upload**: Không gửi server (except API calls)
- ✅ **Secure**: Tương tự mật khẩu

**Best Practice**: Không share, không commit vào git.

### Tôi có thể xóa dữ liệu không?
**A**: **Có**:

1. **Settings** → **Privacy**
2. Chọn xóa:
   - ✅ Clear Cache
   - ✅ Remove API Keys
   - ✅ Reset Settings
   - ✅ Clear History
3. Nhấp **Clear**

Dữ liệu sẽ xóa hoàn toàn.

### GDPR compliance?
**A**: **Có**:
- ✅ Xóa dữ liệu được
- ✅ Data portability
- ✅ Privacy controls
- ✅ Opt-out analytics

Xem [Privacy Policy](https://teshub.dev/privacy).

---

## ⚡ Performance

### Parallel TTS limit là bao nhiêu?
**A**:
- **FREE**: Không có (sequential only)
- **TRIAL**: 2 concurrent
- **PRO**: 10 concurrent

Ý nghĩa: Bao nhiêu requests cùng lúc được process.

### Tốc độ TTS average?
**A**: ~1000 ký tự/giây (rough):
```
100 ký tự = ~0.1 giây
1000 ký tự = ~1 giây
10000 ký tự = ~10 giây

+ Network latency (~0.5-1 giây)
```

### App chiếm bao nhiêu RAM?
**A**: ~150-300 MB (típical):
- Idle: ~100 MB
- Batch TTS: ~300-500 MB
- With files open: ~200-400 MB

Recommended: 4GB minimum, 8GB preferred.

---

## 🌍 Ngôn Ngữ & Localization

### TeSHub hỗ trợ ngôn ngữ nào?
**A**:
- ✅ **Tiếng Việt** (vi-VN) - Primary
- ✅ **Tiếng Anh** (en-US) - Secondary
- 🔜 **Tiếng Trung** (zh-CN) - Planned
- 🔜 **Tiếng Nhật** (ja-JP) - Planned

Có thể thay đổi trong **Settings** → **Language**.

### TTS hỗ trợ bao nhiêu ngôn ngữ?
**A**: 99+ ngôn ngữ (tùy ElevenLabs):
- ✅ Tiếng Việt
- ✅ Tiếng Anh
- ✅ Tiếng Trung, Nhật, Hàn
- ✅ Tiếng Tây Ban Nha, Pháp, Đức
- ✅ v.v. (tất cả ngôn ngữ chính)

Chọn trong **TTS Settings** → **Language**.

---

## 🆘 Liên Hệ Hỗ Trợ

### Cách liên hệ nếu cần giúp?
**A**: Xem [Contact & Support](./CONTACT_SUPPORT.md):
- 🤖 **Telegram Bot**: @TeSHub_Support_Bot
- 💬 **Zalo Group**: zalo.me/g/teshub
- ✉️ **Email**: support@teshub.dev
- 🌐 **Website**: teshub.dev/contact

Phản hồi thường 1-4 giờ.

### Có thể gặp founder trực tiếp không?
**A**: Có, founder: [@lap14503](https://t.me/lap14503)

Gửi message, sẽ phản hồi (nếu sử dụng).

---

## 📖 Tài Liệu

### Tài liệu đầy đủ ở đâu?
**A**: Trong ứng dụng & online:

**In-app**:
- Help menu
- Tooltips
- Context help

**Online**:
- [teshub.dev/docs](https://teshub.dev/docs)
- [User Guide](./USAGE_GUIDE.md)
- [Features](./FEATURES.md)

### Có tutorial video không?
**A**:
- 🎥 **YouTube Channel**: [TeSHub Channel](https://youtube.com/@teshub)
- 🎥 **Tutorials**: Video setup, features, tips
- 🎥 **Community**: Community members tạo tutorials

---

## 🎁 Khác

### Có referral program không?
**A**: **Có!** [teshub.dev/referral](https://teshub.dev/referral):
- Share link: `teshub.dev/ref/YOUR_ID`
- Bạn tham gia → Bạn được $5
- Bạn nó được 20% discount

Unlimited referrals.

### Có discount/promo không?
**A**:
- 🎉 **Black Friday**: 50% off
- 🎉 **Seasonal**: Holiday promotions
- 🎉 **Referral**: $5 off per friend

Subscribe [newsletter](https://teshub.dev/newsletter) để nhận thông báo.

### TeSHub có open source không?
**A**:
- **Hiện tại**: Proprietary (v1.0)
- **Planned**: Open source (v2.0)

Codebase có thể sẽ release MIT license năm 2027.

---

## 📞 Nếu Không Tìm Được Câu Trả Lời

1. **Xem lại tài liệu**: [User Guide](./USAGE_GUIDE.md), [Features](./FEATURES.md)
2. **Hỏi cộng đồng**: [Zalo Group](zalo.me/g/teshub)
3. **Liên hệ support**: [Contact & Support](./CONTACT_SUPPORT.md)

**Response time**: 1-24h (tuỳ channel).

---

*Lần cập nhật cuối: 2026-03-08*

*Không tìm thấy câu trả lời? [Liên hệ chúng tôi!](./CONTACT_SUPPORT.md)*

[← Quay Lại](./INTRODUCTION.md)

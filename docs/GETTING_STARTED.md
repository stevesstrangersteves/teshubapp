# Bắt Đầu Nhanh (Getting Started)

Hướng dẫn nhanh để tạo file audio đầu tiên với TeSHub Desktop.

## Điều Kiện Tiên Quyết

Trước khi bắt đầu, bạn cần:

1. **Cài đặt TeSHub Desktop** - Xem [Hướng Dẫn Cài Đặt](./INSTALLATION.md)
2. **Tài khoản ElevenLabs** - Đăng ký tại [elevenlabs.io](https://elevenlabs.io)
3. **ElevenLabs API Key** - Lấy từ dashboard ElevenLabs

## Bước 1: Lấy ElevenLabs API Key

### Cách 1: Sử Dụng Free Plan
1. Truy cập [elevenlabs.io](https://elevenlabs.io)
2. Đăng ký tài khoản (miễn phí)
3. Vào **Profile** → **API Key**
4. Nhấp **Copy** để sao chép key (bắt đầu với `sk_...`)

### Cách 2: Sử Dụng Paid Plan
1. Chọn plan phù hợp (Starter, Professional, Scale)
2. Lấy API key từ dashboard
3. Quota sẽ tùy theo plan

## Bước 2: Nhập API Key vào TeSHub Desktop

1. **Mở TeSHub Desktop**
2. **Vào Settings** (⚙️ icon hoặc tab "Settings")
3. **Tìm "ElevenLabs API Key"**
4. **Dán API key** vào ô input
5. **Nhấp "Save"** hoặc **"Test Connection"** để kiểm tra

```
✅ Nếu hiển thị "Connected" → API key hợp lệ
❌ Nếu hiển thị lỗi → Kiểm tra lại API key
```

## Bước 3: Tạo Text-to-Speech Đầu Tiên

1. **Chọn Tab "Text-to-Speech"**
2. **Nhập văn bản** vào ô input (ví dụ: "Xin chào thế giới")
3. **Chọn Giọng** từ dropdown (ví dụ: "Rachel", "Antoni")
4. **Điều chỉnh cài đặt** (tuỳ chọn):
   - Stability: 0.0 (thay đổi) → 1.0 (ổn định)
   - Similarity Boost: 0.0 → 1.0 (giống giọng gốc hơn)
5. **Nhấp "Generate"** hoặc **"Play Preview"**
6. **Nhấp "Download"** để lưu file MP3

```
Ví dụ cấu hình:
├─ Văn bản: "Xin chào, đây là giọng AI"
├─ Giọng: Rachel
├─ Stability: 0.75
├─ Similarity: 0.75
└─ 📥 Download → tts_output_2026_03_08.mp3
```

## Bước 4: Sử Dụng Các Tính Năng Khác

### 🔄 Xử Lý Song Song (Parallel TTS)
1. Vào tab **"Parallel TTS"**
2. **Nhập nhiều dòng** (mỗi dòng = 1 câu)
3. **Chọn giọng** cho từng dòng (tuỳ chọn)
4. **Nhấp "Generate All"**
5. Tất cả file sẽ được tạo cùng lúc

### 🎙️ Speech-to-Text (STT)
1. Vào tab **"Speech-to-Text"**
2. **Upload file audio** (MP3, WAV, M4A, v.v.)
3. **Chọn ngôn ngữ** (Tiếng Việt, English, v.v.)
4. **Nhấp "Transcribe"**
5. Kết quả text xuất hiện + download SRT (subtitle)

### 🔊 Hiệu Ứng Âm Thanh
1. Vào tab **"Sound Effects"**
2. **Mô tả hiệu ứng** (ví dụ: "tiếng mưa ngoài trời")
3. **Chọn độ dài** (5-30 giây)
4. **Nhấp "Generate"**
5. **Download** file MP3

### 🎵 Soạn Nhạc
1. Vào tab **"Music"**
2. **Mô tả nhạc** (ví dụ: "lo-fi nhẹ nhàng")
3. **Chọn độ dài**
4. **Nhấp "Compose"**
5. **Download** bản nhạc

## Bước 5: Quản Lý API Keys (Nâng Cao)

Nếu có nhiều API keys:

1. Vào tab **"Key Manager"**
2. **Thêm Key**: Nhập key → Nhấp "Add"
3. **Nhập Hàng Loạt**: Upload CSV/Excel với cột "key"
4. **Xem Thống Kê**: Dung lượng sử dụng, số request
5. **Xóa/Disable**: Chọn key → Nhấp "Remove"

## Cấu Hình Khuyên Cáo

### Cho Người Mới Bắt Đầu
| Cài Đặt | Giá Trị | Ghi Chú |
|---------|--------|--------|
| Stability | 0.75 | Cân bằng tự nhiên |
| Similarity | 0.75 | Giọng nhất quán |
| Tốc Độ | 1.0 | Tốc độ bình thường |
| Speaker Boost | Tắt | Âm thanh tự nhiên hơn |

### Cho Podcast/Audiobook
| Cài Đặt | Giá Trị | Ghi Chú |
|---------|--------|--------|
| Stability | 0.95 | Ổn định cao |
| Similarity | 0.85 | Giọng sâu, ổn định |
| Tốc Độ | 0.9-1.0 | Phát âm rõ ràng |
| Speaker Boost | Bật | Âm thanh mạnh hơn |

## Lưu Ý Quan Trọng

### Giới Hạn & Quota
- **Free Plan**: ~10,000 ký tự/tháng
- **Paid Plans**: Tùy gói (Starter: 100k, Professional: 1M+)
- Kiểm tra quota trên [dashboard ElevenLabs](https://elevenlabs.io/app/usage)

### Định Dạng File
Hỗ trợ:
- **TTS Output**: MP3 (192 kbps)
- **STT Input**: MP3, WAV, M4A, FLAC, OPUS, ULAW
- **Max File Size**: 100MB

### Tốc Độ Xử Lý
- **TTS**: ~1000 ký tự/giây (tuỳ kết nối)
- **STT**: Tùy độ dài file
- **Parallel**: 2-10 request song song (tuỳ plan)

## Các Tính Năng Tiếp Theo

Sau khi thành thạo cơ bản:
- 🔍 Xem [Hướng Dẫn Sử Dụng Chi Tiết](./USAGE_GUIDE.md)
- 🎨 Khám Phá [Tính Năng Nâng Cao](./FEATURES.md)
- 🔐 Cấu Hình [Kimi AI Integration](./KIMI_INTEGRATION.md)
- 💳 Nâng Cấp [Pro Plan](./LICENSING.md)

## Gặp Vấn Đề?

### "Invalid API Key" Error
- Kiểm tra key ở [elevenlabs.io/app/settings](https://elevenlabs.io/app/settings)
- Đảm bảo copy đủ key (không thiếu ký tự)
- Thử xóa & nhập lại key

### "Quota Exceeded" Error
- Hết dung lượng tháng này
- Nâng cấp plan hoặc chờ reset (tháng sau)
- Kiểm tra quota: [elevenlabs.io/app/usage](https://elevenlabs.io/app/usage)

### Audio Không Phát
- Kiểm tra loa/headphone
- Thử file khác
- Kiểm tra volume hệ thống

Xem thêm [FAQ](./FAQ.md) để giải pháp khác.

---

**Chúc mừng!** 🎉 Bạn đã sẵn sàng tạo âm thanh AI.

*Cần trợ giúp thêm? [Liên Hệ](./CONTACT_SUPPORT.md)*

*Lần cập nhật cuối: 2026-03-08*

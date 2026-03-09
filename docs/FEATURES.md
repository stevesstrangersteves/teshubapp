# Tính Năng (Features)

Khám phá tất cả tính năng mạnh mẽ của TeSHub Desktop.

## 🗣️ Text-to-Speech (TTS)

Chuyển bất kỳ văn bản nào thành giọng nói tự nhiên và sống động.

### Tính Năng Chính
- **Đa Giọng**: Chọn từ 30+ giọng AI (nam, nữ, nhiều ngôn ngữ)
- **Điều Chỉnh Tự Do**:
  - Stability: Kiểm soát độ thay đổi giọng (0-100%)
  - Similarity: Giữ nguyên giọng gốc (0-100%)
  - Speaker Boost: Tăng âm lượng (bật/tắt)
- **Tốc Độ Phát**: Từ 0.5x → 2.0x bình thường
- **Style**: (Tùy model) Chọn phong cách: Neutral, Cheerful, sad, v.v.
- **Giới Hạn Văn Bản**: 5,000 ký tự/request
- **Định Dạng Output**: MP3 (192 kbps), WAV

### Trường Hợp Sử Dụng
✅ Tạo nội dung YouTube
✅ Podcast & Audiobooks
✅ Video TikTok/Instagram
✅ Giáo dục & e-learning
✅ Quảng cáo & Marketing

### Cách Sử Dụng
1. Mở tab **Text-to-Speech**
2. Nhập hoặc dán văn bản
3. Chọn giọng & cài đặt
4. Nhấp **Preview** để nghe mẫu
5. Nhấp **Generate** để tạo
6. Download file MP3/WAV

### Ví Dụ
```
Input: "Xin chào, tôi là AI voice"
Giọng: Rachel (Nữ, Anh)
Stability: 0.85
Similarity: 0.75
Output: ♪ tts_output.mp3 (2.3 MB, 4 giây)
```

---

## 🔄 Xử Lý Song Song (Parallel TTS)

Tạo nhiều file TTS cùng lúc - hoàn hảo cho batch processing.

### Tính Năng Chính
- **Xử Lý Đa Luồng**: 2-10 request song song (tuỳ plan)
- **Cùng Lúc**: Tất cả file được tạo song song, không tuần tự
- **Cài Đặt Riêng**: Mỗi dòng có thể có giọng & cài đặt khác
- **Tiến Độ Real-time**: Xem trạng thái từng file
- **Xuất Hàng Loạt**: Download tất cả file cùng một lần

### Trường Hợp Sử Dụng
✅ Tạo audiobook (từng chương)
✅ Podcast series (nhiều episode)
✅ Video dài với nhiều giọng
✅ Học ngoại ngữ (nhiều câu)
✅ Game narration

### Cách Sử Dụng
1. Mở tab **Parallel TTS**
2. Nhập văn bản (mỗi dòng = 1 file)
3. **Tùy chọn**: Chọn giọng cho từng dòng
4. Nhấp **Generate All** hoặc **Generate Selected**
5. Chờ hoàn thành (thường nhanh hơn tuần tự 5-10x)
6. Nhấp **Download All** hoặc chọn file riêng

### Ví Dụ
```
Dòng 1: "Chương 1: Bắt đầu" → Rachel (Nữ)
Dòng 2: "Nội dung chương 1..." → Antoni (Nam)
Dòng 3: "Kết thúc chương 1" → Rachel (Nữ)

⏱️ Xử lý: ~5 giây (song song)
vs. ~20 giây (tuần tự)
```

### Lưu Ý
- Khi xử lý song song: Dung lượng API được tính từng file
- Ví dụ: 10 dòng × 100 ký tự = 1,000 ký tự usage
- Chọn "Least-Used" strategy để cân bằng API keys

---

## 🎙️ Speech-to-Text (STT / Transcription)

Phiên âm âm thanh hoặc video thành văn bản tự động.

### Tính Năng Chính
- **Hỗ Trợ Nhiều Format**: MP3, WAV, M4A, FLAC, OPUS, ULAW
- **Đa Ngôn Ngữ**: 99+ ngôn ngữ
- **Tự Động Phát Hiện Ngôn Ngữ**: Hoặc chỉ định thủ công
- **Diarization**: Xác định ai nói (Speaker 1, Speaker 2, v.v.)
- **Timestamps**: Thời gian chính xác cho mỗi từ
- **Keyword Highlighting**: Tìm từ khóa cụ thể
- **Export Options**: Text, SRT (subtitle), JSON, VTT

### Giới Hạn
- Max File Size: 100MB
- Tốc Độ: ~1 giây file = 1 giây xử lý (trung bình)
- Mục Đích Sử Dụng: Sở hữu file gốc (không vi phạm bản quyền)

### Trường Hợp Sử Dụng
✅ Phiên âm podcast
✅ Subtitle video
✅ Ghi chép cuộc họp
✅ Dịch ngoại ngữ
✅ Lưu trữ audio

### Cách Sử Dụng
1. Mở tab **Speech-to-Text**
2. Nhấp **Upload Audio** hoặc kéo thả file
3. Chọn **Ngôn Ngữ** (hoặc để Auto-detect)
4. **(Tùy)** Bật **Diarization** (tìm nhà phát biểu)
5. Nhấp **Transcribe**
6. Chờ hoàn thành
7. **Copy** text hoặc **Download** SRT/JSON

### Ví Dụ
```
Input: podcast_episode_001.mp3 (45 phút)
Ngôn Ngữ: Tiếng Việt
Diarization: Bật

Output:
[00:00:00] Speaker 1: "Xin chào mọi người..."
[00:01:23] Speaker 2: "Chào bạn, tôi là..."
...
```

---

## 🔊 Hiệu Ứng Âm Thanh (Sound Effects)

Tạo SFX tùy chỉnh từ mô tả văn bản - không cần kinh nghiệm audio.

### Tính Năng Chính
- **Text-to-Sound**: Mô tả hiệu ứng → Tạo SFX
- **Độ Dài Tuỳ Chỉnh**: 5-30 giây
- **Loop Support**: Lặp lại vô hạn (phù hợp cho game)
- **Prompt Engineering**: Từng từ quan trọng
- **Variety**: Mỗi lần tạo có chút khác biệt

### Trường Hợp Sử Dụng
✅ Game sound effects
✅ Video game assets
✅ Podcast transitions
✅ ASMR content
✅ Audio branding

### Cách Sử Dụng
1. Mở tab **Sound Effects**
2. **Mô Tả Hiệu Ứng**: Ví dụ: "tiếng cửa mở rầm rầm"
3. **Chọn Độ Dài**: 5-30 giây
4. **Tùy Chọn**: Bật Loop, Prompt Influence (mức chi tiết)
5. Nhấp **Generate**
6. **Preview** hoặc **Download** WAV/MP3

### Ví Dụ Mô Tả
- "tiếng gõ cửa 3 lần nhẹ"
- "tiếng nước chảy từ từ, bình yên"
- "tiếng vũ khí laser sci-fi"
- "tiếng chim hót buổi sáng"
- "tiếng bàn phím máy tính gõ nhanh"

---

## 🎵 Soạn Nhạc (Music Generation)

Tạo bản nhạc gốc & bài hát với lời từ AI.

### Tính Năng Chính
- **Soạn Nhạc**: Tạo instrumental từ mô tả
- **Tạo Bài Hát**: Với lời tự tạo hoặc do bạn cung cấp
- **Thể Loại**: Pop, Jazz, Electronic, Classical, v.v.
- **Tâm Trạng**: Happy, Sad, Energetic, Calm, v.v.
- **Độ Dài**: 30 giây - 5 phút
- **Điều Chỉnh**: BPM, Key, Instruments

### Trường Hợp Sử Dụng
✅ YouTube background music
✅ Podcast intro/outro
✅ Game soundtrack
✅ Advertising music
✅ Streaming stream music

### Cách Sử Dụng
1. Mở tab **Music**
2. **Chọn Loại**: Instrumental hoặc Song (với lời)
3. **Mô Tả Nhạc**: "lo-fi chillhop buổi sáng"
4. **(Song)** Nhập **Lời Bài Hát** (nếu cần)
5. **Chọn Độ Dài**
6. Nhấp **Compose**
7. **Download** MP3/WAV

### Ví Dụ Mô Tả
- "upbeat electronic dance music energetic"
- "relaxing piano solo melancholic"
- "cinematic orchestral epic"
- "lo-fi hip-hop chill study"

---

## 🔐 Quản Lý Key Pool (API Key Manager)

Quản lý nhiều ElevenLabs API keys hiệu quả.

### Tính Năng Chính
- **Thêm/Xóa Key**: Quản lý multiple keys
- **Theo Dõi Sử Dụng**:
  - Ký tự đã dùng (characters)
  - Giới hạn ký tự (character limit)
  - Số request (request count)
  - Lỗi (error count)
- **Tự Động Xoay Chuyển**: 3 strategy:
  - **Least-Used**: Chọn key dùng ít nhất
  - **Round-Robin**: Quay vòng lần lượt
  - **Random**: Chọn ngẫu nhiên
- **Mã Hóa Bảo Mật**: AES-256-GCM
- **Nhập Hàng Loạt**: CSV/Excel import
- **Lịch Sử Sử Dụng**: Snapshot theo thời gian
- **Health Check**: Kiểm tra key hoạt động/hết dung lượng

### Trường Hợp Sử Dụng
✅ Nhiều dự án có API key riêng
✅ Đội làm việc cần chia sẻ keys
✅ Tối ưu quota sử dụng
✅ A/B testing keys
✅ Phát hiện keys lỗi sớm

### Cách Sử Dụng

#### Thêm Key Đơn Lẻ
1. Mở tab **Key Manager**
2. Nhấp **Add Key**
3. Dán API key
4. Nhập Label (tên gợi nhớ): "Project A - Main"
5. Nhấp **Add**

#### Nhập Hàng Loạt (CSV/Excel)
1. Chuẩn bị file CSV:
   ```
   key,label
   sk_xxxx...,Project A
   sk_yyyy...,Project B
   ```
2. Nhấp **Import from File**
3. Chọn file CSV
4. Xem preview → Nhấp **Import**

#### Chọn Strategy (Chiến Lược)
1. Vào **Settings**
2. Tìm **Key Selection Strategy**
3. Chọn:
   - **Least-Used**: Cân bằng dung lượng
   - **Round-Robin**: Công bằng
   - **Random**: Ngẫu nhiên
4. **Save**

#### Xem Thống Kê
1. Vào tab **Key Manager**
2. Chọn key để xem chi tiết
3. Xem:
   - Sử dụng: X / Y ký tự
   - Request count
   - Last used: Lần cuối dùng
   - Error count: Lỗi

#### Xóa Key
1. Chọn key
2. Nhấp **Delete** hoặc **Disable**
3. Xác nhận

### Ví Dụ Quản Lý
```
Key 1 (Project A): 800k / 1M ký tự
Key 2 (Project B): 200k / 1M ký tự
Key 3 (Backup):    10k / 100k ký tự

Strategy: Least-Used
→ TTS request sẽ dùng Key 3 trước (dung lượng dư)
```

---

## 🌐 Tích Hợp Kimi AI (Bonus Feature)

Sử dụng mô hình Kimi AI để tối ưu văn bản trước TTS.

**Tính Năng:**
- Tối ưu văn bản (ngữ pháp, dễ phát âm)
- Dịch ngoại ngữ tự động
- Tóm tắt văn bản dài

Xem chi tiết: [Kimi AI Integration](./GITBOOK_KIMI_INTEGRATION.md)

---

## 📊 So Sánh Các Plan

| Tính Năng | FREE | TRIAL | PRO |
|-----------|------|-------|-----|
| TTS | ✅ | ✅ | ✅ |
| STT | ❌ | ✅ | ✅ |
| Music | ❌ | ❌ | ✅ |
| Parallel TTS | ❌ | ✅ (2x) | ✅ (10x) |
| Max Keys | 1 | 5 | ∞ |
| Character Limit | ~10k/mo | ~100k/mo | ∞ |

Xem [Licensing](./GITBOOK_LICENSING.md) để nâng cấp.

---

## 🔍 Cách Chọn Giọng

### Cho Podcast
- **Nam**: Anthony, George, Winston
- **Nữ**: Rachel, Bella, Serena
- Stability: 0.90+ (ổn định)

### Cho Video Marketing
- **Năng Lượng**: Chris, Ethan (nam), Ava, Freya (nữ)
- Boost: Bật để âm thanh nổi bật

### Cho E-learning
- **Rõ Ràng**: Daniel, Matthew (nam), Olivia, Stella (nữ)
- Tốc Độ: 0.85-0.95 (phát âm rõ)

### Cho Audiobook
- **Tự Nhiên**: Liam, Patrick (nam), Nova, Sarah (nữ)
- Stability: 0.95+ (tự nhiên nhất)

---

*Lần cập nhật cuối: 2026-03-08*

[← Quay Lại](./GITBOOK_INTRODUCTION.md)

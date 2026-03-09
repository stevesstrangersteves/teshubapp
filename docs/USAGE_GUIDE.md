# Hướng Dẫn Sử Dụng Chi Tiết (Usage Guide)

Hướng dẫn chi tiết từng tính năng của TeSHub Desktop.

## Text-to-Speech: Hướng Dẫn Đầy Đủ

### 1. Chuẩn Bị Văn Bản

#### Định Dạng Văn Bản Tốt
✅ Văn bản rõ ràng, không lỗi chính tả
✅ Chia thành các đoạn ngắn (< 500 ký tự)
✅ Dùng dấu câu đúng (. ! ? , ;)
✅ Rõ ràng cách phát âm (viết rõ tên riêng)

#### Ví Dụ Tốt vs. Xấu
```
❌ Xấu: tôi tên là a b c đến từ việt nam ngành công nghệ
✅ Tốt: Tôi tên là ABC, đến từ Việt Nam, ngành công nghệ thông tin.

❌ Xấu: pd là gì? nó là phicb
✅ Tốt: PD là gì? Nó là Product Development.
```

### 2. Chọn Giọng Phù Hợp

#### Danh Sách Giọng (30+)
**Giọng Nam:**
- **Casual**: Antoni, Chris, Ethan, Isaac
- **Professional**: George, Liam, Patrick
- **Character**: Josh, Michael, Oliver

**Giọng Nữ:**
- **Casual**: Ava, Emma, Ivy, Mimi
- **Professional**: Rachel, Sarah, Stella
- **Character**: Bella, Freya, Gigi, Grace

#### Chọn Giọng Dựa Trên
1. **Nội Dung**: Trang trọng → Formal giọng | Vui nhộn → Casual
2. **Độ Tuổi Nhân Vật**: Trẻ → Higher pitch | Già → Lower pitch
3. **Giới Tính Nhân Vật**: Phù hợp (tùy chọn)
4. **Ngôn Ngữ**: Giọng nam/nữ tiếng Anh - tất cả hỗ trợ nhiều ngôn ngữ

### 3. Điều Chỉnh Cài Đặt

#### Stability (0-100%)
**Ý Nghĩa**: Độ thay đổi / biến thiên của giọng

| Giá Trị | Tác Dụng | Trường Hợp Sử Dụng |
|---------|----------|-------------------|
| 0-25% | Rất thay đổi, tự nhiên | Creative content, variety |
| 25-50% | Thay đổi, tự nhiên | Conversational style |
| 50-75% | Cân bằng | Audiobooks, default |
| 75-100% | Ổn định, khác | Robotic, professional |

**Khuyên Cáo**: 70-85% cho hầu hết trường hợp

#### Similarity Boost (0-100%)
**Ý Nghĩa**: Giữ nguyên đặc trưng giọng gốc

| Giá Trị | Tác Dụng |
|---------|----------|
| 0% | Giọng hoàn toàn khác, lạ |
| 50% | Trung bình |
| 100% | Giống giọng gốc nhất |

**Khuyên Cáo**: 75-100% để giọng nhất quán

#### Speaker Boost
**Tác Dụng**: Tăng giọng nói (làm to hơn, nổi hơn)

- **Tắt (Off)**: Giọng bình thường, tự nhiên
- **Bật (On)**: Giọng lớn, nổi bật, chuyên nghiệp

**Khuyên Cáo**: Bật cho Podcast/Video, Tắt cho Audiobook

#### Speed (Tốc Độ)
**Phạm Vi**: 0.5x → 1.0x → 2.0x

| Tốc Độ | Khi Nào |
|--------|---------|
| 0.5-0.75x | Audiobook, formal, emphasis |
| 0.9-1.0x | Normal, podcast (mặc định) |
| 1.25-1.5x | YouTube, video ngắn |
| 1.5-2.0x | Tutorial, hướng dẫn nhanh |

### 4. Generate & Download

#### Bước Thực Hiện
1. **Nhập Văn Bản** (hoặc copy-paste)
2. **Chọn Giọng**
3. **Điều Chỉnh Cài Đặt**
4. **Nhấp "Play Preview"** - Nghe mẫu 5-10 giây đầu
5. Nếu OK → **Nhấp "Generate"**
6. Chờ hoàn thành (thanh tiến độ)
7. **"Download"** để lưu file

#### File Output
- **Format**: MP3 (128-192 kbps)
- **Tên File**: `tts_output_[timestamp].mp3`
- **Lưu ở**: Downloads folder

### 5. Pro Tips

#### Tối Ưu Chất Lượng
```
1. Chia văn bản 500-1000 ký tự/file
2. Dùng dấu câu rõ ràng (. ! ? ,)
3. Dành thời gian cho pause: "...trong tương lai. Tiếp theo..."
4. Test với từ khó phát âm trước generate lớn
```

#### Tối Ưu Tốc Độ
```
1. Dùng Parallel TTS cho >3 đoạn
2. Batch generate (tất cả cùng lúc) thay vì từng cái
3. Giảm Stability → nhanh hơn 10-20%
```

#### Xử Lý Lỗi Phát Âm
Nếu từ nào phát âm sai:
```
Cách 1: Viết lại cách phát âm
❌ "SQL" → ✅ "S-Q-L" hoặc "Sequel"

Cách 2: Thêm dấu câu
❌ "API là..." → ✅ "A-P-I là..."

Cách 3: Chọn giọng khác (một giọng nó sẽ đúng)
```

---

## Parallel TTS: Hướng Dẫn Batch Processing

### 1. Chuẩn Bị Batch

#### Cấu Trúc Văn Bản
```
Dòng 1: "Câu thứ nhất"
Dòng 2: "Câu thứ hai"
Dòng 3: "Câu thứ ba"
...
```

Mỗi dòng = 1 file output riêng

#### Chuẩn Bị CSV (Nhập Hàng Loạt)
```csv
text,voice,stability,similarity
"Xin chào",Rachel,0.75,0.75
"Cảm ơn bạn",Antoni,0.80,0.80
"Tạm biệt",Rachel,0.75,0.75
```

### 2. Chế Độ Xử Lý

#### Mode 1: Cùng Giọng
```
1. Nhập tất cả văn bản
2. Chọn 1 giọng chung
3. Nhấp "Generate All"
→ Tất cả dùng Rachel
```

#### Mode 2: Giọng Riêng
```
1. Nhập từng dòng
2. Chọn giọng cho từng dòng
3. Nhấp "Generate All"
→ Dòng 1 = Rachel, Dòng 2 = Antoni...
```

#### Mode 3: Import CSV
```
1. Chuẩn bị CSV (text, voice, settings)
2. Nhấp "Import from File"
3. Xem preview
4. Nhấp "Generate All"
```

### 3. Tối Ưu Parallel Processing

#### Điều Chỉnh Concurrency
- **2 songs**: Free plan
- **5 songs**: Trial plan
- **10+ songs**: Pro plan

Mỗi song parallel = 1 request riêng

#### Strategy Chọn Key
```
Khi có 3 API keys:
Key 1: 800k/1M dung lượng
Key 2: 900k/1M dung lượng
Key 3: 100k/100k dung lượng (sắp hết)

Setting: "Least-Used"
→ Request sẽ dùng Key 1 & 2 trước
```

### 4. Xuất Kết Quả

#### Download Options
- **Download All**: ZIP chứa tất cả MP3
- **Download Selected**: Chọn file cụ thể
- **Select & Re-generate**: Chỉ tạo lại những cái lỗi

#### Tổ Chức File
```
Audiobook_Chapter_01/
├─ sentence_001.mp3 (Rachel)
├─ sentence_002.mp3 (Rachel)
├─ sentence_003.mp3 (Rachel)
...
└─ manifest.json (metadata)
```

---

## Speech-to-Text: Phiên Âm Audio

### 1. Chuẩn Bị File Audio

#### Format Hỗ Trợ
✅ MP3, WAV, M4A, FLAC, OPUS, ULAW

#### Yêu Cầu Chất Lượng
- **Bitrate**: 128 kbps trở lên (lý tưởng: 192+)
- **Sample Rate**: 16000 Hz trở lên
- **Duration**: Không giới hạn (nhưng <100MB)
- **Ngôn Ngữ**: Tiếng Việt, Tiếng Anh, v.v.

#### Chuẩn Bị
✅ Audio rõ, ít noise
✅ Volume cân bằng
✅ Không bị cắt đầu/cuối
❌ Tránh nhạc nền quá to

### 2. Upload & Transcribe

#### Các Bước
1. **Mở Tab STT**
2. **Upload File**: Drag-drop hoặc Browse
3. **Chọn Ngôn Ngữ**:
   - Auto-detect (nếu không chắc)
   - Manual (tìm "Vietnamese", "English", v.v.)
4. **Tùy Chọn**:
   - Bật **Diarization**: Phân biệt nhà phát biểu
   - **Timestamps**: Cho SRT subtitle
   - **Keywords**: Từ đặc biệt cần highlight
5. **Nhấp Transcribe**
6. **Chờ Hoàn Thành**

#### Thời Gian Xử Lý
- 1 phút audio ≈ 1 phút xử lý
- 1 tiếng audio ≈ 1 tiếng xử lý (rough estimate)
- Phụ thuộc chất lượng audio & server load

### 3. Kết Quả Output

#### Format Output

**Text Format** (Simple)
```
Xin chào, đây là podcast của tôi.
Hôm nay chúng ta sẽ nói về AI.
AI là công nghệ tương lai.
```

**SRT Format** (Subtitle)
```
1
00:00:00,000 --> 00:00:02,500
Xin chào, đây là podcast của tôi.

2
00:00:02,500 --> 00:00:05,000
Hôm nay chúng ta sẽ nói về AI.
```

**VTT Format** (Web Video)
```
WEBVTT

00:00:00.000 --> 00:00:02.500
Xin chào, đây là podcast của tôi.
```

**JSON Format** (Detailed)
```json
{
  "text": "Xin chào, đây là podcast của tôi.",
  "segments": [
    {"start": 0, "end": 2.5, "text": "Xin chào", "speaker": "Speaker 1"}
  ]
}
```

### 4. Diarization (Phân Biệt Nhà Phát Biểu)

#### Khi Bật
```
[00:00:00] Speaker 1: "Xin chào bạn"
[00:00:03] Speaker 2: "Chào, tôi là Bình"
[00:00:06] Speaker 1: "Vui lắm được gặp bạn"
```

#### Khi Tắt
```
[00:00:00] Xin chào bạn
[00:00:03] Chào, tôi là Bình
[00:00:06] Vui lắm được gặp bạn
```

**Khuyên Cáo**: Bật cho Podcast, Cuộc Họp; Tắt cho Audiobook

### 5. Pro Tips STT

#### Cải Thiện Độ Chính Xác
```
1. Upload file rõ (< 8dB noise)
2. Chọn ngôn ngữ đúng
3. Thêm keywords nếu có từ đặc biệt
4. Split file dài (>1 tiếng) → riêng lẻ
```

#### Xử Lý Kết Quả
```
1. Download SRT
2. Dùng subtitle editor (Subtitle Edit, Aegisub)
3. Fix timing & typos
4. Export lại
```

---

## Sound Effects & Music: Creative Tools

### Sound Effects Workflow
1. Mô tả: "tiếng gió mạnh hú"
2. Duration: 8-12 giây
3. Loop: On/Off (game = On)
4. Generate → Preview → Download

### Music Generation Workflow
1. Chọn: Instrumental hoặc Song
2. Describe: "upbeat happy electronic"
3. Length: 30-120 giây
4. (Song) Lyrics: Nhập lời
5. Compose → Download

---

## Quản Lý API Keys: Advanced

### Bulk Import CSV
**File Format**:
```csv
key,label,charLimit
sk_xxx,Project A,1000000
sk_yyy,Project B,500000
```

### Monitoring Dashboard
- **Usage**: X% of limit per key
- **Health**: Green = OK, Orange = Warning, Red = Error
- **Rotation**: Auto-select next key when current exhausted
- **Reset Date**: When monthly quota resets

---

*Lần cập nhật cuối: 2026-03-08*

[← Quay Lại](./INTRODUCTION.md)

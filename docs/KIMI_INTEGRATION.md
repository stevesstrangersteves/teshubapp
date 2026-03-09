# Tích Hợp Kimi AI (Kimi AI Integration)

Hướng dẫn chi tiết cách tích hợp Kimi AI để tối ưu văn bản trước TTS.

## Kimi AI Là Gì?

Kimi AI là mô hình ngôn ngữ AI mạnh mẽ từ Moonshot AI, hỗ trợ:
- **Tối Ưu Văn Bản**: Sửa lỗi, cải thiện dòng chảy
- **Dịch Thuật**: Dịch chuyên sâu giữa các ngôn ngữ
- **Tóm Tắt**: Nén nội dung dài thành ngắn gọn
- **Viết Lại**: Điều chỉnh tone, style, phương thức

## Lợi Ích Sử Dụng Kimi

### Cải Thiện Chất Lượng TTS
```
Trước: "tôi đến từ việt nam, tôi làm kỹ sư"
Kimi:  "Tôi đến từ Việt Nam. Tôi là một kỹ sư phần mềm."
       (TTS sẽ phát âm tự nhiên hơn)
```

### Xử Lý Lỗi Phát Âm
```
Trước: "pd là gì? gọi là phicb hoặc plhcb"
Kimi:  "PD là gì? Gọi là P-D hoặc P-L-H-C-B."
       (TTS sẽ phát âm chính xác từng chữ)
```

### Tối Ưu Từ Khóa
```
Input: "seo, sem, ppc là các từ viết tắt của digital marketing"
Kimi:  "S-E-O, S-E-M, P-P-C là các từ viết tắt của digital marketing."
       (Dễ phát âm + dễ hiểu)
```

## Bước 1: Tạo Tài Khoản Kimi

1. Truy cập [kimi.moonshot.cn](https://kimi.moonshot.cn)
2. **Sign Up** với Email/WeChat/Alipay
3. Xác Minh Email (kiểm tra inbox)
4. Hoàn Tất Onboarding

## Bước 2: Lấy Kimi API Key

### Cách Lấy
1. Đăng Nhập [kimi.moonshot.cn](https://kimi.moonshot.cn)
2. Vào **Profile** → **API Keys** (hoặc setting)
3. Nhấp **Create New API Key**
4. Nhập Tên (ví dụ: "TeSHub Desktop")
5. Nhấp **Generate**
6. Copy Key (format: `sk_...`)

### Lưu Ý
⚠️ **Giữ bí mật API key!** Không chia sẻ hoặc commit vào git.

## Bước 3: Cấu Hình Trong TeSHub

### Thêm Kimi Key
1. **Mở TeSHub Desktop**
2. **Vào Settings** (⚙️ icon)
3. **Tìm "Kimi AI"** section
4. **Dán Kimi API Key**
5. **Nhấp "Save"** hoặc **"Test Connection"**

### Xác Nhận Thành Công
```
✅ "Kimi connected successfully"
✅ Có thể sử dụng tính năng Kimi
```

## Sử Dụng Kimi Features

### Feature 1: Optimize Text

Tối ưu văn bản trước TTS - sửa lỗi, cải thiện dòng chảy.

#### Cách Sử Dụng
```
1. Nhập văn bản vào TTS input
2. Nhấp "Optimize with Kimi" (button)
3. Chờ Kimi xử lý (10-30 giây)
4. Xem kết quả optimized
5. Nhấp "Use Optimized Text" nếu OK
6. Generate TTS bình thường
```

#### Ví Dụ Tối Ưu
```
Input:
"tôi tên là nguyễn văn a, tôi đến từ việt nam, tôi làm kỹ sư phần mềm"

Output:
"Tôi tên là Nguyễn Văn A, đến từ Việt Nam. Tôi là một kỹ sư phần mềm."

Cải Thiện:
✓ Viết hoa tên riêng
✓ Thêm dấu câu chính xác
✓ Tự nhiên hơn
✓ TTS sẽ phát âm tốt hơn
```

### Feature 2: Translate

Dịch text sang/từ các ngôn ngữ khác.

#### Cách Sử Dụng
```
1. Nhập text cần dịch
2. Nhấp "Translate"
3. Chọn Ngôn Ngữ Đích (Target Language):
   - Tiếng Việt
   - Tiếng Anh
   - Tiếng Trung
   - Tiếng Nhật
   - v.v.
4. Chờ dịch hoàn thành
5. Review kết quả
6. Sử dụng hoặc chỉnh sửa
```

#### Ví Dụ Dịch
```
Input (English):
"I am a software engineer from Vietnam working at Google."

Output (Vietnamese):
"Tôi là một kỹ sư phần mềm từ Việt Nam, làm việc tại Google."

Chất Lượng Dịch:
✓ Chính xác
✓ Tự nhiên
✓ Sử dụng được cho TTS
```

### Feature 3: Summarize

Nén nội dung dài thành ngắn gọn.

#### Cách Sử Dụng
```
1. Dán text dài cần tóm tắt
2. Nhấp "Summarize"
3. Chọn Tỷ Lệ Nén:
   - Short (30% độ dài gốc)
   - Medium (50%)
   - Long (70%)
4. Chờ tóm tắt
5. Review & sử dụng
```

#### Ví Dụ Tóm Tắt
```
Input (1000 ký tự):
[Article dài về AI...]

Output (Medium - 500 ký tự):
"AI là công nghệ được ứng dụng rộng rãi.
Các ứng dụng chính bao gồm: TTS, STT, NLP, Computer Vision.
AI giúp tối ưu quy trình làm việc và tăng năng suất."

Tỷ Lệ: 50% độ dài gốc, vẫn giữ nguyên ý chính
```

### Feature 4: Rephrase / Rewrite

Viết lại text theo style khác hoặc tone khác.

#### Cách Sử Dụng
```
1. Nhập text cần viết lại
2. Nhấp "Rephrase"
3. Chọn Style/Tone:
   - Professional / Casual
   - Formal / Informal
   - Enthusiastic / Neutral
   - Short / Detailed
4. Chờ xử lý
5. So sánh & lựa chọn
```

#### Ví Dụ Viết Lại
```
Original (Formal):
"I kindly request your attendance at the meeting scheduled for..."

Casual Rephrase:
"Hey, we've got a meeting coming up..."

Việt Nam (Professional → Casual):
"Chúng tôi xin kính mời quý vị tham dự buổi họp..."
→ "Chúng ta có buổi họp sắp tới..."
```

## Tối Ưu Workflow: TTS + Kimi

### Workflow Tối Ưu (Recommended)
```
1. Chuẩn bị text thô
   ↓
2. Nhấp "Optimize with Kimi"
   (Kimi sửa lỗi, cải thiện)
   ↓
3. Review text optimized
   (Đảm bảo ý nghĩa không thay đổi)
   ↓
4. Generate TTS
   (Với text đã tối ưu)
   ↓
5. Download audio
   (Chất lượng TTS tốt hơn)
```

### Ví Dụ Full Workflow
```
Text Gốc:
"mình tên là A, mình từ HCMC, mình là fullstack developer,
mình thích làm những project cool, kỹ năng: js, python, react"

Kimi Optimize:
"Tôi tên là A, đến từ Thành Phố Hồ Chí Minh.
Tôi là một Fullstack Developer yêu thích các dự án thú vị.
Kỹ năng của tôi bao gồm: JavaScript, Python, React."

TTS (Rachel):
♪ [Natural, professional audio output]
```

## Advanced: Kimi Trong Parallel TTS

Khi xử lý hàng loạt text:

```
CSV Input:
text,voice
"tôi là dev",Rachel
"tôi code game",Rachel

Nhấp "Optimize All with Kimi"
↓
Tất cả text được tối ưu cùng lúc

Kết Quả:
"Tôi là developer",Rachel
"Tôi lập trình game",Rachel

Sau đó: Generate All
```

## Cấu Hình Kimi Nâng Cao

### Chọn Model Kimi

1. **Vào Settings** → **Kimi Settings**
2. **Chọn Model**:
   - `kimi-moonshot`: Model chính (khuyên cáo)
   - `kimi-moonshot-turbo`: Nhanh hơn, kém chính xác
   - v.v.
3. **Save**

### Điều Chỉnh Tham Số

1. **Temperature** (0.0 - 2.0):
   - Thấp (0.0): Kết quả xác định, nhất quán
   - Cao (2.0): Kết quả sáng tạo, đa dạng
   - Khuyên: 0.7 (cân bằng)

2. **Max Tokens**:
   - Giới hạn độ dài output
   - Khuyên: 2000 (đủ cho hầu hết text)

### Optimize Prompt

Tùy chỉnh instruction cho Kimi:

```
Default: "Optimize this Vietnamese text for TTS quality"

Custom: "Optimize for clear pronunciation.
Replace abbreviations with full names.
Keep professional tone."
```

## Giới Hạn & Quota

### Free Plan Kimi
- Requests/tháng: Giới hạn (ví dụ: 50,000)
- Token/tháng: Giới hạn (ví dụ: 1,000,000)

### Paid Plan Kimi
- Starter: ~100k requests/tháng
- Professional: ~500k requests/tháng
- Unlim: Custom pricing

### Kiểm Tra Quota
1. Vào [kimi.moonshot.cn](https://kimi.moonshot.cn)
2. **Usage** hoặc **Billing**
3. Xem token usage

## Lỗi & Giải Pháp

### Lỗi: "Invalid Kimi Key"
```
Nguyên nhân:
- Key sai hoặc incomplete
- Key đã bị xóa

Giải pháp:
1. Copy key lại từ kimi.moonshot.cn
2. Dán vào TeSHub
3. Test connection
```

### Lỗi: "Kimi Request Failed"
```
Nguyên nhân:
- Network error
- Kimi API down

Giải pháp:
1. Kiểm tra internet
2. Thử lại sau vài giây
3. Kiểm tra Kimi status
```

### Lỗi: "Quota Exceeded"
```
Nguyên nhân:
- Dùng hết token/tháng

Giải pháp:
1. Chờ tháng mới reset
2. Nâng cấp plan Kimi
3. Tạm dừng dùng Kimi
```

## Bật/Tắt Kimi

Nếu muốn tạm dừng sử dụng Kimi:

1. **Settings** → **Kimi AI**
2. **Tắt "Enable Kimi"** (toggle off)
3. **Save**

Khi tắt: Tất cả nút Kimi sẽ ẩn, không thể dùng.

---

## Mẹo & Thủ Thuật

### Tip 1: Optimize Trước Khi Generate
Luôn optimize text trước TTS → Chất lượng audio tốt hơn.

### Tip 2: Batch Optimize
Dùng Parallel TTS + "Optimize All" → Xử lý nhanh.

### Tip 3: Custom Prompts
Viết prompt rõ ràng cho kết quả tốt:
```
❌ "optimize"
✅ "Optimize for clear TTS pronunciation.
    Use full names instead of abbreviations.
    Ensure proper punctuation."
```

### Tip 4: Review Kết Quả
Luôn review Kimi output trước dùng:
```
✓ Ý nghĩa không thay đổi
✓ Tự nhiên, không lạ
✓ Phù hợp tone/context
```

---

## Câu Hỏi Thường Gặp

**Q: Kimi có sửa lỗi chính tả không?**
A: Có, Kimi sẽ sửa lỗi chính tả, viết hoa, dấu câu.

**Q: Kimi có thay đổi ý nghĩa không?**
A: Không, chỉ cải thiện cách diễn đạt, giữ nguyên ý chính.

**Q: Bao lâu Kimi xử lý?**
A: Thường 10-30 giây tùy độ dài text.

**Q: Có thể dùng Kimi offline không?**
A: Không, cần kết nối internet.

---

*Lần cập nhật cuối: 2026-03-08*

[← Quay Lại](./INTRODUCTION.md)

# Cấu Hình API (API Configuration)

Hướng dẫn chi tiết để cấu hình ElevenLabs API key và tích hợp các dịch vụ khác.

## ElevenLabs API Key

### Bước 1: Tạo Tài Khoản ElevenLabs

1. Truy cập [elevenlabs.io](https://elevenlabs.io)
2. Nhấp **Sign Up** (nếu chưa có tài khoản)
3. Nhập Email & Mật Khẩu
4. Xác Minh Email (kiểm tra inbox)
5. Hoàn Tất Onboarding

### Bước 2: Lấy API Key

#### Cách Lấy API Key
1. Đăng Nhập vào [elevenlabs.io](https://elevenlabs.io)
2. Nhấp **Profile** → **Account** (hoặc gear icon)
3. Tìm **"API Key"** section
4. Nhấp **Copy** hoặc **Generate New Key**
5. Key sẽ có format: `sk_xxxxxxxxxxxxxxxx...`

#### Lưu Ý Bảo Mật
⚠️ **Không chia sẻ API key của bạn!**
- Giữ bí mật như mật khẩu
- Không commit vào git/GitHub
- Nếu leak: Xóa key cũ & tạo key mới ngay lập tức

### Bước 3: Cấu Hình Trong TeSHub

1. **Mở TeSHub Desktop**
2. **Vào Settings** (⚙️ icon)
3. **Tìm Section: "ElevenLabs API"**
4. **Dán API Key** vào ô "API Key" input
5. **Nhấp "Save"** hoặc **"Test Connection"**

#### Xác Nhận Thành Công
```
✅ "Connected successfully"
✅ Quota hiển thị (ví dụ: 250,000 characters left)
```

#### Nếu Lỗi
```
❌ "Invalid API Key" → Kiểm tra lại key
❌ "Connection failed" → Kiểm tra internet
❌ "Expired key" → Tạo key mới
```

### Bước 4: Kiểm Tra Quota

#### Cách 1: Trong TeSHub
- **Settings** → Xem "Remaining Characters"

#### Cách 2: Trên ElevenLabs Dashboard
1. Đăng nhập [elevenlabs.io](https://elevenlabs.io)
2. Vào **Usage** hoặc **Billing**
3. Xem "Characters Used" & "Characters Remaining"

#### Quota Tính Toán
```
1,000 ký tự text → 1,000 characters usage
Mỗi TTS request = số ký tự * 1

Ví dụ:
"Xin chào" (8 ký tự) = 8 characters
"Đây là bài hát..." (20 ký tự) = 20 characters
```

### Bước 5: Chọn Plan Phù Hợp

| Plan | Monthly Characters | Cost | Lý Tưởng Cho |
|------|-------------------|------|------------|
| **Free** | ~10,000 | Free | Testing, nhỏ |
| **Starter** | 100,000 | $5 | Personal projects |
| **Professional** | 1,000,000+ | $99 | Content creators |
| **Scale** | Custom | Custom | Enterprise |

**Nâng Cấp Plan**:
1. Vào [elevenlabs.io/pricing](https://elevenlabs.io/pricing)
2. Chọn Plan
3. Thanh Toán (Credit Card, Stripe, v.v.)
4. Key sẽ tự động cập nhật quota

---

## Quản Lý Multiple API Keys

### Khi Nào Dùng Multiple Keys?
✅ Nhiều dự án cần chia quota
✅ Team hoạt động song song
✅ Backup key khi key chính gần hết
✅ A/B testing khác nhau

### Setup Multiple Keys

#### Bước 1: Tạo Key Thứ Hai (Trên ElevenLabs)
1. Vào **Profile** → **API Key**
2. Nhấp **Generate New Key**
3. Copy key mới

#### Bước 2: Thêm vào TeSHub

**Cách 1: Thêm Từng Cái Một**
1. Vào **Key Manager** tab
2. Nhấp **Add Key**
3. Dán Key #2
4. Nhập Label: "Backup Key" hoặc "Project B"
5. Nhấp **Add**
6. Lặp lại cho Key #3, #4, v.v.

**Cách 2: Import Hàng Loạt (CSV)**
1. Chuẩn Bị File CSV:
   ```csv
   key,label,charLimit
   sk_aaa...,Project A,1000000
   sk_bbb...,Project B,500000
   sk_ccc...,Backup,100000
   ```
2. Vào **Key Manager** → **Import from File**
3. Chọn file CSV
4. Xem Preview
5. Nhấp **Import**

#### Bước 3: Chọn Selection Strategy

1. Vào **Settings**
2. Tìm **"Key Selection Strategy"**
3. Chọn:
   - **Least-Used**: Dùng key có dung lượng dư nhiều nhất
   - **Round-Robin**: Quay vòng lần lượt (Key 1 → Key 2 → Key 3 → Key 1...)
   - **Random**: Chọn ngẫu nhiên

#### Ví Dụ Cấu Hình
```
Scenario: 3 Keys, Least-Used Strategy

Initial State:
├─ Key 1: 800k/1M (80% used)
├─ Key 2: 900k/1M (90% used)
└─ Key 3: 50k/100k (50% used)

Next TTS Request → Uses Key 3 (least used)
After Request:
├─ Key 1: 800k/1M
├─ Key 2: 900k/1M
└─ Key 3: 50.5k/100k → Still Key 3 for next request
```

### Giám Sát Sử Dụng Keys

#### Trong TeSHub
1. **Key Manager** tab
2. **Chọn Key** để xem:
   - Usage: X / Y characters
   - Request Count
   - Last Used
   - Error Count
   - Health Status (✅ / ⚠️ / ❌)

#### Xử Lý Key Gần Hết
```
Khi Key 1 sắp hết (95% used):
✅ Tự động chuyển sang Key 2 (nếu cấu hình Least-Used)
✅ Nhận cảnh báo: "Key 1 approaching limit"
✅ Tùy chọn: Disable Key 1 (không dùng)
```

#### Xử Lý Key Lỗi
```
Nếu Key bị lỗi:
❌ Status: "ERROR"
❌ Red indicator
✅ Tự động skip (dùng key khác)
✅ Disable hoặc xóa Key
```

---

## Kimi AI Integration (Tùy Chọn)

### Tại Sao Cần Kimi AI?

Kimi AI giúp:
- ✅ Tối ưu văn bản trước TTS (cải thiện chất lượng)
- ✅ Dịch tự động (nếu cần tiếng Anh)
- ✅ Tóm tắt văn bản dài
- ✅ Sửa lỗi ngữ pháp

### Cách Lấy Kimi API Key

1. Truy cập [kimi.moonshot.cn](https://kimi.moonshot.cn)
2. Đăng ký hoặc Đăng Nhập
3. Vào **API** → **API Keys**
4. Nhấp **Create New Key**
5. Copy key (format: `sk_...`)

### Cấu Hình Kimi Trong TeSHub

1. **Vào Settings**
2. **Tìm "Kimi AI Integration"**
3. **Dán Kimi API Key**
4. **Nhấp "Save"** hoặc **"Test Connection"**

#### Xác Nhận Thành Công
```
✅ "Kimi connected"
✅ Có thể dùng tính năng Kimi
```

### Sử Dụng Kimi Features

#### Tối Ưu Văn Bản
```
Input: "tôi tên là abcd đến từ việt nam, tôi là kỹ sư phần mềm, tôi làm việc tại google"

Nhấp "Optimize with Kimi"

Output: "Tôi tên là ABCD, đến từ Việt Nam. Tôi là kỹ sư phần mềm, làm việc tại Google."
(Tự động sửa: viết hoa, dấu câu, cách, v.v.)
```

#### Dịch Text
```
Input: "I am a software engineer from Vietnam"

Nhấp "Translate to Vietnamese"

Output: "Tôi là một kỹ sư phần mềm từ Việt Nam"
```

#### Tóm Tắt Văn Bản Dài
```
Input: [1000 ký tự dài]

Nhấp "Summarize"

Output: [200 ký tự tóm tắt ngắn gọn]
```

Xem chi tiết: [Kimi AI Integration](./KIMI_INTEGRATION.md)

---

## Advanced: Proxy & Network

### Sử Dụng Proxy

Nếu kết nối trực tiếp bị chặn:

1. **Vào Settings**
2. **Network** → **Proxy**
3. **Chọn Loại Proxy**:
   - HTTP
   - HTTPS
   - SOCKS5
4. **Nhập Proxy URL**: ví dụ: `http://proxy.company.com:8080`
5. **(Tùy)** Nhập Username & Password
6. **Save & Test**

### Kiểm Tra Kết Nối

1. **Vào Settings**
2. **Nhấp "Test Connection"**
3. Chờ kết quả:
   - ✅ Connected
   - ❌ Failed → Kiểm tra proxy hoặc internet

---

## Bảo Mật API Key

### Best Practices

✅ **Làm Gì:**
- Giữ key bí mật (như mật khẩu)
- Xóa key cũ khi không dùng
- Tạo key mới nếu key bị lộ
- Dùng environment variables (.env file)
- Backup dữ liệu định kỳ

❌ **Không Làm Gì:**
- Chia sẻ key qua email, chat, Telegram
- Commit key vào git repository
- Sử dụng key của người khác
- Share file cấu hình chứa key

### Nếu Key Bị Lộ

**Hành Động Ngay Lập Tức:**
1. Vào ElevenLabs dashboard
2. Xóa key bị lộ
3. Tạo key mới
4. Cập nhật key mới trong TeSHub
5. Kiểm tra usage có bất thường không

---

## Troubleshooting

### Lỗi: "Invalid API Key"
```
Nguyên nhân:
- Key sai hoặc thiếu ký tự
- Key đã bị xóa
- Key từ account khác

Giải pháp:
1. Kiểm tra key trên elevenlabs.io
2. Copy key lại (từng ký tự)
3. Test connection
```

### Lỗi: "Quota Exceeded"
```
Nguyên nhân:
- Dung lượng tháng này hết
- Sử dụng quá mức

Giải pháp:
1. Chờ tháng mới reset
2. Nâng cấp plan
3. Dùng key khác (nếu có)
4. Kiểm tra quota: elevenlabs.io/app/usage
```

### Lỗi: "Rate Limited"
```
Nguyên nhân:
- Quá nhiều request trong thời gian ngắn
- API server busy

Giải pháp:
1. Chờ vài phút
2. Thử lại
3. Giảm concurrent requests
4. Dùng key khác
```

### Connection Error
```
Nguyên nhân:
- Mất kết nối internet
- Firewall chặn
- Proxy không hoạt động

Giải pháp:
1. Kiểm tra internet (ping 8.8.8.8)
2. Kiểm tra firewall settings
3. Thử proxy khác
4. Restart ứng dụng
```

---

## Tóm Tắt Cấu Hình

| Bước | Hành Động | Kết Quả |
|------|-----------|---------|
| 1 | Lấy API Key từ ElevenLabs | `sk_...` |
| 2 | Dán vào TeSHub Settings | ✅ Connected |
| 3 | Test Connection | ✅ Quota visible |
| 4 | (Tùy) Thêm Key thứ 2+ | Multiple keys setup |
| 5 | (Tùy) Setup Kimi AI | Text optimization |
| 6 | (Tùy) Configure Proxy | Network access |
| 7 | Bắt đầu sử dụng! | 🎉 Ready to go |

---

*Lần cập nhật cuối: 2026-03-08*

[← Quay Lại](./INTRODUCTION.md)

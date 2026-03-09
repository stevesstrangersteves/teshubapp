# Hướng Dẫn Thiết Lập Affiliate Shopee

**Tính năng:** Tự động mở link affiliate Shopee sau mỗi 3 lần generate thành công trong app TeSHub Desktop

**Ngày tạo:** 2026-03-05

---

## Tổng Quan

Tính năng affiliate tự động mở link Shopee trong trình duyệt sau mỗi 3 lần người dùng generate thành công (TTS, SFX, Music, hoặc Parallel TTS).

### Đặc Điểm Chính
- **Bắt buộc**: Người dùng không thể tắt hoặc cấu hình
- **Trigger đơn giản**: Mỗi 3 lần generate
- **Tự động mở**: Mở link Shopee trong trình duyệt mặc định
- **Lấy link từ server**: Fetch danh sách link từ server của bạn
- **Chọn ngẫu nhiên**: Random 1 link từ danh sách trả về
- **Lưu trữ bền vững**: Counter tồn tại sau khi restart app
- **Cache link**: Link được cache trong 24 giờ, có fallback khi lỗi

---

## Cách Hoạt Động

```
Lần generate thứ 1 → count = 1 (lưu persistent)
Lần generate thứ 2 → count = 2 (lưu persistent)
Lần generate thứ 3 → count = 3 → Fetch links (hoặc dùng cache) → Chọn random → Mở browser → count reset về 0
```

**Lưu trữ:**
- Counter lưu trong `{userData}/affiliate-state.json`
- Link cache trong 24 giờ
- Fallback về cached links nếu server không phản hồi

---

## Thiết Lập Server

### 1. API Endpoint

Tạo endpoint GET để trả về danh sách link affiliate:

**Endpoint:** `GET /api/affiliate/links`

**Response Format:**

```json
{
  "links": [
    "https://shopee.vn/product1?affiliate=xxx",
    "https://shopee.vn/product2?affiliate=yyy",
    "https://shopee.vn/shop?affiliate=zzz"
  ],
  "expiresAt": 1709654400000
}
```

**Headers yêu cầu:**
- `Content-Type: application/json`
- `User-Agent: ElevenLabs-Desktop-App`

**Timeout:** 10 giây

### 2. Fallback Behavior

| Tình huống | Hành vi |
|-----------|---------|
| Server không phản hồi | Dùng cached links |
| Không có cached links | Dùng default: `https://shopee.vn/?affiliate_source=elevenlabs_app` |
| Server trả về empty links | Dùng default link |

---

## Mẫu Server Implementation

### Node.js + Express

```javascript
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors());

// Danh sách link affiliate của bạn
const affiliateLinks = [
  'https://shopee.vn/product1?affiliate=YOUR_CODE_1',
  'https://shopee.vn/product2?affiliate=YOUR_CODE_2',
  'https://shopee.vn/shop?affiliate=YOUR_CODE_3',
];

app.get('/api/affiliate/links', (req, res) => {
  // Validate User-Agent nếu cần
  const userAgent = req.get('User-Agent');
  if (!userAgent || !userAgent.includes('ElevenLabs')) {
    return res.status(403).json({ error: 'Invalid client' });
  }

  res.json({
    links: affiliateLinks,
    expiresAt: Date.now() + (24 * 60 * 60 * 1000) // 24 hours from now
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Affiliate API server running on port ${PORT}`);
});
```

### Python + FastAPI

```python
from fastapi import FastAPI, Request, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from datetime import datetime, timedelta

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["GET"],
    allow_headers=["*"],
)

# Danh sách link affiliate của bạn
AFFILIATE_LINKS = [
    "https://shopee.vn/product1?affiliate=YOUR_CODE_1",
    "https://shopee.vn/product2?affiliate=YOUR_CODE_2",
    "https://shopee.vn/shop?affiliate=YOUR_CODE_3",
]

@app.get("/api/affiliate/links")
async def get_affiliate_links(request: Request):
    # Validate User-Agent nếu cần
    user_agent = request.headers.get("User-Agent", "")
    if "ElevenLabs" not in user_agent:
        raise HTTPException(status_code=403, detail="Invalid client")

    return {
        "links": AFFILIATE_LINKS,
        "expiresAt": int((datetime.now() + timedelta(hours=24)).timestamp() * 1000)
    }
```

### Go + Gin

```go
package main

import (
    "net/http"
    "strings"
    "time"

    "github.com/gin-gonic/gin"
)

var affiliateLinks = []string{
    "https://shopee.vn/product1?affiliate=YOUR_CODE_1",
    "https://shopee.vn/product2?affiliate=YOUR_CODE_2",
    "https://shopee.vn/shop?affiliate=YOUR_CODE_3",
}

func main() {
    r := gin.Default()

    r.GET("/api/affiliate/links", func(c *gin.Context) {
        // Validate User-Agent
        userAgent := c.GetHeader("User-Agent")
        if !strings.Contains(userAgent, "ElevenLabs") {
            c.JSON(http.StatusForbidden, gin.H{"error": "Invalid client"})
            return
        }

        c.JSON(http.StatusOK, gin.H{
            "links": affiliateLinks,
            "expiresAt": time.Now().Add(24 * time.Hour).UnixMilli(),
        })
    })

    r.Run(":3000")
}
```

---

## Cấu Hình Trong App

### Environment Variable

Đặt URL server qua environment variable:

```bash
export AFFILIATE_API_URL="https://your-server.com/api/affiliate/links"
```

**Default:** `https://your-server.com/api/affiliate/links`

### Build App với Environment Variable

```bash
# macOS/Linux
AFFILIATE_API_URL="https://your-server.com/api/affiliate/links" bun run build

# Windows (PowerShell)
$env:AFFILIATE_API_URL="https://your-server.com/api/affiliate/links"; bun run build
```

---

## Testing

### Test Feature

1. Start app: `bun run dev`
2. Generate audio 3 lần (bất kỳ feature nào: TTS, SFX, Music, Parallel TTS)
3. Ở lần generate thứ 3, verify browser mở link từ server của bạn
4. Restart app và verify counter vẫn tồn tại
5. Generate tiếp để verify counter reset đúng

### Test Server Integration

1. Setup server endpoint trả về test links
2. Set environment variable:
   ```bash
   export AFFILIATE_API_URL="http://localhost:3000/api/affiliate/links"
   ```
3. Xóa cached state:
   ```bash
   # macOS
   rm ~/Library/Application\ Support/ElevenLabs\ Desktop/affiliate-state.json

   # Windows
   del %APPDATA%\ElevenLabs\ Desktop\affiliate-state.json
   ```
4. Generate 3 lần và verify links được fetch từ server của bạn

### Test Offline Behavior

1. Generate 3 lần khi online (links được cache)
2. Ngắt kết nối mạng
3. Generate 3 lần nữa - vẫn mở cached links

---

## Debug & Troubleshooting

### Xem Logs

```bash
# Chạy với debug logs
DEBUG=electron* bun run dev
```

Logs affiliate sẽ hiển thị:
- Khi fetch links từ server
- Khi mở link trong browser
- Lỗi (nếu có)

### Reset Counter & Cache

```bash
# Xóa state file
rm ~/Library/Application\ Support/ElevenLabs\ Desktop/affiliate-state.json

# Hoặc trên Windows:
del %APPDATA%\ElevenLabs\ Desktop\affiliate-state.json
```

### Kiểm Tra Counter Hiện Tại

Mở DevTools trong app (Cmd+Option+I) và chạy:

```javascript
window.elevenlabs.affiliate.getCount().then(count => {
  console.log('Current affiliate count:', count);
});
```

### Force Refresh Links

```javascript
window.elevenlabs.affiliate.refreshLinks().then(success => {
  console.log('Links refreshed:', success);
});
```

---

## File Structure

### Backend (Electron)

```
electron/
├── affiliate-manager.ts      # AffiliateManager class
├── main.ts                   # IPC handlers
└── preload.ts                # Exposed API
```

### Frontend Integration

App đã tích hợp sẵn trong các files:

```
src/pages/
├── TextToSpeech.tsx
├── ParallelTTS.tsx
├── SoundEffects.tsx
└── Music.tsx
```

Mỗi file gọi `window.elevenlabs.affiliate.recordGeneration()` sau khi generate thành công.

---

## State File Format

File: `{userData}/affiliate-state.json`

```json
{
  "generationCount": 2,
  "lastOpened": "2026-03-05T10:30:00.000Z",
  "cachedLinks": [
    "https://shopee.vn/product1?affiliate=xxx",
    "https://shopee.vn/product2?affiliate=yyy"
  ],
  "linksFetchedAt": 1709640000000
}
```

**Fields:**
- `generationCount`: Số lần generate hiện tại (0-2)
- `lastOpened`: Thời gian mở link affiliate gần nhất
- `cachedLinks`: Danh sách link cache từ server
- `linksFetchedAt`: Timestamp khi fetch links gần nhất

---

## Production Checklist

- [ ] Setup server endpoint với danh sách link affiliate thật
- [ ] Configure HTTPS cho server (required cho production)
- [ ] Set environment variable `AFFILIATE_API_URL` khi build
- [ ] Test với link Shopee affiliate thật
- [ ] Verify counter persist sau app restart
- [ ] Test fallback behavior khi server down
- [ ] Monitor logs để đảm bảo không có lỗi

---

## FAQ

**Q: Link có mở khi app offline không?**
A: Có, nếu đã có cached links từ trước. Nếu không có cache, sẽ dùng default link.

**Q: Counter có reset khi restart app không?**
A: Không, counter được lưu persistent trong file JSON.

**Q: Làm sao để tắt tính năng này?**
A: Tính năng là bắt buộc, không có option tắt.

**Q: Link nào được dùng nếu server trả về empty?**
A: Default link: `https://shopee.vn/?affiliate_source=elevenlabs_app`

**Q: Cache expire sau bao lâu?**
A: 24 giờ kể từ thời điểm fetch.

---

## Contact

Nếu có vấn đề hoặc cần hỗ trợ, kiểm tra logs và report với thông tin:
- OS version
- App version
- Logs từ `DEBUG=electron* bun run dev`
- State file content (sau khi xóa thông tin nhạy cảm)

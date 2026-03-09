# Quick Start: Affiliate Shopee Server

Hướng dẫn nhanh để thiết lập server affiliate cho app TeSHub Desktop.

---

## 1. Tạo Server Đơn Giản (Node.js)

```bash
# Tạo project mới
mkdir elevenlabs-affiliate-server
cd elevenlabs-affiliate-server
npm init -y
npm install express cors
```

Tạo file `server.js`:

```javascript
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors());

// Thay bằng link affiliate thật của bạn
const affiliateLinks = [
  'https://shopee.vn/?affiliate_code=YOUR_CODE_1',
  'https://shopee.vn/?affiliate_code=YOUR_CODE_2',
  'https://shopee.vn/?affiliate_code=YOUR_CODE_3',
];

app.get('/api/affiliate/links', (req, res) => {
  res.json({
    links: affiliateLinks,
    expiresAt: Date.now() + (24 * 60 * 60 * 1000)
  });
});

app.listen(3000, () => {
  console.log('Server running at http://localhost:3000/api/affiliate/links');
});
```

Chạy server:

```bash
node server.js
```

---

## 2. Cấu Hình App

```bash
# Set environment variable
export AFFILIATE_API_URL="http://localhost:3000/api/affiliate/links"

# Chạy app
bun run dev
```

---

## 3. Test

1. Mở app
2. Generate audio 3 lần
3. Browser sẽ tự động mở link Shopee affiliate

---

## 4. Deploy Server (Tùy Chọn)

### Deploy lên Vercel

Tạo `vercel.json`:

```json
{
  "version": 2,
  "builds": [{ "src": "server.js", "use": "@vercel/node" }],
  "routes": [{ "src": "/(.*)", "dest": "server.js" }]
}
```

Deploy:

```bash
npm i -g vercel
vercel --prod
```

### Deploy lên Render/Railway

1. Push code lên GitHub
2. Connect repo lên Render/Railway
3. Deploy tự động

---

## 5. Production Config

Sau khi deploy, cập nhật:

```bash
export AFFILIATE_API_URL="https://your-app.vercel.app/api/affiliate/links"
```

Build app:

```bash
AFFILIATE_API_URL="https://your-app.vercel.app/api/affiliate/links" bun run build
```

---

## Response Format

```json
{
  "links": [
    "https://shopee.vn/link1?affiliate=xxx",
    "https://shopee.vn/link2?affiliate=yyy"
  ],
  "expiresAt": 1709654400000
}
```

**Lưu ý:**
- `links`: Bắt buộc, phải là mảng không rỗng
- `expiresAt`: Tùy chọn, timestamp khi links expire

---

## Troubleshooting

| Vấn đề | Giải pháp |
|--------|-----------|
| Browser không mở link | Kiểm tra logs với `DEBUG=electron*` |
| Link không đúng | Verify server response đúng format |
| Server 404 | Kiểm tra endpoint path |
| CORS error | Thêm `cors()` middleware |

---

## Tài Liệu Đầy Đủ

Xem `docs/shopee-affiliate-setup.md` để biết chi tiết hơn.

# TeSHub Desktop - Tài liệu tiếng Việt

Bộ tài liệu này mô tả cách dùng app theo đúng tính năng hiện có trong mã nguồn hiện tại.

## Tài liệu chính

- [Hướng dẫn sử dụng chi tiết](./huong-dan-su-dung-vi.md)
- [Hướng dẫn nhanh](./huong-dan-nhanh-vi.md)

## Tính năng đang có

- Text To Speech
- Parallel TTS
- Qwen3 TTS (local)
- Speech To Text
- Sound Effects
- Music generation
- Quản lý ElevenLabs API key
- Quản lý Kimi token
- Settings: Proxy, Tunnel, Auto-save, License
- Donate page

## Chạy dự án

```bash
npm install
npm run dev
```

Build production:

```bash
npm run build
```

Đóng gói Electron:

```bash
npm run electron:build
```

## Ghi chú

- App hỗ trợ giao diện song ngữ EN/VI trực tiếp trong header.
- Một số tính năng có thể bị giới hạn theo license (đặc biệt phần Kimi/Pro).

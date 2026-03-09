# Hướng dẫn nhanh (Tiếng Việt)

## 1. Luồng dùng nhanh

1. Vào **Khóa API** -> thêm key `sk_...` hoặc `xi_...`.
2. Vào tính năng cần dùng (`TTS`, `STT`, `SFX`, `Music`, `Parallel TTS`, `Qwen3 TTS`).
3. Nhập nội dung -> chỉnh thông số -> bấm `Generate`.
4. Nghe lại trong `Audio Player` và tải file.

## 2. Công thức theo từng tính năng

- `TTS`: nhập text -> chọn voice/model -> Generate.
- `Parallel TTS`: nhập nhiều câu -> (tuỳ chọn) custom từng câu -> Generate All.
- `Qwen3 TTS`: setup môi trường -> tải model -> nhập text -> Generate.
- `STT`: tải file audio/video -> (tuỳ chọn) language/key terms/diarize -> đợi transcript.
- `SFX`: nhập prompt hiệu ứng -> chỉnh duration/influence/loop -> Generate SFX.
- `Music`: nhập prompt nhạc -> chỉnh độ dài -> Generate Music.

## 3. Phím và thao tác nhanh

- `Enter` trong Parallel TTS: thêm dòng mới.
- `Backspace` trên dòng trống: xóa dòng.
- `EN/VI` ở header: đổi ngôn ngữ.
- Nút mặt trăng/mặt trời ở header: đổi theme.

## 4. Cài đặt quan trọng

- `Settings > Proxy`: cấu hình proxy + test kết nối.
- `Settings > Tunnel`: chạy local server + tạo public URL.
- `Settings > Auto-save`: bật tự lưu kết quả ra thư mục.
- `Settings > License`: kích hoạt/hủy license.

## 5. Khi gặp lỗi

- `Invalid API key`: kiểm tra và thêm lại key.
- `Quota exceeded`: đổi key hoặc chờ reset quota.
- Qwen3 không generate được: kiểm tra đã tải model chưa.
- Chạy chậm: giảm độ dài input hoặc kiểm tra proxy/mạng.

Chi tiết đầy đủ xem: `docs/huong-dan-su-dung-vi.md`.

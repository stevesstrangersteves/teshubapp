# Hướng dẫn sử dụng TeSHub Desktop (Tiếng Việt)

Tài liệu này mô tả cách dùng các tính năng đang có trong ứng dụng desktop tại thời điểm cập nhật.

## 1. Tổng quan giao diện

- Thanh bên trái: chuyển nhanh giữa các tính năng.
- Header phía trên: đổi ngôn ngữ `EN/VI` và đổi giao diện sáng/tối.
- Mỗi trang thường có:
- Vùng nhập nội dung ở giữa.
- Panel cài đặt ở bên phải.

## 2. Bắt đầu sử dụng

1. Mở tab **Khóa API**.
2. Bấm **Thêm Khóa**.
3. Nhập:
- `Label` (tên dễ nhớ).
- `API key` (dạng `sk_...` hoặc `xi_...`).
4. Bấm **Thêm** để lưu.

Gợi ý: thêm nhiều khóa để tránh gián đoạn khi một khóa hết quota.

## 3. Văn bản thành giọng nói (Text To Speech)

### Cách dùng

1. Vào tab **Văn bản thành Giọng nói**.
2. Nhập/dán văn bản (tối đa 50.000 ký tự mỗi lần nhập).
3. Chọn `Voice`, `Model` và tinh chỉnh thông số.
4. Bấm **Generate**.
5. Nghe lại và tải file MP3 từ trình phát.

### Thông số chính

- `Speed`: 0.7 -> 1.2
- `Stability`: 0 -> 1
- `Similarity`: 0 -> 1
- `Style`: 0 -> 1
- `Speaker boost`: bật/tắt

### Lưu ý

- Nếu văn bản dài hơn 5.000 ký tự, app sẽ tự chia chunk để xử lý.
- Có nút **Optimize** để tối ưu câu chữ bằng Kimi trước khi tạo giọng.

## 4. Parallel TTS (tạo nhiều câu cùng lúc)

### Cách dùng

1. Vào tab **Parallel TTS**.
2. Nhập nhiều dòng/câu (mỗi dòng là một câu).
3. Có thể bấm **Import** để nạp từ file `.txt`.
4. Chọn cài đặt chung ở panel phải.
5. Bấm **Generate All**.

### Tùy chỉnh từng câu

- Bấm icon `Settings` ở từng dòng để bật `custom settings` riêng.
- Có thể đặt riêng voice/model/speed/stability/similarity/style/speaker boost cho từng câu.

### Mẹo thao tác nhanh

- `Enter`: thêm dòng mới.
- `Backspace` trên dòng trống: xóa dòng.
- Khi có kết quả: có thể nghe từng câu hoặc tải file tổng hợp.

## 5. Qwen3 TTS (local model)

Tab **Qwen3 TTS** là luồng local, gồm 2 bước lớn:

1. **Enable/Setup environment** (chạy cài đặt môi trường).
2. **Download model** trước khi generate.

### Các chế độ model

- `CustomVoice`
- `Base` (cần file audio tham chiếu)
- `VoiceDesign` (cần prompt mô tả giọng)

Sau khi đủ điều kiện, nhập text và bấm **Generate** để tạo audio.

## 6. Speech To Text (STT)

### Cách dùng

1. Vào tab **Speech To Text**.
2. Kéo-thả hoặc chọn file audio/video.
3. Tùy chọn:
- `Language override` (ví dụ: `vie`, `eng`).
- `Key terms` (các từ ngữ chuyên ngành, ngăn cách bằng dấu phẩy).
- `Speaker diarization` nếu muốn tách người nói.
4. Đợi xử lý xong và sao chép transcript.

## 7. Sound Effects

### Cách dùng

1. Mô tả hiệu ứng âm thanh bằng văn bản.
2. Chỉnh:
- `Duration` (0.5 -> 30 giây)
- `Prompt influence` (0 -> 1)
- `Loop` (bật/tắt)
3. Bấm **Generate SFX**.
4. Nghe lại và tải file MP3.

## 8. Music

### Cách dùng

1. Nhập prompt mô tả bài nhạc.
2. Chỉnh độ dài `5 -> 300` giây.
3. Bấm **Generate Music**.
4. Nghe lại và tải file MP3.

## 9. Quản lý khóa API ElevenLabs

Trong tab **Khóa API** bạn có thể:

- Thêm/Xóa key.
- Import key hàng loạt bằng file.
- Chọn chiến lược phân phối key:
- `least-used`
- `round-robin`
- `random`
- Health check từng key hoặc toàn bộ.
- Sync usage toàn bộ key.
- Xem biểu đồ lịch sử sử dụng theo mốc `24h / 7d / 30d / all`.

## 10. Quản lý Kimi token

Trong tab **Kimi Keys**:

- Thêm/xóa token Kimi.
- Chọn chiến lược phân phối token.
- Health check token.
- Theo dõi trạng thái và số request.

Lưu ý: tính năng này có thể bị giới hạn theo license.

## 11. Settings

Tab **Settings** gồm 4 phần:

### Proxy

- Bật/tắt proxy.
- Chọn loại `HTTP/HTTPS/SOCKS4/SOCKS5`.
- Cấu hình host/port/user/password.
- Test kết nối trước khi lưu.

### Tunnel

- Bật local API server (chọn port).
- Tạo public tunnel qua `Cloudflare` hoặc `ngrok`.
- Sao chép public URL để dùng bên ngoài.

### Auto-save

- Bật/tắt tự lưu file đầu ra.
- Chọn thư mục lưu.
- Chọn format tên file (`timestamp`, `feature-timestamp`, `custom`).
- Tùy chọn tách thư mục theo tính năng.
- Có nút test trước khi lưu cấu hình.

### License

- Xem trạng thái gói hiện tại.
- Kích hoạt key Pro.
- Hủy kích hoạt key đang dùng.

## 12. Trang Donate

Tab **Donate** mở trang ủng hộ từ giao diện app. Nếu không tải được trang nội bộ, app có fallback URL và cho mở ngoài trình duyệt.

## 13. Các lỗi thường gặp

- `Invalid API key`: key sai hoặc đã bị thu hồi.
- `Quota exceeded`: key hết hạn mức, cần đổi key khác.
- Generate chậm: kiểm tra mạng/proxy hoặc giảm độ dài input.
- Qwen3 chưa chạy: chưa setup environment hoặc chưa tải model.

## 14. Kiểm tra kỹ thuật dự án

Tại lần cập nhật tài liệu này, lệnh build chạy thành công:

- `npm run build` -> thành công cho `vite`, `electron main`, `preload`.
- Có cảnh báo bundle lớn (không làm build thất bại).


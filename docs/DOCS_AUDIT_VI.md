# Docs Audit (VI) - 2026-03-06

Mục tiêu: đối chiếu tính năng thực tế trong code với tài liệu tiếng Việt cho người dùng, xác định thiếu/outdated, và xác nhận đã bổ sung.

## Phạm vi kiểm tra

- Nguồn sự thật tính năng: `src/App.tsx`, `src/components/Sidebar.tsx`, các page trong `src/pages/*`.
- Tài liệu đối chiếu: `docs/README-vi.md`, `docs/huong-dan-su-dung-vi.md`, `docs/huong-dan-nhanh-vi.md`, `docs/INDEX-vI.md`.

## Ma trận tính năng và độ phủ docs

| Tính năng (theo code) | Route | Trạng thái docs | Nơi đã mô tả |
|---|---|---|---|
| Text To Speech | `/tts` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Parallel TTS | `/parallel-tts` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Qwen3 TTS (local) | `/qwen3-tts` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Speech To Text | `/stt` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Sound Effects | `/sfx` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Music | `/music` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| API Key Manager | `/keys` | OK | `README-vi`, `huong-dan-su-dung-vi` |
| Kimi Key Manager | `/kimi-keys` | OK | `README-vi`, `huong-dan-su-dung-vi` |
| Settings (Proxy/Tunnel/Auto-save/License) | `/settings` | OK | `README-vi`, `huong-dan-su-dung-vi`, `huong-dan-nhanh-vi` |
| Donate | `/donate` | OK | `README-vi`, `huong-dan-su-dung-vi` |

## Các điểm outdated đã xử lý

1. Đồng bộ tên ứng dụng trong chỉ mục VI từ “ElevenCreative” sang “TeSHub Desktop”.
2. Cập nhật lại chỉ mục tài liệu vì nội dung cũ không còn khớp với bản hướng dẫn mới.
3. Bổ sung tài liệu kiểm tra coverage này để theo dõi lần cập nhật tiếp theo.

## Các lưu ý quan trọng

- Route `/vieneu-tts` đang bị disable trong code nên không đưa vào hướng dẫn sử dụng chính.
- Một số tài liệu kỹ thuật khác trong `docs/` có thể chứa bối cảnh lịch sử triển khai; không ảnh hưởng trực tiếp đến hướng dẫn user-facing.

## Kết luận

- Bộ docs tiếng Việt cho người dùng hiện đã có đủ các tính năng đang bật trong app.
- Các mục chính đã được đồng bộ theo trạng thái code hiện tại.

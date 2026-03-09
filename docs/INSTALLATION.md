# Cài Đặt (Installation)

Hướng dẫn cài đặt TeSHub Desktop cho Windows, macOS và Linux.

## Windows 10 / Windows 11

### Yêu Cầu
- Windows 10 hoặc mới hơn
- RAM: 4GB (8GB khuyên cáo)
- Ổ cứng: 2GB khoảng trống
- Quyền Administrator (để cài đặt)

### Các Bước Cài Đặt

**Bước 1: Tải Xuống**
1. Truy cập [TeSHub Download](https://teshub.dev/download)
2. Chọn **Windows** → Tải file `.zip`
3. Chờ tải hoàn tất (~100MB)

**Bước 2: Giải Nén**
1. Tìm file `TeSHub-Desktop-win.zip` trong thư mục Downloads
2. Nhấp chuột phải → **Extract All**
3. Chọn vị trí (ví dụ: `C:\Program Files\TeSHub`)

**Bước 3: Chạy Ứng Dụng**
1. Mở thư mục đã giải nén
2. Tìm file `TeSHub Desktop.exe`
3. Nhấp đôi để chạy

**Bước 4: Xác Nhận Bảo Mật (nếu cần)**
- Windows có thể hiển thị cảnh báo "Unknown Publisher"
- Nhấp **More info** → **Run anyway**

### Tạo Shortcut Desktop (Tùy Chọn)
1. Nhấp chuột phải trên `TeSHub Desktop.exe`
2. Chọn **Send to** → **Desktop (create shortcut)**
3. Shortcut sẽ xuất hiện trên Desktop

### Gỡ Cài Đặt
1. Tìm thư mục cài đặt
2. Xóa toàn bộ thư mục
3. Dữ liệu lưu ở: `%APPDATA%\TeSHub Desktop`
   - Để giữ dữ liệu: Sao lưu thư mục này trước
   - Để xóa hoàn toàn: Xóa cả thư mục này

---

## macOS (Intel & Apple Silicon)

### Yêu Cầu
- macOS 12 (Monterey) hoặc mới hơn
- RAM: 4GB (8GB khuyên cáo)
- Ổ cứng: 2GB khoảng trống
- Apple ID có thể được yêu cầu (cho app từ lần đầu)

### Các Bước Cài Đặt

**Bước 1: Tải Xuống**
1. Truy cập [TeSHub Download](https://teshub.dev/download)
2. Chọn **macOS** (tự động chọn Intel hoặc Apple Silicon)
3. Tải file `.zip`

**Bước 2: Giải Nén**
1. Tìm file `TeSHub Desktop.app.zip` trong Downloads
2. Nhấp đôi để tự động giải nén
3. File `TeSHub Desktop.app` sẽ xuất hiện

**Bước 3: Di Chuyển vào Applications**
1. Mở Finder → Applications
2. Kéo file `TeSHub Desktop.app` vào Applications
3. Hoặc: Nhấp chuột phải → **Move to Applications**

**Bước 4: Chạy Lần Đầu**
1. Mở Applications folder
2. Tìm `TeSHub Desktop`
3. Nhấp chuột phải → **Open** (nếu hiển thị cảnh báo)
4. Nhấp **Open** trong dialog xác nhận

**Bước 5: Cho Phép Quyền (nếu cần)**
- macOS có thể yêu cầu quyền microphone/camera
- Nhấp **Allow** hoặc điều chỉnh trong **System Preferences** → **Privacy & Security**

### Lần Sau
- Mở Applications → Nhấp đôi `TeSHub Desktop`
- Hoặc: Tìm trên Spotlight (Cmd + Space) → gõ "TeSHub"

### Gỡ Cài Đặt
1. Mở Applications folder
2. Kéo `TeSHub Desktop` vào Trash
3. Dữ liệu lưu ở: `~/Library/Application Support/TeSHub Desktop`
   - Để giữ: Sao lưu trước
   - Để xóa: Xóa thư mục này

---

## Linux (Ubuntu 20.04+ & Tương Đương)

### Yêu Cầu
- Ubuntu 20.04 LTS hoặc mới hơn (hoặc distro tương đương)
- RAM: 4GB (8GB khuyên cáo)
- Ổ cứng: 2GB khoảng trống
- Terminal/Shell access

### Các Bước Cài Đặt

**Bước 1: Tải Xuống**
```bash
# Download file
wget https://releases.teshub.dev/TeSHub-Desktop-linux.zip
```

**Bước 2: Giải Nén**
```bash
# Giải nén
unzip TeSHub-Desktop-linux.zip

# Di chuyển vào /opt (tuỳ chọn nhưng khuyên cáo)
sudo mv TeSHub-Desktop /opt/teshub-desktop
```

**Bước 3: Cấp Quyền Thực Thi**
```bash
# Cấp quyền chạy
chmod +x /opt/teshub-desktop/TeSHub\ Desktop
```

**Bước 4: Chạy Ứng Dụng**
```bash
# Chạy trực tiếp
/opt/teshub-desktop/TeSHub\ Desktop

# Hoặc (nếu ở thư mục cài đặt)
./TeSHub\ Desktop
```

### Tạo Desktop Entry (Tuỳ Chọn)
```bash
# Tạo file desktop entry
cat > ~/.local/share/applications/teshub-desktop.desktop << 'EOF'
[Desktop Entry]
Name=TeSHub Desktop
Exec=/opt/teshub-desktop/TeSHub\ Desktop
Icon=application-x-executable
Type=Application
Categories=Audio;
EOF

# Cấp quyền
chmod +x ~/.local/share/applications/teshub-desktop.desktop
```

Sau đó, tìm **TeSHub Desktop** trong Applications menu.

### Gỡ Cài Đặt
```bash
# Xóa ứng dụng
sudo rm -rf /opt/teshub-desktop

# Xóa desktop entry (nếu tạo)
rm ~/.local/share/applications/teshub-desktop.desktop

# Xóa dữ liệu (tuỳ chọn)
rm -rf ~/.config/TeSHub\ Desktop
```

---

## Xác Minh Cài Đặt Thành Công

Ứng dụng cài đặt thành công nếu:

✅ Ứng dụng khởi động mà không có lỗi
✅ Cửa sổ chính xuất hiện với menu
✅ Thấy tab **Text-to-Speech** và **Settings**
✅ Không có thông báo lỗi trong console

## Vấn Đề & Giải Pháp

### Windows: "Unknown Publisher" Warning
- **Nguyên nhân**: App không được ký nhân chứng
- **Giải pháp**: Nhấp **More info** → **Run anyway**

### macOS: "'TeSHub Desktop' cannot be opened"
- **Giải pháp 1**: Nhấp chuột phải → Open
- **Giải pháp 2**: System Preferences → Privacy & Security → Allow

### Linux: Permission Denied
```bash
# Cấp quyền lại
chmod +x ./TeSHub\ Desktop
```

### Lỗi: "Cannot find dependencies"
- **Giải pháp**: Cài đặt libraries cần thiết
```bash
# Ubuntu/Debian
sudo apt-get install libgtk-3-0 libssl3
```

## Cập Nhật

TeSHub Desktop sẽ thông báo khi có phiên bản mới:
1. Tải file mới từ [Download](https://teshub.dev/download)
2. Cài đặt lại (dữ liệu sẽ được giữ lại)
3. Hoặc: Xóa folder cũ → Giải nén folder mới (để dữ liệu)

---

**Cần trợ giúp?** Xem [FAQ](./GITBOOK_FAQ.md) hoặc [Liên Hệ](./GITBOOK_CONTACT_SUPPORT.md).

*Lần cập nhật cuối: 2026-03-08*

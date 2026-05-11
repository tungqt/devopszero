# Quản lý User, Phân quyền và Process trong Linux

## Document Information
- **Doc ID:** 02-002
- **Summary:** Hướng dẫn cách quản lý người dùng (User/Group), phân quyền file (chmod, chown) và quản lý tiến trình (Process/Service).
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Quản lý User và Group
Trong môi trường Server, không bao giờ chạy ứng dụng dưới quyền `root` (Admin tối cao) vì lý do bảo mật. Bạn phải biết cách tạo user riêng cho từng ứng dụng.

- `whoami`: Xem bạn đang đăng nhập bằng user nào.
- `id`: Xem thông tin user và các group mà user đó thuộc về.
- `sudo <lệnh>`: Chạy một lệnh với quyền `root` (Super User DO).
- `su - <tên_user>`: Chuyển đổi sang user khác.
- `useradd <tên_user>`: Tạo user mới.
- `passwd <tên_user>`: Đặt mật khẩu cho user.

## 2. Phân quyền File/Thư mục (Permissions)
Đây là phần quan trọng nhất gây ra 90% lỗi "Permission Denied" khi bạn làm DevOps.

Khi bạn gõ `ls -l`, bạn sẽ thấy một chuỗi như: `-rwxr-xr--`
Chuỗi này chia làm 4 phần: `[-] [rwx] [r-x] [r--]`
1. Ký tự đầu: `-` là file, `d` là thư mục (directory).
2. 3 ký tự tiếp theo (`rwx`): Quyền của **User** (Chủ sở hữu).
3. 3 ký tự tiếp theo (`r-x`): Quyền của **Group** (Nhóm).
4. 3 ký tự cuối (`r--`): Quyền của **Others** (Những người khác).

**Ý nghĩa các quyền:**
- `r` (Read = 4): Quyền đọc.
- `w` (Write = 2): Quyền ghi/sửa.
- `x` (Execute = 1): Quyền thực thi (chạy script).

### 2.1. Lệnh `chmod` (Change Mode - Đổi quyền)
Sử dụng hệ cơ số 8 (cộng các số lại với nhau):
- `chmod 777 script.sh`: Cấp toàn quyền cho tất cả mọi người (4+2+1 = 7). **Rất nguy hiểm, hạn chế dùng!**
- `chmod 755 script.sh`: User có toàn quyền (7), Group và Others chỉ được đọc và chạy (4+1 = 5).
- `chmod +x script.sh`: Cách viết nhanh để cấp quyền thực thi cho file.

### 2.2. Lệnh `chown` (Change Owner - Đổi chủ sở hữu)
- `chown user:group file.txt`: Đổi chủ sở hữu và nhóm của file.
- `chown -R nginx:nginx /var/www/html`: Đổi chủ sở hữu của toàn bộ thư mục (Recursive).

## 3. Quản lý Tiến trình (Process Management)
Khi một ứng dụng bị treo, bạn cần biết cách tìm và "giết" nó.

- `top` hoặc `htop`: Xem các tiến trình đang chạy realtime, mức tiêu thụ CPU/RAM (nhấn `q` để thoát).
- `ps aux`: Liệt kê tất cả các tiến trình đang chạy (thường kết hợp với `grep`).
  - Ví dụ: `ps aux | grep node` (Tìm xem app NodeJS có đang chạy không).
- `kill <PID>`: Dừng một tiến trình (PID lấy từ lệnh `ps`).
- `kill -9 <PID>`: Ép buộc giết tiến trình ngay lập tức (Force kill).

## 4. Quản lý Service (Systemd)
Các ứng dụng chạy ngầm (như Nginx, Docker) được quản lý bởi Systemd.

- `systemctl status nginx`: Xem trạng thái của service Nginx (đang chạy hay lỗi).
- `systemctl start nginx`: Bật service.
- `systemctl stop nginx`: Tắt service.
- `systemctl restart nginx`: Khởi động lại service (thường dùng khi vừa sửa file config).
- `systemctl enable nginx`: Cấu hình cho service tự động chạy mỗi khi server khởi động lại (Reboot).

## 5. Bài tập thực hành (Lab 2)
1. Tạo một file tên là `run.sh` chứa nội dung `echo "Hello DevOps"`.
2. Thử chạy file đó bằng lệnh `./run.sh` (Sẽ bị báo lỗi Permission Denied).
3. Dùng `chmod` để cấp quyền thực thi cho file đó.
4. Chạy lại file để thấy kết quả thành công.
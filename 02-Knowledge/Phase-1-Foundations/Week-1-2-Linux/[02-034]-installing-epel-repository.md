# Cài đặt Repository Mới trên Red Hat và CentOS (Lab)

## Document Information
- **Doc ID:** 02-034
- **Summary:** Bài lab hướng dẫn cách bổ sung kho lưu trữ (Repository) mới vào các hệ thống Enterprise Linux (như RHEL, CentOS) để mở rộng danh sách phần mềm. Đặc biệt là kho EPEL (Extra Packages for Enterprise Linux).
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tại sao cần phải thêm Repository mới?

Các nhà phân phối Enterprise Linux (như Red Hat, CentOS) nổi tiếng với sự bảo thủ nhằm đặt mục tiêu ổn định lên hàng đầu. 
- Kho lưu trữ mặc định của họ (Default Repository) thường chỉ cung cấp một tập hợp rất nhỏ (small subset) các phần mềm thiết yếu. 
- Các phiên bản phần mềm thường không phải là bản mới nhất (not cutting edge), mà là các phiên bản cũ đã được kiểm chứng qua thời gian dài.
- Mục đích: Giúp họ kiểm soát tốt sự tương tác giữa các gói (package interaction), giảm thiểu tối đa lỗi phát sinh. Càng nhiều phần mềm, số lượng lỗi tiềm ẩn càng tăng theo cấp số nhân.

**Vấn đề:** Trong thực tế, bạn thường xuyên cần các phần mềm thông dụng nhưng lại không có sẵn trong kho mặc định. Thay vì tự tải mã nguồn về và biên dịch (install from source) rất mệt mỏi, chúng ta có thể **thêm các Repository của bên thứ ba (Third-party Repositories)** vào hệ thống để dùng `dnf` hoặc `yum` cài đặt như bình thường.

---

## 2. Kho lưu trữ EPEL (Extra Packages for Enterprise Linux)

Đối với họ Red Hat / CentOS, một trong những Repository bên thứ ba nổi tiếng và an toàn nhất là **EPEL**.
- Nó chứa hàng ngàn gói phần mềm được port sang từ dự án Fedora (người anh em tiên phong của Red Hat).
- Các gói này được kỳ vọng là sẽ cài đặt mượt mà và không gây xung đột (play well) với các phần mềm mặc định của hệ thống.
- *(Lưu ý: Mặc dù rất tốt, nhưng các phần mềm từ EPEL sẽ không nhận được hỗ trợ kỹ thuật chính thức từ Red Hat).*

> 📌 **Lưu ý Lab:** Bài Lab này **CHỈ** dành cho các hệ thống RHEL, CentOS, AlmaLinux, hoặc Rocky Linux. Nó không áp dụng cho Debian/Ubuntu.

---

## 3. Thực hành: Cài đặt EPEL Repository

Việc cài đặt một Repository thực chất là việc cài đặt một gói tin `.rpm` rất nhỏ. Gói tin này chứa thông tin cấu hình (URL trỏ về máy chủ EPEL) và khóa GPG (để hệ thống kiểm tra tính hợp lệ của file tải về).

### Bước 1: Tải và cài đặt gói cấu hình EPEL

Bạn tải file RPM trực tiếp từ trang web của dự án EPEL (phải chọn đúng phiên bản hệ điều hành của mình, ví dụ CentOS 7 hay 8) và dùng lệnh `rpm` cấp thấp để cập nhật/cài đặt nó.

**Cú pháp tổng quát:**
```bash
sudo rpm -Uvh epel-release*.noarch.rpm
```

**Ví dụ thực tế cho CentOS 8 / RHEL 8:**
```bash
sudo rpm -Uvh epel-release-8-6.el8.noarch.rpm
```
> *(Mẹo: Trên các hệ thống CentOS đời mới, bạn thậm chí không cần phải tải file .rpm thủ công. Kho mặc định `extras` của CentOS đã chứa sẵn gói này, bạn chỉ cần gõ lệnh `sudo dnf install epel-release` là xong).*

### Bước 2: Kiểm tra cấu hình

Ngay sau khi lệnh cài đặt hoàn tất, hệ thống sẽ tự động tạo ra một tệp tin cấu hình Repository tại thư mục:
```text
/etc/yum.repos.d/
```
Hãy mở thư mục này ra kiểm tra:
```bash
ls -l /etc/yum.repos.d/
```
Bạn sẽ thấy một file tên là **`epel.repo`**.

### Bước 3: Đọc file cấu hình mẫu (Template)

Hãy dùng lệnh `cat` để đọc nội dung file `epel.repo`:
```bash
cat /etc/yum.repos.d/epel.repo
```
Hãy xem kỹ file này. Việc hiểu cấu trúc của file `.repo` này rất quan trọng, vì nó đóng vai trò là **khuôn mẫu (template)** nếu sau này bạn muốn tự tay thêm các Repository khác (Ví dụ: repo của Docker, của Nginx, của MongoDB...).

Nội dung bên trong thường có các dòng quan trọng như:
- `[epel]`: Tên định danh của repo.
- `name=...`: Tên mô tả hiển thị.
- `baseurl=...` hoặc `metalink=...`: Đường dẫn tải phần mềm.
- `enabled=1`: Kích hoạt repo này.
- `gpgcheck=1`: Yêu cầu kiểm tra chữ ký số để chống mã độc.

---

## 4. Tổng kết

Khi làm việc với các hệ thống Enterprise (CentOS/RHEL), câu lệnh đầu tiên mà nhiều SysAdmin gõ ngay sau khi tạo xong máy chủ là cài `epel-release`. Nếu bạn dùng một máy ảo CentOS của bên thứ 3 (ví dụ từ Linux Foundation), rất có thể họ đã cài sẵn gói này cho bạn rồi. Việc biết vị trí file cấu hình tại `/etc/yum.repos.d/` sẽ giúp bạn làm chủ được nguồn gốc các phần mềm trong máy chủ của mình.
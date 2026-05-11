# Quá trình Khởi tạo Hệ thống (System Initialization)

## Document Information
- **Doc ID:** 02-011
- **Summary:** Tìm hiểu về giai đoạn System Initialization sau khi Kernel khởi động, vai trò của tiến trình `init` (PID 1), và sự tiến hóa từ System V init đến systemd.
- **Version:** 0.0.1
- **Date:** 2026-05-07
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
Tại sao DevOps cần hiểu về quá trình System Initialization?
Sau khi **Kernel** khởi động xong, hệ thống vẫn chưa thể sử dụng được ngay. Nó cần một cơ chế để khởi chạy các dịch vụ ngầm (services/daemons) như mạng (network), SSH, hay web server, cũng như chuẩn bị môi trường cho người dùng đăng nhập. Việc hiểu hệ thống khởi tạo (đặc biệt là **systemd**) giúp bạn biết cách cấu hình các ứng dụng tự động chạy (auto-start) khi máy chủ bật, hoặc khắc phục (troubleshoot) khi một dịch vụ bị treo (crash).

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Local Linux VM hoặc AWS EC2 instance.
- **Yêu cầu:** Mọi bản phân phối Linux hiện đại (Ubuntu, CentOS, RHEL, Debian...). Các lệnh kiểm tra trong bài này có thể chạy trực tiếp bằng user thường mà không cần `sudo`.

## 3. Tiến trình Đầu tiên: `init`
Tiến trình đầu tiên được thực thi sau khi Kernel tải xong được gọi chung là **`init`**.
- Nó luôn mang **Process ID (PID) là 1** (các tiến trình chạy sau đó sẽ mang các số ID lớn hơn).
- Theo truyền thống, nó được coi là "cha của mọi tiến trình" trong User Space (không gian người dùng).
- **Nhiệm vụ chính:**
  - Cấu hình các khía cạnh của môi trường hệ thống.
  - Khởi chạy các dịch vụ cần thiết để hệ thống hoạt động và cho phép người dùng đăng nhập.
  - Theo dõi trạng thái khi một tiến trình kết thúc để dọn dẹp tài nguyên một cách gọn gàng.

## 4. Sự tiến hóa của Hệ thống Khởi tạo

Hệ thống quản lý `init` đã trải qua nhiều thay đổi theo thời gian:

### 4.1. System V init (SysVinit)
- Bắt nguồn từ những ngày đầu của hệ điều hành Unix.
- **Cơ chế:** Hoạt động theo chuỗi tuần tự (serial). Tiến trình này khởi chạy xong thì tiến trình khác mới được bắt đầu, dựa trên khái niệm các mức chạy (Runlevel).
- **Nhược điểm:** Phù hợp với các hệ thống Mainframe cổ điển. Trong môi trường hiện đại, thời gian khởi động rất quan trọng, và việc chạy tuần tự không tận dụng được sức mạnh của kiến trúc CPU đa nhân.

### 4.2. Upstart
- Ra mắt năm 2006 do Ubuntu phát triển để khắc phục nhược điểm của SysVinit.
- **Cơ chế:** Khởi chạy dịch vụ bất đồng bộ (asynchronous).
- **Trạng thái:** Hiện nay đã bị coi là **lỗi thời (obsolete)** và loại bỏ trên các bản phân phối lớn.

### 4.3. systemd (Tiêu chuẩn hiện đại)
- Được các bản phân phối lớn áp dụng từ khoảng năm 2011.
- **Cơ chế:** Tận dụng tối đa đa luồng (multiple CPUs), cho phép khởi chạy song song nhiều dịch vụ cùng lúc, giúp tối ưu hóa cực tốt thời gian boot.
- **Trạng thái:** Ngày nay, **tất cả** các bản phân phối Linux hiện đại đều đã chuyển hẳn sang sử dụng `systemd` (SysVinit đã bị loại bỏ, dù một số thuật ngữ như Runlevel đôi khi vẫn được giữ lại vì thói quen).

## 5. Công cụ `systemctl`
Trong hệ sinh thái `systemd`, công cụ quan trọng nhất để tương tác hằng ngày là lệnh **`systemctl`**. Đây là công cụ DevOps sẽ sử dụng liên tục để kiểm tra trạng thái, bật/tắt (start/stop) và cho phép các dịch vụ tự khởi động cùng máy chủ. Chúng ta sẽ làm quen chi tiết với nó trong các bài tiếp theo.

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Kiểm tra tiến trình `init` và xác nhận hệ thống Linux của bạn đang sử dụng `systemd`.

**Bước 1:** Kiểm tra tiến trình có PID = 1.
```bash
ps -p 1 -f
```
*Lệnh này dùng để xem thông tin của tiến trình số 1. Cột `CMD` sẽ hiển thị lệnh đang chạy.*

**Bước 2:** Xác nhận `/sbin/init` thực chất là một liên kết (link) đến `systemd`.
Vì lý do tương thích ngược (để các script cũ gọi `/sbin/init` vẫn hoạt động), các hệ thống hiện đại thường cấu hình `init` là một symbolic link trỏ thẳng tới `systemd`.
```bash
ls -l /sbin/init
```
*Kết quả trả về sẽ có dạng: `lrwxrwxrwx 1 root root ... /sbin/init -> /lib/systemd/systemd`.*

**Bước 3:** Xem sơ đồ cây của tất cả các tiến trình (Process Tree).
```bash
pstree -p | head -n 10
```
*(Nếu hệ thống báo lỗi không có lệnh `pstree`, bạn có thể cài đặt bằng lệnh `sudo apt install psmisc` trên Ubuntu hoặc `sudo yum install psmisc` trên CentOS).*

*Kết quả sẽ hiển thị nhánh cao nhất của hệ thống chính là `systemd(1)`, chứng tỏ nó là cha của toàn bộ các tiến trình đang chạy bên dưới.*

---

## 7. Lab Nâng cao: Tắt/Bật Giao diện Đồ họa (GUI)

**Mục tiêu:** Thử nghiệm việc can thiệp vào quá trình khởi tạo hệ thống bằng cách chủ động tắt và bật lại Giao diện người dùng đồ họa (Graphical User Interface - GUI).

> 📌 **Note (2026-05-07):** Giống như bài thực hành cấu hình GRUB trước đó, bài Lab này đòi hỏi bạn phải có một hệ điều hành Linux phiên bản Desktop (có giao diện đồ họa như GNOME, KDE). Do hiện tại bạn đang thực hành trên máy chủ AWS EC2 (phiên bản Server mặc định không có giao diện), vui lòng **chỉ đọc để hiểu khái niệm** và đánh dấu để thực hành sau khi có Local VM.

**Bước 1: Tắt Giao diện Đồ họa (GUI)**
Trong một môi trường có giao diện đồ họa (như Ubuntu Desktop), nếu bạn mở Terminal và chạy lệnh sau, toàn bộ Desktop sẽ bị tắt và màn hình sẽ chuyển về chế độ dòng lệnh đen trắng thuần túy (Text Console):

- **Trên hệ thống dùng `systemd` (hiện đại):**
  ```bash
  sudo systemctl stop gdm
  ```
  *(Lưu ý: `gdm` là GNOME Display Manager. Nếu bạn dùng môi trường khác, tên dịch vụ có thể là `lightdm`, `kdm`...)*

- **Trên hệ thống dùng SysVinit (cũ):**
  ```bash
  sudo telinit 3
  ```
  *(Số 3 đại diện cho Runlevel 3: Multi-user mode with networking, không có giao diện đồ họa).*

**Bước 2: Bật lại Giao diện Đồ họa (GUI)**
Từ màn hình dòng lệnh Text Console, bạn có thể gọi lại giao diện đồ họa bằng các lệnh sau:

- **Trên hệ thống dùng `systemd`:**
  ```bash
  sudo systemctl start gdm
  ```

- **Trên hệ thống dùng SysVinit:**
  ```bash
  sudo telinit 5
  ```
  *(Số 5 đại diện cho Runlevel 5: Tương tự Runlevel 3 nhưng bật thêm giao diện đồ họa).*

*Ý nghĩa của Lab:* Bài thực hành này minh họa rất rõ cách `systemctl` (hoặc `telinit` ngày xưa) kiểm soát các trạng thái hoạt động của hệ thống, cho phép bạn chuyển đổi linh hoạt giữa chế độ Server (chỉ dòng lệnh) và Desktop (có giao diện) mà không cần phải khởi động lại máy.
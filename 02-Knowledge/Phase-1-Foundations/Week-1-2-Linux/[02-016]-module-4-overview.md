# Tổng quan Module 4: Giám sát Hệ thống và Quản lý Thiết bị (System Monitoring & Device Management)

## Document Information
- **Doc ID:** 02-016
- **Summary:** Giới thiệu tổng quan về Module 4, bao gồm hai chủ đề cốt lõi: Giám sát hiệu năng hệ thống (System Monitoring) và Quản lý mô-đun nhân cùng thiết bị (Kernel Modules & Device Management).
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần học Module này?**
- **System Monitoring:** Việc giữ cho máy chủ hoạt động ổn định là nhiệm vụ sống còn. Bạn cần biết hệ thống đang ngốn bao nhiêu CPU, RAM, Disk I/O hay Network để có thể mở rộng (scale) kịp thời hoặc tìm ra thủ phạm gây giật lag. Việc thành thạo các công cụ dòng lệnh (CLI) giúp bạn chẩn đoán sự cố ngay cả khi SSH vào các máy chủ không có giao diện đồ họa.
- **Device Management & Kernel Modules:** Linux rất linh hoạt. Bạn không cần phải khởi động lại máy chủ mỗi khi cắm thêm một ổ cứng mới hay cần hỗ trợ một giao thức mạng mới. Hiểu cách Linux tải/gỡ các "mô-đun nhân" (Kernel modules) và cách `udev` tự động nhận diện phần cứng giúp bạn làm chủ hoàn toàn quá trình quản lý thiết bị.

## 2. Giám sát Hiệu năng Hệ thống (System Monitoring)

Linux cung cấp rất nhiều công cụ để theo dõi sức khỏe của hệ thống:

### 2.1. Công cụ Dòng lệnh (CLI Utilities)
Đây là "vũ khí" chính của các Quản trị viên hệ thống (SysAdmin) và DevOps, bởi vì 99% máy chủ trên môi trường Production (như AWS EC2) không cài đặt giao diện đồ họa.
- **Giám sát CPU & Tiến trình:** Lệnh `top` là công cụ kinh điển nhất, giúp bạn quản lý mức sử dụng CPU và tìm ra ngay tiến trình (process) nào đang ngốn tài nguyên nhất.
- **Các thành phần khác:** Có những công cụ chuyên biệt khác để kiểm tra RAM (`free`), tốc độ đọc/ghi ổ cứng (I/O usage), và hiệu suất mạng (network performance) mà chúng ta sẽ tìm hiểu chi tiết.

### 2.2. Công cụ Đồ họa (GUI Monitors)
Nếu bạn sử dụng Linux phiên bản Desktop (như Ubuntu Desktop), hệ thống luôn đi kèm các công cụ trực quan:
- **GNOME System Monitor:** Dành cho môi trường GNOME (giống Task Manager của Windows).
- **ksysguard:** Dành cho môi trường KDE.
*Lưu ý: Dù có GUI, một DevOps chuyên nghiệp vẫn ưu tiên và thành thạo CLI hơn.*

## 3. Quản lý Mô-đun Nhân (Kernel Modules)

Một trong những sức mạnh tuyệt vời nhất của nhân Linux (Linux Kernel) là khả năng **thêm vào và gỡ bỏ các thành phần ngay lúc hệ thống đang chạy (runtime)** mà không cần reboot. Các thành phần này gọi là **Kernel Modules**.

- **Khi nào cần tải Module?** 
  - Khi bạn cắm một thiết bị mới (ví dụ: USB, Card mạng mới) và cần Driver để nó hoạt động.
  - Khi bạn cần kích hoạt một giao thức mạng mới, một loại Filesystem mới (như ZFS hay Btrfs) vốn không chạy sẵn để tiết kiệm RAM, nhưng năng lực luôn sẵn sàng khi bạn "gọi" (load) nó lên.

## 4. Quản lý Thiết bị với `udev` (Device Management)

Quản lý thiết bị là một bài toán rất phức tạp: Làm sao hệ điều hành biết bạn vừa cắm một cái USB vào, nên đặt tên nó là gì, cấp quyền đọc/ghi cho ai, và thông báo cho ứng dụng nào?

- Giải pháp của Linux là hệ thống **`udev` (User Device)**. 
- Nó là một daemon (dịch vụ ngầm) lắng nghe các sự kiện từ Kernel mỗi khi có phần cứng mới được tìm thấy (lúc khởi động hoặc cắm nóng hot-plug), sau đó tự động tạo các file đại diện cho thiết bị trong thư mục `/dev/` và thực thi các rule (quy tắc) tương ứng.

Trong các bài học tiếp theo, chúng ta sẽ đi sâu vào từng công cụ giám sát, cách tải một Kernel module, và thực hành tương tác với các sự kiện của `udev`.
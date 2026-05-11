# Tổng quan Module 3: System Components

## Document Information
- **Doc ID:** 02-009
- **Summary:** Giới thiệu tổng quan về 3 thành phần cốt lõi của hệ thống Linux: System Initialization (Quá trình khởi động), Memory Management (Quản lý bộ nhớ) và Network Interfaces (Giao diện mạng).
- **Version:** 0.0.1
- **Date:** 2026-05-07
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. System Initialization (Quá trình Khởi động Hệ thống)

Khi bạn nhấn nút nguồn của máy chủ, Linux không bật lên ngay lập tức mà phải trải qua nhiều giai đoạn (phases). Quá trình này được chia làm 2 phần chính:

### 1.1. Early Boot Phase (Khởi động sớm)
Giai đoạn này phụ thuộc rất nhiều vào phần cứng (hardware-dependent). Mục tiêu duy nhất của nó là **đánh thức Linux Kernel**.
- Trên hầu hết các máy chủ và máy tính cá nhân (PC/Server), giai đoạn này được quản lý bởi một chương trình gọi là **GRUB** (Grand Unified Bootloader). GRUB cho phép bạn chọn hệ điều hành muốn khởi động (nếu cài nhiều HĐH) hoặc truyền các tham số đặc biệt cho Kernel.
- Trên các thiết bị nhúng (Embedded systems - như Raspberry Pi, router), người ta thường dùng các bootloader khác nhẹ hơn, ví dụ như **Das U-Boot**.

### 1.2. System Services Initialization (Khởi tạo Dịch vụ)
Sau khi Kernel đã được nạp vào RAM và chạy thành công, nó sẽ gọi chương trình đầu tiên của hệ điều hành (thường là `systemd` hoặc `init`).
Giai đoạn này thuộc về phạm trù **Quản trị hệ thống (System Administration)**. Nó chịu trách nhiệm bật mạng, bật tường lửa, khởi động các dịch vụ ngầm (daemons) như SSH, Nginx, Docker...

---

## 2. Memory Management (Quản lý Bộ nhớ)

Bộ nhớ (RAM) là tài nguyên quý giá nhất của máy chủ. Là một DevOps, bạn phải hiểu rõ hệ thống xử lý thế nào khi RAM bị đầy.

### 2.1. Swap Space (Không gian hoán đổi)
Khi RAM vật lý bị đầy, Linux không để hệ thống sập ngay. Nó sẽ lấy một phần dữ liệu ít được sử dụng trên RAM và "đẩy" tạm xuống ổ cứng. Khu vực trên ổ cứng dùng để chứa dữ liệu này gọi là **Swap Space** (có thể là Swap Partition hoặc Swap File).
*Lưu ý: Tốc độ đọc/ghi của ổ cứng chậm hơn RAM hàng vạn lần, nên khi hệ thống phải dùng đến Swap, máy chủ sẽ trở nên rất chậm (lag).*

### 2.2. OOM Killer (Out of Memory Killer)
Nếu RAM đã đầy 100% và Swap cũng đầy nốt (hoặc hệ thống không cấu hình Swap), Linux sẽ kích hoạt một "sát thủ" gọi là **OOM Killer**.
- Nhiệm vụ của OOM Killer là đi "săn" và **giết (kill)** một hoặc vài tiến trình (process) đang ngốn nhiều RAM nhất để cứu sống toàn bộ hệ điều hành.
- *Nỗi ám ảnh của DevOps:* Đôi khi OOM Killer sẽ quyết định "giết" chính cái Database (MySQL) hoặc Web Server (Java/Node.js) quan trọng nhất của bạn vì chúng ngốn quá nhiều RAM. Việc hiểu và cấu hình OOM Killer là kỹ năng sinh tồn.

---

## 3. Network Interfaces (Giao diện Mạng)

Máy chủ Linux không có mạng thì coi như vô dụng. Trong phần này, chúng ta sẽ tìm hiểu về:
- **Cách đặt tên (Naming convention):** Tại sao card mạng không còn tên là `eth0`, `eth1` như ngày xưa mà lại đổi thành `ens5`, `enp3s0`...
- **Quản lý trạng thái:** Cách bật (bring up) hoặc tắt (bring down) một card mạng khi cần thiết.
- **Cấu hình:** Cách hệ thống nhận IP (DHCP) hoặc gán IP tĩnh (Static IP).

---

> 💡 **Định hướng học tập:** Trong 3 chủ đề trên, **Memory Management (đặc biệt là OOM Killer)** và **Network** là 2 chủ đề bạn sẽ gặp lỗi (troubleshoot) nhiều nhất trong thực tế công việc DevOps hàng ngày. Hãy đặc biệt chú ý đến chúng.

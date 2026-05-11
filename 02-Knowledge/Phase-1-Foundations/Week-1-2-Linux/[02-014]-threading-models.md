# Mô hình Đa luồng (Threading Models)

## Document Information
- **Doc ID:** 02-014
- **Summary:** Tìm hiểu sự khác biệt giữa Tiến trình (Process) và Luồng (Thread), ưu điểm của lập trình đa luồng và tính tương thích của thư viện POSIX threads (pthreads).
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Tiến trình và Luồng?**
Khi vận hành một hệ thống server, bạn sẽ liên tục làm việc với các ứng dụng (như Web server, Database). Việc hiểu cách một ứng dụng chạy dưới dạng một hay nhiều tiến trình (process), và bên trong mỗi tiến trình có bao nhiêu luồng (thread) giúp bạn tối ưu hóa hiệu năng, giới hạn tài nguyên CPU/RAM, cũng như dễ dàng đọc hiểu các công cụ giám sát hệ thống như `top`, `htop`, hay `ps`.

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Local Linux VM hoặc AWS EC2 instance.
- **Yêu cầu:** Bất kỳ bản phân phối Linux nào. Các lệnh giám sát tiến trình và luồng trong bài không cần quyền `root`.

## 3. Tiến trình (Process) vs Luồng (Thread)

### 3.1. Tiến trình (Process) là gì?
- **Process** đơn giản là một bản thể (instance) đang chạy của một chương trình máy tính.
- Khi một chương trình được thực thi, nó không chỉ có mã lệnh (code), mà Kernel còn phải cấp phát cho nó một loạt các thông tin đi kèm, bao gồm:
  - Biến môi trường (Environment variables).
  - Vùng nhớ riêng biệt (Attached memory regions).
  - Thư mục hiện tại (Current directory).
  - Danh sách các file đang mở (Open file descriptors).

### 3.2. Luồng (Thread) là gì?
- **Thread** là một đơn vị thực thi mã lệnh nhỏ hơn nằm *bên trong* một tiến trình. Một tiến trình có thể chứa một hoặc nhiều luồng.
- **Đặc điểm cốt lõi:** Khi có nhiều luồng cùng nằm trong một tiến trình, chúng sẽ **chia sẻ (share)** toàn bộ các thông tin của tiến trình đó. Quan trọng nhất là chúng **dùng chung bộ nhớ (share memory)**. Điều này giúp các luồng có thể giao tiếp và trao đổi dữ liệu với nhau cực kỳ nhanh chóng mà không cần thông qua cơ chế IPC (Inter-Process Communication) phức tạp của hệ điều hành.

## 4. Tại sao lại cần ứng dụng Đa luồng (Multi-threading)?

Việc viết ứng dụng hỗ trợ nhiều luồng mang lại những lợi ích to lớn:
- **Xử lý đồng thời cùng một công việc:** Ví dụ điển hình là một Web Server. Khi có 100 client kết nối tới, server không thể phục vụ từng người một theo thứ tự (người sau phải chờ người trước). Thay vào đó, với mỗi client kết nối, server sẽ tạo ra (hoặc cấp) một luồng riêng biệt để xử lý yêu cầu của client đó.
- **Tách biệt các tác vụ (Tasks):** Một ứng dụng có thể phân chia công việc cho nhiều luồng để tránh bị "đơ" (block). Ví dụ: một luồng làm nhiệm vụ liên tục lắng nghe tương tác của người dùng (click chuột, gõ phím), trong khi một luồng khác ở phía dưới âm thầm tải dữ liệu từ mạng hoặc tính toán các tác vụ nặng.

## 5. Thư viện POSIX Threads (pthreads) và Tính di động (Portability)

Để lập trình đa luồng trên hệ điều hành, có rất nhiều cách khác nhau. Tuy nhiên, cách chuẩn mực và mang tính di động (portable) nhất là sử dụng thư viện **POSIX threads (pthreads)**.

### 5.1. Lý thuyết "Viết một lần, chạy mọi nơi"
- Ý tưởng của pthreads là tạo ra một bộ tiêu chuẩn (API) chung cho việc quản lý luồng trên mọi hệ điều hành nhân Unix (như Linux, Solaris, AIX, HP-UX...).
- Nhờ đó, một ứng dụng viết bằng pthreads trên Linux về lý thuyết có thể mang sang biên dịch và chạy trên Solaris mà không cần sửa lại code.

### 5.2. Thực tế về tính di động
Tuy nhiên, "viết một lần, chạy mọi nơi" đôi khi chỉ là lý thuyết. Trong thực tế, vẫn có những hành vi (behavior) không được quy định rõ ràng trong chuẩn POSIX, dẫn đến việc các hệ điều hành xử lý khác nhau.
- **Ví dụ về sự khác biệt:** Việc khởi tạo "mutex" (cơ chế khóa để tránh xung đột giữa các luồng khi dùng chung bộ nhớ). Trên Solaris, nếu lập trình viên quên khởi tạo đúng cách, OS có thể tự động sửa lỗi và giúp chương trình vẫn chạy. Nhưng trên Linux, sự cẩu thả này sẽ ngay lập tức làm chương trình bị sập (crash).
- **Takeaway:** Việc tuân thủ chuẩn pthreads là một điểm xuất phát cực kỳ vững chắc, nhưng trên thực tế, ứng dụng vẫn luôn cần phải được test kỹ lưỡng trên hệ điều hành đích.

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Sử dụng các lệnh giám sát hệ thống để nhìn thấy sự khác biệt giữa Process và Thread của một ứng dụng đang chạy.

**Bước 1:** Dùng lệnh `top` để xem các tiến trình (Processes).
```bash
top
```
*Lệnh này mặc định chỉ hiển thị danh sách các **Tiến trình (Processes)**. Bạn có thể thấy mỗi tiến trình có một `PID` (Process ID).*

**Bước 2:** Bật chế độ hiển thị Luồng (Threads) trong `top`.
- Đang ở trong giao diện lệnh `top`, hãy nhấn phím chữ `H` (viết hoa).
- Màn hình sẽ chuyển sang chế độ hiển thị luồng. Lúc này cột PID sẽ thể hiện là luồng. Bạn sẽ thấy số lượng mục hiển thị tăng lên rất nhiều, vì một tiến trình có thể chứa hàng chục luồng.
- Nhấn `q` để thoát.

**Bước 3:** Sử dụng `ps` để xem các luồng của một tiến trình cụ thể.
Cú pháp `ps -eLf` sẽ hiển thị tất cả các luồng. Cột `PID` là ID của tiến trình, và cột `LWP` (Light Weight Process) chính là ID của luồng.
```bash
ps -eLf | head -n 20
```
*Bạn sẽ nhận thấy có những dòng có cùng một `PID` nhưng `LWP` khác nhau, đó chính là các luồng (threads) khác nhau đang cùng chạy bên trong một tiến trình (process) mẹ.*
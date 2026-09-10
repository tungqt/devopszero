# Roadmap Phase 1: Foundations (Nền tảng cốt lõi)

## Document Information
- **Doc ID:** 02-001
- **Summary:** Lộ trình chi tiết cho Phase 1 - Các kiến thức nền tảng bắt buộc của DevOps.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 🎯 Mục tiêu Phase 1
Nắm vững các công cụ và khái niệm cơ bản nhất của hệ thống máy tính, mạng và quản lý mã nguồn trước khi chạm vào các công cụ DevOps phức tạp (như Docker, CI/CD, Cloud).

## 📚 Chi tiết Lộ trình

### 1. Internet & Networking (Mạng máy tính cơ bản)
- Mô hình mạng: OSI & TCP/IP.
- Cách Internet hoạt động: DNS, HTTP/HTTPS, SSL/TLS.
- Các khái niệm mạng cốt lõi: IP (Public/Private), Port, Subnet, Routing, Firewall cơ bản.
- Các lệnh kiểm tra mạng: `ping`, `curl`, `telnet`, `traceroute`, `netstat`/`ss`.

### 2. Operating System & Linux (Hệ điều hành Linux)
- Kiến trúc Linux cơ bản (Kernel, Shell, File System Hierarchy).
- Quản lý User/Group và Phân quyền (Permissions: `chmod`, `chown`).
- Quản lý tiến trình (Process: `ps`, `top`, `htop`, `kill`).
- Quản lý dịch vụ (Services: `systemd`, `systemctl`).
- SSH và cấu hình xác thực bằng SSH Keys (Key-based authentication).

### 3. Terminal & Bash Scripting (Dòng lệnh & Tự động hóa cơ bản)
- Thao tác file/thư mục: `ls`, `cd`, `cp`, `mv`, `rm`, `mkdir`.
- Công cụ xử lý văn bản (Text manipulation): `cat`, `grep`, `awk`, `sed`, `tail`, `head`.
- Viết Bash script cơ bản: 
  - Khai báo biến (Variables).
  - Câu lệnh điều kiện (`if/else`).
  - Vòng lặp (`for`, `while`).
  - Lấy input từ người dùng và arguments.

### 4. Version Control System (Hệ thống quản lý phiên bản)
- Git cơ bản: `init`, `add`, `commit`, `push`, `pull`, `clone`.
- Quản lý nhánh (Branching): `branch`, `checkout`, `merge`, `rebase`.
- Xử lý xung đột code (Merge conflicts).
- Nền tảng lưu trữ: GitHub / GitLab.
- Các luồng làm việc phổ biến (Git workflows): GitFlow, GitHub Flow.

### 5. Programming Language (Ngôn ngữ lập trình cơ bản - Python & Go)
- **Python (Thế mạnh: Tự động hóa, Scripting, Data):**
  - Cú pháp cơ bản: Biến, kiểu dữ liệu, vòng lặp, câu điều kiện, hàm.
  - Cấu trúc dữ liệu: List, Dictionary, Tuple, Set.
  - Thao tác với file: Đọc/ghi file Text, JSON, YAML.
  - Tương tác mạng: Gửi HTTP requests (sử dụng thư viện `requests` để gọi REST API).
  - Xây dựng ứng dụng: Viết một Web API (Hello World) cơ bản bằng Flask hoặc FastAPI.
- **Go / Golang (Thế mạnh: Hệ thống, Cloud-Native, Hiệu năng cao):**
  - Cú pháp cơ bản: Biến, kiểu dữ liệu tĩnh, vòng lặp (chỉ có `for`), hàm, Struct, Interface.
  - Xử lý đồng thời (Concurrency): Goroutines, Channels cơ bản.
  - Thao tác với hệ thống: Đọc/ghi file, xử lý JSON/YAML.
  - Xây dựng ứng dụng: Viết một Web API (Hello World) bằng thư viện chuẩn `net/http` hoặc framework `Gin`.
- **Mục tiêu chung:** Cả 2 ngôn ngữ đều sẽ được dùng để viết các ứng dụng Web API đơn giản, làm tiền đề thực hành cho việc đóng gói Docker ở Phase 2.

---
**Next Step:** Sau khi hoàn thành Phase 1, chúng ta sẽ bắt đầu tạo các file bài học chi tiết cho từng mục trên trong thư mục `02-Knowledge`.
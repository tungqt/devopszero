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

---
**Next Step:** Sau khi hoàn thành Phase 1, chúng ta sẽ bắt đầu tạo các file bài học chi tiết cho từng mục trên trong thư mục `02-Knowledge`.
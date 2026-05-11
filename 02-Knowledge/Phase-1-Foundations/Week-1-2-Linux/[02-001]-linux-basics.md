# Linux & Terminal - Kiến thức cốt lõi cho DevOps

## Document Information
- **Doc ID:** 02-001
- **Summary:** Tổng hợp các lệnh Linux cơ bản, quản lý file, thư mục và cách sinh tồn trong Terminal.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan về Linux trong DevOps

### 1.1. Tại sao DevOps phải giỏi Linux?
Hơn 90% các máy chủ (servers) và container trên thế giới chạy hệ điều hành Linux. Nếu không hiểu Linux, bạn sẽ không thể:
- SSH vào server để debug lỗi.
- Viết Dockerfile (vì bản chất Docker container là các tiến trình Linux).
- Viết CI/CD pipeline (vì các runner của GitHub Actions/Jenkins đều chạy trên Linux).

### 1.2. Môi trường thực hành (Environment Setup)
Để học và thực hành các lệnh Linux trong tài liệu này, bạn cần hiểu rõ mình đang gõ lệnh ở đâu và tác động lên hệ thống nào.

**A. Môi trường Local (Máy tính cá nhân của bạn):**
- **MacOS (Khuyên dùng):** Vì MacOS dựa trên nền tảng Unix, nó có sẵn Terminal và hỗ trợ 95% các lệnh Linux cơ bản. Bạn chỉ cần mở **Terminal** tích hợp sẵn trong IDE Cursor (hoặc app Terminal của Mac) là có thể thực hành ngay lập tức mà không cần cài thêm gì.
- **Windows:** Cần cài đặt WSL (Windows Subsystem for Linux) hoặc Git Bash để giả lập môi trường Linux.

**B. Môi trường Server (Thực tế công việc):**
Trong thực tế, DevOps Engineer không chạy lệnh trên máy cá nhân để phục vụ user. Thay vào đó, quy trình làm việc chuẩn là:
1. Mở Terminal trên máy cá nhân (Mac/Windows).
2. Dùng giao thức SSH (Secure Shell) để kết nối từ xa vào một máy chủ Linux (đặt tại AWS, Azure, hoặc Data Center). Lệnh ví dụ: `ssh ubuntu@192.168.1.100`.
3. Lúc này, cửa sổ Terminal của bạn trở thành màn hình điều khiển của máy chủ đó. Mọi lệnh bạn gõ (như `ls`, `rm`) sẽ thực thi trực tiếp trên máy chủ.

*💡 **Lưu ý cho bài học này:** Bạn hãy mở Terminal trong Cursor và thực hành trực tiếp trên máy Mac của bạn.*

### 1.3. Tài nguyên học tập (Recommended Courses)
Nếu bạn muốn học qua Video/Khóa học bài bản thay vì chỉ đọc tài liệu, dưới đây là các nguồn tốt nhất (từ miễn phí đến trả phí):

**Miễn phí (YouTube):**
- [Linux for Hackers (and DevOps) - NetworkChuck](https://www.youtube.com/watch?v=VbEx7B_PTOE): Cực kỳ dễ hiểu, phong cách dạy cuốn hút, tập trung đúng vào những gì DevOps cần.
- [Linux Crash Course for Beginners - freeCodeCamp](https://www.youtube.com/watch?v=sWbUDq4S6Y8): Dài 2.5 tiếng, bao phủ toàn bộ lệnh cơ bản.

**Trả phí (Chuyên sâu):**
- **[Linux for Developers - Coursera (The Linux Foundation)](https://www.coursera.org/learn/linux-for-developers):** Đây là khóa học **rất khuyến nghị** dành cho người mới. Khóa học này do chính The Linux Foundation (tổ chức duy trì Linux) cung cấp. Nó tập trung vào góc nhìn của Developer/DevOps (cách dùng Command Line, Text Editors, System Monitoring, User Management) thay vì đi quá sâu vào System Admin cũ kỹ.
- **KodeKloud - Linux Basics Course:** Rất tốt vì họ có sẵn môi trường Terminal ảo (Lab) ngay trên trình duyệt để bạn thực hành mà không sợ làm hỏng máy.

---

## 2. Các lệnh điều hướng và quản lý File/Thư mục cơ bản

### 2.1. Điều hướng (Navigation)
- `pwd` (Print Working Directory): Xem đường dẫn thư mục hiện tại.
- `ls` (List): Liệt kê các file và thư mục.
  - `ls -l`: Hiển thị chi tiết (quyền, chủ sở hữu, kích thước).
  - `ls -a`: Hiển thị cả các file ẩn (file bắt đầu bằng dấu `.`, ví dụ `.env`).
  - `ls -la`: Kết hợp cả hai.
- `cd` (Change Directory): Di chuyển giữa các thư mục.
  - `cd /var/log`: Di chuyển đến thư mục tuyệt đối.
  - `cd ..`: Lùi lại 1 cấp.
  - `cd ~` hoặc `cd`: Về thư mục Home của user hiện tại.
  - `cd -`: Quay lại thư mục vừa đứng trước đó.

### 2.2. Thao tác với File và Thư mục
- `mkdir <tên_thư_mục>`: Tạo thư mục mới (`mkdir -p a/b/c` để tạo thư mục lồng nhau).
- `touch <tên_file>`: Tạo một file trống.
- `cp <nguồn> <đích>` (Copy): Copy file. Dùng `cp -r` để copy thư mục.
- `mv <nguồn> <đích>` (Move): Di chuyển file hoặc đổi tên file.
- `rm <tên_file>` (Remove): Xóa file.
  - **CẢNH BÁO:** `rm -rf <thư_mục>`: Xóa thư mục và toàn bộ nội dung bên trong mà không hỏi lại. Dùng cực kỳ cẩn thận!

## 3. Xem và thao tác với nội dung File (Text Manipulation)
DevOps engineer dành rất nhiều thời gian để đọc log file. Đây là các lệnh bắt buộc phải thuộc:

- `cat <tên_file>`: In toàn bộ nội dung file ra màn hình (chỉ dùng cho file ngắn).
- `less <tên_file>`: Xem file dài, có thể cuộn lên/xuống (nhấn `q` để thoát).
- `head -n 10 <tên_file>`: Xem 10 dòng đầu tiên của file.
- `tail -n 10 <tên_file>`: Xem 10 dòng cuối cùng của file.
  - **Lệnh thần thánh:** `tail -f /var/log/syslog`: Xem log realtime (log cứ sinh ra là in lên màn hình ngay lập tức).

## 4. Tìm kiếm (Search & Filter)
- `grep "từ_khóa" <tên_file>`: Tìm kiếm một chuỗi ký tự trong file.
  - `grep -i "error" app.log`: Tìm chữ "error" (không phân biệt hoa thường).
  - `grep -r "TODO" .`: Tìm chữ "TODO" trong tất cả các file ở thư mục hiện tại.
- `find <đường_dẫn> -name "tên_file"`: Tìm kiếm file trong hệ thống.
  - `find . -name "*.log"`: Tìm tất cả các file có đuôi `.log` ở thư mục hiện tại.

## 5. Pipe (`|`) và Redirection (`>`, `>>`)
Đây là sức mạnh thực sự của Linux Terminal: kết hợp các lệnh nhỏ thành một luồng xử lý lớn.

- `>` (Ghi đè): In output vào một file (xóa nội dung cũ).
  - `echo "Hello" > test.txt`
- `>>` (Ghi tiếp): In output vào cuối file (không xóa nội dung cũ).
  - `echo "World" >> test.txt`
- `|` (Pipe): Lấy output của lệnh đằng trước làm input cho lệnh đằng sau.
  - `cat app.log | grep "ERROR" | tail -n 5`: Đọc file log -> Lọc ra các dòng có chữ ERROR -> Chỉ lấy 5 dòng cuối cùng.

## 6. Bài tập thực hành (Lab 1)
1. Tạo một thư mục tên là `devops_lab`.
2. Di chuyển vào thư mục đó, tạo một file tên là `server.log`.
3. Dùng lệnh `echo` và `>>` để thêm 3 dòng chữ vào file đó (trong đó có 1 dòng chứa từ "ERROR").
4. Dùng lệnh `grep` để tìm và in ra dòng có chữ "ERROR" đó.
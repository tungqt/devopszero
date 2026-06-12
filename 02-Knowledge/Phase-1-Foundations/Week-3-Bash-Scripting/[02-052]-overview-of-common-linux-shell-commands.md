# Tổng quan các lệnh shell Linux thường dùng (Overview of Common Linux Shell Commands)

## Document Information
- **Doc ID:** 02-052
- **Summary:** Bài học định nghĩa **shell**; shell mặc định thường là **Bash**; kiểm tra bằng **`printenv SHELL`**; các ứng dụng của lệnh shell (thông tin, file/thư mục, in nội dung, nén/lưu trữ, mạng, giám sát, batch); bảng lệnh thường gặp theo nhóm; gợi ý chạy Linux trên Windows (dual-boot, VM, Cygwin, WSL).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Tổng quan các lệnh shell Linux thường dùng". Sau khi xem video này, bạn sẽ có thể:

*   **Định nghĩa** shell là gì.
*   **Liệt kê** các ứng dụng của lệnh shell.
*   **Nhớ lại** các lệnh shell thường gặp.

## Shell là gì?

**Shell** là giao diện dòng lệnh mạnh cho các hệ điều hành kiểu Unix. Shell có thể:

*   **Diễn giải lệnh** và **chạy các chương trình khác**.
*   Cho phép truy cập **tập tin**, **tiện ích** và **ứng dụng**.
*   Đồng thời là một **ngôn ngữ tương tác** (interactive language).
*   Cũng là **ngôn ngữ kịch bản (scripting)** — dùng để **tự động hóa** tác vụ.

## Shell mặc định và Bash

Trên nhiều hệ Linux, shell mặc định thường là **Bash**. Một số shell khác được nhắc trong khóa học:

*   **sh** — Bourne shell  
*   **ksh** — Korn shell  
*   **tcsh** — TC shell  
*   **zsh** — Z shell  
*   **fish** — fish  

Trong khóa học này chỉ dùng **Bash** (**B**ourne **A**gain **SH**ell).

Để xem shell mặc định hiện tại:

```bash
printenv SHELL
```

Kết quả thường là **đường dẫn** tới chương trình shell (ví dụ trỏ tới `bash`).

Nếu shell mặc định **không** phải Bash, bạn vẫn có thể chuyển sang Bash tạm thời bằng cách gõ:

```bash
bash
```

**Quy ước trong khóa học:** dùng ký hiệu **`$`** để biểu thị **dòng nhắc lệnh (prompt)**. Bên ngoài khóa học, đôi khi bạn sẽ thấy ký hiệu **`>`** được dùng cho cùng mục đích.

## Ứng dụng của lệnh shell

Lệnh shell thường dùng để:

*   **Lấy thông tin** về hệ thống và người dùng  
*   **Điều hướng** và **làm việc** với tập tin và thư mục  
*   **In nội dung** tập tin hoặc chuỗi  
*   **Nén và lưu trữ (archive)** tập tin  
*   **Thao tác mạng**  
*   **Giám sát** hiệu năng và trạng thái hệ thống, thành phần và ứng dụng  
*   **Chạy batch** — ví dụ các tác vụ kiểu **ETL**

## Lệnh thường dùng: lấy thông tin

| Lệnh | Mô tả ngắn |
|------|------------|
| `whoami` | In **tên người dùng** hiện tại |
| `id` | Hiển thị **ID người dùng** và **nhóm** |
| `uname` | Tên hệ điều hành / thông tin kernel |
| `ps` | Liệt kê **tiến trình** đang chạy và **PID** |
| `top` | Tiến trình đang chạy và **dùng tài nguyên** (CPU, bộ nhớ, I/O, …) |
| `df` | Thông tin về **filesystem** đã gắn (mount) |
| `man` | Mở **trang hướng dẫn** cho lệnh shell |
| `date` | In **ngày/giờ** hiện tại |

## Lệnh thường dùng: tập tin

| Lệnh | Mô tả ngắn |
|------|------------|
| `cp` | **Sao chép** tập tin |
| `mv` | **Đổi tên** hoặc **di chuyển** (đổi đường dẫn) |
| `rm` | **Xóa** tập tin |
| `touch` | Tạo tập tin **rỗng** hoặc **cập nhật timestamp** |
| `chmod` | **Thay đổi quyền** tập tin *(trong transcript tiếng Anh đôi khi đọc nhầm thành “changemod”; lệnh đúng là **`chmod`**)* |
| `wc` | Đếm **dòng**, **từ**, **ký tự** trong tập tin |
| `grep` | In các **dòng khớp mẫu** trong tập tin |

## Lệnh thường dùng: thư mục và điều hướng

| Lệnh | Mô tả ngắn |
|------|------------|
| `ls` | **Liệt kê** tập tin và thư mục trong thư mục hiện tại |
| `find` | **Tìm** tập tin theo mẫu trong **cây thư mục** |
| `pwd` | In **thư mục làm việc hiện tại** (*present working directory*) |
| `mkdir` | **Tạo** thư mục mới |
| `cd` | **Chuyển** sang thư mục khác |
| `rmdir` | **Xóa thư mục rỗng** *(chỉ thư mục **trống**; để xóa cây thư mục có nội dung cần cẩn thận với lệnh khác, ví dụ `rm -r` — rủi ro cao nếu dùng sai)* |

## Lệnh thường dùng: in nội dung tập tin hoặc chuỗi

| Lệnh | Mô tả ngắn |
|------|------------|
| `cat` | In **toàn bộ** nội dung tập tin |
| `more` | In nội dung **từng trang** |
| `head` | In **N dòng đầu** của tập tin |
| `tail` | In **N dòng cuối** của tập tin |
| `echo` | In **chuỗi** đầu vào; có thể in **giá trị biến** |

## Lệnh thường dùng: nén và lưu trữ

| Lệnh | Mô tả ngắn |
|------|------------|
| `tar` | **Đóng gói (archive)** một tập tập tin |
| `zip` | **Nén** một tập tập tin |
| `unzip` | **Giải nén** / trích xuất từ file nén kiểu zip |

## Lệnh thường dùng: mạng

| Lệnh | Mô tả ngắn |
|------|------------|
| `hostname` | In **tên máy (hostname)** |
| `ping` | Gửi gói tới URL/host và in phản hồi |
| `ifconfig` | Hiển thị hoặc cấu hình **giao diện mạng** *(trên Linux hiện đại, lệnh **`ip`** thường được ưu tiên hơn)* |
| `curl` | Lấy / hiển thị nội dung từ **URL** |
| `wget` | **Tải xuống** tập tin từ URL |

## Chạy Linux trên máy Windows (theo video)

Nếu bạn dùng Windows và muốn chạy Linux, có thể:

*   Cài Linux trên **phân vùng ổ đĩa riêng** (dual-boot) — chuyển OS thường cần **khởi động lại**.
*   Cài Linux trên **máy ảo (VM)**.
*   Dùng **mô phỏng/emulator** kiểu **Cygwin**.
*   Dùng **WSL** (Windows Subsystem for Linux) — lớp tương thích để chạy **binary Linux** trên Windows.

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   **Shell** là giao diện dòng lệnh để chạy lệnh, đồng thời là **ngôn ngữ tương tác** và **ngôn ngữ script**.
*   Lệnh shell dùng để **điều hướng** và **làm việc** với tập tin và thư mục.
*   Có nhóm lệnh phục vụ **nén và lưu trữ** tập tin.
*   **`curl`** và **`wget`** lần lượt thường dùng để **xem/lấy nội dung** và **tải tập tin** từ URL.
*   **`echo`** in chuỗi hoặc giá trị biến; **`cat`** và **`tail`** dùng để **hiển thị nội dung** tập tin (toàn bộ hoặc phần cuối).

# Cơ bản kịch bản shell (Shell Scripting Basics)

## Document Information
- **Doc ID:** 02-064
- **Summary:** Định nghĩa **script** và **shell script**; use case tự động hóa; **shebang** (`#!/bin/bash`, `#!/usr/bin/env python3`); tạo **`hello_world.sh`** với `touch`, `echo`, `>>`; **`chmod +x`**; chạy **`./hello_world.sh`**; so sánh ngôn ngữ thông dịch vs biên dịch.
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Cơ bản kịch bản shell". Sau khi xem video này, bạn sẽ có thể:

*   **Mô tả** script là gì.
*   **Liệt kê** các trường hợp dùng scripting.
*   **Giải thích** chỉ thị trình thông dịch **shebang**.
*   **Tạo** và **chạy** shell script đơn giản **`hello_world`**.

## Script là gì?

**Script** là danh sách lệnh được **một chương trình ngôn ngữ kịch bản (scripting language)** diễn giải và chạy.

Lệnh có thể:

*   Gõ **tương tác** trên dòng lệnh, hoặc
*   Ghi **từng dòng** trong file văn bản.

Ngôn ngữ kịch bản **thường không biên dịch** trước — chúng được **thông dịch lúc chạy** (*interpreted at runtime*).

| | Scripting | Biên dịch (compiled) |
|---|-----------|----------------------|
| Tốc độ chạy | Thường **chậm hơn** | Thường **nhanh hơn** |
| Thời gian phát triển | **Dễ và nhanh** hơn | Thường **lâu hơn** |

## Trường hợp dùng scripting

Script được dùng **rộng rãi** để **tự động hóa**, ví dụ:

*   Job **ETL** (trích – biến đổi – nạp dữ liệu)
*   **Sao lưu** và **lưu trữ** file
*   Tác vụ **quản trị hệ thống** chung

Script còn áp dụng cho hầu hết tác vụ tính toán, gồm **tích hợp ứng dụng**, **plugin**, và phát triển **ứng dụng web**.

## Shell script và shebang

**Shell script** là file văn bản **có thể thực thi**, dòng đầu thường là **chỉ thị trình thông dịch** (*interpreter directive*) — còn gọi là **shebang** (*hash-bang*: `#` + `!`).

**Dạng chung:**

```text
#!interpreter [optional-argument]
```

| Thành phần | Ý nghĩa |
|------------|--------|
| **`interpreter`** | Đường dẫn **tuyệt đối** tới chương trình thực thi |
| **`optional-argument`** | Một đối số (chuỗi) truyền cho interpreter |

**Shell script** là script gọi chương trình **shell**. Ví dụ shebang:

```bash
#!/bin/sh      # Bourne shell (hoặc tương thích) trong thư mục bin
#!/bin/bash    # Bash shell
```

Shebang **không chỉ** dành cho shell — ví dụ script Python:

```bash
#!/usr/bin/env python3
```

Dùng `env` giúp tìm `python3` trên **`PATH`** thay vì hard-code một đường dẫn cố định.

*(Quyền `rwx` và `chmod`: xem `[02-057]`.)*

## Ví dụ: `hello_world.sh`

### 1. Tạo file script

```bash
touch hello_world.sh
```

Hậu tố **`.sh`** là **quy ước** (không bắt buộc về mặt kernel) để báo đây là shell script.

### 2. Ghi shebang và lệnh in ra màn hình

Dùng **`echo`** và toán tử **chuyển hướng ghi nối** `>>` để **thêm** nội dung vào cuối file (không ghi đè):

```bash
echo '#!/bin/bash' >> hello_world.sh
echo 'echo Hello World' >> hello_world.sh
```

*   Dòng 1: kernel/shell biết dùng **Bash** để chạy file.
*   Dòng 2: lệnh **`echo`** in chuỗi `Hello World` khi script chạy.

### 3. Kiểm tra quyền: `ls -l`

```bash
ls -l hello_world.sh
```

Ví dụ quyền ban đầu có thể là `-rw-r--r--`:

*   **R**, **W**: đọc và ghi được.
*   **Không có X**: **chưa thực thi** được.

Ba nhóm quyền: **owner** (bạn), **group**, **other** (mọi user).

### 4. Cho phép thực thi: `chmod +x`

```bash
chmod +x hello_world.sh
ls -l hello_world.sh
```

Sau `chmod +x`, cột quyền hiện **x** cho owner / group / other — file **executable** với mọi user trên máy (trong ví dụ khóa học).

### 5. Chạy script

```bash
./hello_world.sh
```

*   **`./`** — chạy file trong **thư mục hiện tại** (shell không tự tìm file trong `.` trừ khi bạn chỉ rõ).
*   Kết quả: dòng chữ **`Hello World`** trên terminal.

## Tóm tắt bài học

*   **Shell script** = file văn bản thực thi, thường bắt đầu bằng **shebang**.
*   Script có thể **chạy lệnh shell** và **gọi chương trình khác**.
*   Ngôn ngữ kịch bản **thông dịch lúc chạy**; ngôn ngữ biên dịch thường **nhanh hơn** nhưng **tốn thời gian phát triển** hơn.

## Ghi chú DevOps

*   Shebang **`#!/bin/bash`** vs **`#!/bin/sh`**: trên nhiều distro, `/bin/sh` trỏ tới dash hoặc bash ở chế độ POSIX — script production nên chọn shebang **khớp** shell bạn test (thường `bash` cho khóa học DevOps).
*   Luôn **`chmod +x`** (hoặc gọi `bash script.sh`) trước khi đưa script vào cron, CI, hoặc Ansible.
*   Mẫu `touch` + `echo >>` trong lab là cách **nhanh** tạo file; thực tế dùng editor (`vim`, `nano`) hoặc SCM để quản lý script có review.

# Bảo mật — Quản lý quyền và chủ sở hữu tập tin (Security - Managing File Permissions and Ownership)

## Document Information
- **Doc ID:** 02-057
- **Summary:** Bài đọc về **quyền và chủ sở hữu** trên Linux (user, group, other); **`ls -l`** đọc chuỗi `rwx`; quyền **thư mục** vs **tập tin**; **`chmod go-r`** để làm file **riêng tư**; giới thiệu **file thực thi** và **shell script** (shebang, `+x`).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập

Sau khi đọc xong, bạn sẽ có thể:

*   **Giải thích** chủ sở hữu và quyền trên tập tin.
*   **Xem** quyền của tập tin và thư mục.
*   **Đặt** một tập tin ở chế độ **riêng tư** (chỉ owner đọc/ghi).

## Vì sao cần quyền và chủ sở hữu?

**Linux** là hệ điều hành **đa người dùng** (*multi-user*). Mặc định, người dùng khác trên cùng máy **có thể xem** nhiều tập tin bạn lưu trên hệ thống.

Bạn có thể có tài liệu **nhạy cảm** — ví dụ khai thuế cá nhân, tài liệu **sở hữu trí tuệ** của công ty — cần **bảo vệ** khỏi bị người khác **xem** hoặc **sửa**. **Quyền** và **chủ sở hữu** tập tin là cơ chế chính để kiểm soát điều đó.

## Chủ sở hữu và quyền tập tin

Trên Linux có **ba cấp** “ai” liên quan tới một tập tin:

| Cấp | Ý nghĩa |
|-----|--------|
| **user** (u) | Người dùng **sở hữu** tập tin — thường là người **tạo** file lúc tạo. |
| **group** (g) | **Nhóm** người dùng có thể được chia sẻ quyền trên file. |
| **other** (o) | **Mọi người còn lại** có quyền truy cập máy — gán quyền cho mức này cần **rất cẩn thận**. |

Chỉ **chủ sở hữu chính thức** của file mới được **đổi quyền** — tức chỉ owner quyết định **ai được đọc**, **ghi**, hoặc **thực thi**.

Ba loại quyền (áp dụng cho user / group / other):

| Ký hiệu | Ý nghĩa (tập tin) |
|--------|-------------------|
| **r** | **Đọc** (*read*) |
| **w** | **Ghi** (*write*) |
| **x** | **Thực thi** (*execute*) |

## Xem quyền: `ls -l`

Ví dụ tạo file và xem quyền mặc định:

```bash
echo "Who can read this file?" > my_new_file
more my_new_file
ls -l my_new_file
```

Đầu ra `ls -l` có thể giống:

```text
-rw-r--r-- 1 theia users 25 Dec 22 17:47 my_new_file
```

*(Trong transcript mẫu có tên file `x` ở cuối dòng — khi thực hành dùng đúng tên `my_new_file`.)*

Chuỗi quyền **`rw-r--r--`** (bỏ ký tự đầu `-`):

| Ba ký tự | Đối tượng | Ý nghĩa trong ví dụ |
|----------|-----------|---------------------|
| **rw-** | **user** (bạn) | Đọc + ghi; **không** thực thi (không có `x`). |
| **r--** | **group** | Chỉ **đọc**. |
| **r--** | **other** | Chỉ **đọc**. |

**Ký tự đầu dòng:**

*   **`-`** ở đầu: đối tượng là **tập tin**.
*   **`d`** ở đầu: đối tượng là **thư mục** (*directory*).

Tóm lại từ **`rw-r--r--`**: **mọi người có thể đọc**, **không ai thực thi** (vì không có `x`), và **chỉ bạn (user)** được **ghi**.

## Quyền trên thư mục

Thư mục dùng cùng định dạng **rwx**, nhưng **ý nghĩa** hơi khác so với tập tin:

| Quyền | Hành động được phép (thư mục) |
|--------|-------------------------------|
| **r** | **Liệt kê** nội dung thư mục (ví dụ `ls`). |
| **w** | **Thêm / xóa** tập tin hoặc thư mục con trong thư mục đó. |
| **x** | **Vào** thư mục (ví dụ `cd`). |

Đặt quyền thư mục **hợp lý** là **thực hành tốt** vì **bảo mật** và **ổn định**; khóa học sẽ đi sâu thêm lý do khác về quyền/ownership sau.

## Làm tập tin riêng tư: `chmod`

Thu hồi quyền **đọc** của **group** và **other**:

```bash
chmod go-r my_new_file
ls -l my_new_file
```

Ví dụ kết quả:

```text
-rw------- 1 theia users 24 Dec 22 18:49 my_new_file
```

*   **`go-r`**: với **group (g)** và **other (o)**, **bỏ** quyền **read (r)**.
*   Chuỗi **`rw-------`**: chỉ **user** còn đọc/ghi; group và other **không** đọc được.

**`chmod`** dùng được cho cả **tập tin** và **thư mục**.

## Tập tin thực thi — nhìn trước (looking ahead)

**Thực thi** (*execute*) trên Linux với file “chương trình” nghĩa là file chứa lệnh mà hệ có thể **chạy trực tiếp** — file **sẵn sàng chạy** (*ready-to-run*), còn gọi **binary** hoặc **executable**.

Trong khóa học bạn sẽ làm quen **script** — chương trình viết bằng **ngôn ngữ kịch bản**, đặc biệt **Bash scripting** (script viết trong **Bash**). **Shell script** là **file văn bản thuần** mà shell **diễn giải** được.

Để một file văn bản được coi là **shell script có thể chạy** cho một user, cần **hai điều**:

1. **Quyền thực thi (x)** cho user đó — ví dụ `chmod +x` (đã gặp ở bài quản lý file).
2. **Shebang** ở **dòng đầu** — khai báo với hệ điều hành shell nào sẽ chạy file (ví dụ `#!/bin/bash`).

Chi tiết sẽ rõ hơn khi học phần **shell scripting**.

## Tổng kết (Summary)

Trong bài đọc này, bạn đã học:

*   Ba mức **ownership** trên Linux: **user**, **group**, **other** — quyết định **ai** được đọc, ghi, thực thi.
*   Dùng **`ls -l`** để **xem** quyền tập tin và thư mục (chuỗi `rwx`, ký tự `-` / `d` đầu dòng).
*   Dùng **`chmod`** để **đổi quyền** — ví dụ **`chmod go-r`** để chỉ owner đọc/ghi file nhạy cảm.

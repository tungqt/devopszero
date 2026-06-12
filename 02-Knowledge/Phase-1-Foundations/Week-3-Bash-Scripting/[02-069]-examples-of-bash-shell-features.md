# Ví dụ tính năng Bash shell (Examples of Bash Shell Features)

## Document Information
- **Doc ID:** 02-069
- **Summary:** Bài đọc thực hành — bảng **metacharacters** (`#`, `;`, `*`, `?`); **quoting** (`\`, `"`, `'`); **I/O redirection** (`>`, `>>`, `2>`, `2>>`, `<`); **command substitution** (`$(pwd)`, `cd $here`); **command-line arguments** (`./script.sh arg1 arg2`).
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập

Sau khi đọc xong, bạn sẽ có thể:

*   **Liệt kê** ví dụ metacharacters.
*   **Dùng** quoting để chỉ định nghĩa literal hoặc đặc biệt.
*   **Áp dụng** input/output redirection.
*   **Áp dụng** command substitution.
*   **Mô tả** ứng dụng của command-line arguments.

*(Lý thuyết video: `[02-068]`.)*

## Metacharacters

**Metacharacters** là ký tự shell **diễn giải như chỉ lệnh**, không phải ký tự văn bản thuần.

| Metacharacter | Ý nghĩa |
|---------------|---------|
| **`#`** | Bắt đầu **comment** |
| **`;`** | **Tách** lệnh trên cùng một dòng |
| **`*`** | Wildcard **filename expansion** — không hoặc nhiều ký tự |
| **`?`** | Wildcard **một** ký tự trong tên file |

### Pound `#`

Dùng trong script hoặc file cấu hình — mọi text **sau `#` trên cùng dòng** bị shell **bỏ qua**:

```bash
#!/bin/bash
# This is a comment
echo "Hello, world!"  # This is another comment
```

Comment giúp **tài liệu hóa** code/config, giải thích mục đích cho người đọc sau — **best practice** khi logic không hiển nhiên.

### Semicolon `;`

Nhiều lệnh trên **một dòng**, chạy **tuần tự** theo thứ tự:

```bash
echo "Hello, "; echo "world!"
```

```text
Hello,
world!
```

Hữu ích khi cần chạy chuỗi lệnh ngắn **không** viết script riêng.

### Asterisk `*`

Wildcard: **bất kỳ chuỗi ký tự nào** (kể cả rỗng):

```bash
ls *.txt
```

Liệt kê mọi file trong thư mục hiện tại có đuôi **`.txt`**.

### Question mark `?`

Wildcard: **đúng một** ký tự:

```bash
ls file?.txt
```

Khớp tên dạng `file` + **một ký tự** + `.txt` (ví dụ `file1.txt`, `fileA.txt`).

## Quoting

**Quoting** bỏ hoặc điều chỉnh **ý nghĩa đặc biệt** của ký tự, khoảng trắng, metacharacters trong đối số lệnh hoặc script.

| Ký hiệu | Ý nghĩa |
|---------|---------|
| **`\`** | Escape — giữ literal một ký tự (hoặc space) |
| **`"..."`** | Literal hầu hết; **metacharacters** vẫn có hiệu lực |
| **`'...'`** | **Mọi** ký tự bên trong là literal |

### Backslash `\`

Ví dụ tên file **có khoảng trắng**:

```bash
touch file\ with\ space.txt
```

Mỗi `\ ` báo shell coi space là **một phần tên file**, không tách đối số.

### Double quotes `" "`

```bash
echo "Hello $USER"
```

```text
Hello <username>
```

`$USER` được **mở rộng** (biến môi trường).

### Single quotes `' '`

```bash
echo 'Hello $USER'
```

```text
Hello $USER
```

**Không** expansion — in đúng chuỗi `$USER`.

## Input/Output redirection

| Ký hiệu | Ý nghĩa |
|---------|---------|
| **`>`** | Redirect **stdout** → file; **ghi đè** |
| **`>>`** | Redirect stdout → file; **append** |
| **`2>`** | Redirect **stderr** → file; ghi đè |
| **`2>>`** | Redirect stderr → file; append |
| **`<`** | Lấy nội dung file làm **stdin** |

Mặc định: chương trình đọc **stdin** (bàn phím), ghi **stdout** (terminal). Redirection chuyển luồng sang **file** hoặc lệnh khác.

### Redirect output `>`

```bash
ls > files.txt
```

*   Tạo `files.txt` nếu chưa có.
*   Ghi kết quả `ls` vào file.

**Cảnh báo:** file đã tồn tại → nội dung cũ bị **ghi đè hoàn toàn**.

### Append output `>>`

```bash
ls >> files.txt
```

Nối output `ls` vào **cuối** `files.txt`, **giữ** nội dung cũ.

### Redirect stderr `2>`

```bash
ls non-existent-directory 2> error.txt
```

Tạo `error.txt` (nếu cần) và ghi **thông báo lỗi** vào đó.

**Cảnh báo:** `2>` cũng **ghi đè** file lỗi nếu file đã có.

### Append stderr `2>>`

```bash
ls non-existent-directory 2>> error.txt
```

Nối stderr vào cuối `error.txt` **không** xóa log cũ.

### Redirect input `<`

```bash
sort < data.txt
```

`sort` đọc từ **`data.txt`** thay vì bàn phím.

## Command substitution

Chạy lệnh và dùng **đầu ra** làm phần của lệnh/biến khác.

Hai cú pháp **tương đương**:

```bash
`command`      # backticks
$(command)     # khuyến nghị
```

### Ví dụ: quay lại thư mục ban đầu

```bash
here=$(pwd)
cd path_to_some_other_directory
cd $here
```

| Bước | Tác dụng |
|------|----------|
| `here=$(pwd)` | Lưu đường dẫn hiện tại vào biến |
| `cd ...` | Chuyển đi nơi khác |
| `cd $here` | Trở về thư mục đã lưu |

Hữu ích cho script deploy/backup cần **tạm rời** rồi **restore** working directory.

## Command-line arguments

**Đối số dòng lệnh** là input bổ sung truyền khi chạy chương trình từ CLI — đặt **sau tên chương trình**.

Dùng để:

*   **Đổi hành vi** chương trình / script
*   Cung cấp **dữ liệu đầu vào**
*   Chỉ định **vị trí output**

Ví dụ Bash script:

```bash
./MyBashScript.sh arg1 arg2
```

Trong script: **`$1`** = `arg1`, **`$2`** = `arg2` (và `$0` = tên script).

## So sánh nhanh: video vs reading

| Chủ đề | `[02-068]` (video) | `[02-069]` (reading) |
|--------|-------------------|----------------------|
| Glob | `/bin/ba*`, `/bin/b?sh` | `*.txt`, `file?.txt` |
| Quoting | `\$1`, `"$1"`, `'$1'` | `file\ with\ space`, `$USER` |
| Redirection | `eg.text`, `err.txt` | `files.txt`, `error.txt`, `sort < data.txt` |
| Substitution | `here=$(pwd)` | `here` + `cd $here` quay lại |

## Tóm tắt

*   **Metacharacters** `#`, `;`, `*`, `?` — shell hiểu theo nghĩa đặc biệt.
*   **Quoting** — kiểm soát literal vs expansion.
*   **I/O redirection** — chuyển stdin/stdout/stderr sang file.
*   **Command substitution** — output lệnh → đối số/biến lệnh khác.
*   **Command-line arguments** — truyền thông tin vào script khi gọi.

## Ghi chú DevOps

*   Script production: ưu tiên **`"$1"`** (quote đối số) để tránh word-splitting và glob ngoài ý muốn.
*   Log rotation: stdout **`>>`**, stderr **`2>>`** vào file riêng; tránh chỉ `2>` ghi đè log lỗi cũ.
*   **`set -euo pipefail`** (bài sau) kết hợp redirection giúp pipeline CI fail sớm khi lỗi.

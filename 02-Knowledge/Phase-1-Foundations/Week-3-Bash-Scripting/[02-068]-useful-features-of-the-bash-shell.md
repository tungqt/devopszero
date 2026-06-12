# Tính năng hữu ích của Bash shell (Useful Features of the Bash Shell)

## Document Information
- **Doc ID:** 02-068
- **Summary:** **Metacharacters** (`#`, `;`, `*`, `?`); **quoting** (`\`, `"`, `'`); **I/O redirection** (`>`, `>>`, `2>`, `2>>`, `<`); **command substitution** (`$(...)`, backticks); **command-line arguments**; chế độ **batch** vs **concurrent** (`;`, `&`).
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Tính năng hữu ích của Bash shell". Sau khi xem video này, bạn sẽ có thể:

*   **Tóm tắt** một số tính năng kịch bản Bash quan trọng.
*   **Nhận biết** ký tự và ký hiệu trong các tính năng đó.

## Metacharacters

**Metacharacters** là ký tự đặc biệt có **ý nghĩa riêng** với shell.

| Ký tự | Ý nghĩa | Ví dụ |
|-------|---------|--------|
| **`#`** | **Comment** — shell bỏ qua phần còn lại của dòng | `# đây là comment` → không in gì |
| **`;`** | **Tách** nhiều lệnh trên **cùng một dòng** | `cmd1; cmd2` — chạy tuần tự, mỗi lệnh một dòng output |
| **`*`** | Glob: **không hoặc nhiều** ký tự liên tiếp trong tên file | `ls /bin/ba*` → ví dụ `bash` (tên bắt đầu `ba`, sau đó bất kỳ ký tự nào) |
| **`?`** | Glob: **đúng một** ký tự | `ls /bin/b?sh` → khớp `bash`, `dash` (một ký tự thay `?`) |

## Quoting (nháy và escape)

**Quoting** quyết định shell có **diễn giải** ký tự đặc biệt hay **coi là ký tự thường** (*escape*).

### Backslash `\`

Escape **một** ký tự — không coi là metacharacter:

```bash
echo \$1 each
```

Kết quả literal: `$1 each` (không thay bằng giá trị tham số `$1`).

### Double quotes `"..."`

Giữ **hầu hết** nội dung literal, **trừ** metacharacters bên trong (vẫn được shell xử lý):

```bash
echo "$1 each"
```

Nếu `$1` **rỗng** (chưa set tham số) → chỉ in khoảng trắng trước `each`: ` each`.

### Single quotes `'...'`

**Mọi** ký tự bên trong là **literal** — không expansion biến, không glob:

```bash
echo '$1 each'
```

Kết quả: `$1 each` (giống ví dụ dùng `\$`).

## I/O redirection

**I/O redirection** chuyển hướng **stdin** (thường bàn phím) hoặc **stdout** / **stderr** (thường terminal).

| Ký hiệu | Tác dụng |
|---------|----------|
| **`>`** | Ghi **stdout** vào file; **tạo** file nếu chưa có; **ghi đè** nếu đã có |
| **`>>`** | **Nối** (append) stdout vào cuối file |
| **`2>`** | Ghi **stderr** vào file |
| **`2>>`** | Append **stderr** vào file (ví dụ log lỗi) |
| **`<`** | Lấy **nội dung file** làm **stdin** cho lệnh |

*(Append stdout trong lab `hello_world`: `[02-064]`.)*

### Ví dụ: tạo file, ghi đè, append, bắt lỗi

Tạo file và ghi dòng đầu:

```bash
echo line1 > eg.text
cat eg.text
```

```text
line1
```

Thêm dòng (append):

```bash
echo line2 >> eg.text
cat eg.text
```

```text
line1
line2
```

Lệnh sai — stderr:

```bash
garbage
```

Bắt thông báo lỗi vào file:

```bash
garbage 2> err.txt
cat err.txt
```

Nội dung `err.txt` là thông báo lỗi shell (ví dụ *command not found*).

## Command substitution

**Command substitution** thay **lệnh** bằng **đầu ra** của lệnh đó.

Hai cách **tương đương**:

```bash
$(command)    # khuyến nghị — dễ lồng nhau, rõ ràng
`command`     # backticks / backquotes — cú pháp cũ
```

Ví dụ: lưu thư mục hiện tại vào biến **`here`**:

```bash
here=$(pwd)
echo $here
```

In đường dẫn **present working directory** hiện tại.

## Command-line arguments

**Command-line arguments** là đối số truyền cho **chương trình** (kể cả **shell script**) ngay trên dòng lệnh.

```bash
./MyBashScript.sh arg1 arg2
```

*   **`arg1`**, **`arg2`** được script đọc qua **`$1`**, **`$2`**, …
*   **`$0`** — tên script; **`$#`** — số đối số; **`$@`** / **`$*`** — danh sách đối số (chi tiết ở bài sau).

## Hai chế độ chạy lệnh

### Batch mode (tuần tự) — mặc định

Lệnh chạy **lần lượt**. Ví dụ với **`;`**:

```bash
command1; command2
```

**`command2`** chỉ chạy sau khi **`command1`** **kết thúc**.

### Concurrent mode (song song)

**`&`** sau lệnh → chạy lệnh đó ở **background**, shell trả quyền điều khiển cho lệnh **foreground** tiếp theo:

```bash
command1 & command2
```

*   **`command1`** chạy nền.
*   **`command2`** chạy foreground (có thể chạy **trước khi** `command1` xong — song song).

## Tóm tắt bài học

*   **Metacharacters** — `#`, `;`, `*`, `?`, … có nghĩa đặc biệt với shell.
*   **Quoting** — `\`, `"`, `'` để escape hoặc điều khiển expansion.
*   **I/O redirection** — `>`, `>>`, `2>`, `2>>`, `<`.
*   **Command substitution** — `$(cmd)` hoặc `` `cmd` ``.
*   **Command-line arguments** — truyền dữ liệu vào script (`$1`, `$2`, …).
*   **Concurrent mode** — `&` chạy lệnh nền song song với lệnh tiếp theo.

## Ghi chú DevOps

*   **`2>> /var/log/app.err`** — mô hình gom lỗi cron/job; kết hợp **`>>`** cho stdout log.
*   **`$(...)`** trong script deploy: `VERSION=$(git rev-parse --short HEAD)`.
*   Background **`&`** hữu ích khi khởi chạy server tạm; production thường dùng **systemd**, **supervisor**, hoặc container thay vì `&` thủ công.
*   Glob `*` trong script có thể vô tình match nhiều file — cân nhắc quote biến: `"$file"`.

## Liên kết

| Doc | Nội dung |
|-----|----------|
| `[02-064]` | Shebang, `>>`, `chmod +x` |
| `[02-065]` | Pipe `\|`, biến `$VAR` |
| `[02-057]` | Quyền file |

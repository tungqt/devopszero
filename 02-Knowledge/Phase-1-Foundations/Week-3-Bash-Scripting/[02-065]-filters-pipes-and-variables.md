# Bộ lọc, pipe và biến (Filters, Pipes, and Variables)

## Document Information
- **Doc ID:** 02-065
- **Summary:** **Filter** (stdin → stdout); toán tử **pipe** `|` (pipeline); ví dụ `ls | sort -r`; **shell variable** (`=`, `$`, `set`, `unset`); **environment variable** (`export`, `env`); ví dụ `GREETINGS`, `AUDIENCE`.
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Bộ lọc, pipe và biến". Sau khi xem video này, bạn sẽ có thể:

*   **Mô tả** và **dùng** pipe và filter.
*   **Giải thích** và **gán** biến shell và biến môi trường.

## Filter là gì?

**Filter** là lệnh shell hoặc chương trình:

*   Nhận **đầu vào** từ **standard input (stdin)** — thường là bàn phím.
*   Trả **đầu ra** ra **standard output (stdout)** — thường là terminal.

Có thể hình dung filter như một **bộ biến đổi** (*transformer*): chương trình **biến đổi** dữ liệu đầu vào thành dữ liệu đầu ra.

Ví dụ filter thường gặp (đã học trong module trước):

| Lệnh | Vai trò (tóm tắt) |
|------|-------------------|
| `wc` | Đếm dòng, từ, ký tự |
| `cat` | Đọc/ghép và in nội dung |
| `more` | Xem file có phân trang |
| `head`, `tail` | Đầu / cuối luồng hoặc file |
| `sort` | Sắp xếp dòng |
| `grep` | Lọc dòng khớp mẫu |

Giá trị của filter: chúng có thể **nối chuỗi** với nhau — nhờ **pipe**.

*(Chi tiết từng lệnh: `[02-058]`, `[02-059]`.)*

## Pipe (`|`)

Toán tử **pipe**, ký hiệu **`|`** (vertical bar), mở rộng mạnh khả năng shell.

**Mẫu dùng:**

```text
command1 | command2 | command3 ...
```

*   **Đầu ra** của `command1` trở thành **đầu vào** của `command2`, và cứ thế.
*   **Pipe** = viết tắt của **pipeline** (đường ống xử lý dữ liệu).

**Ví dụ:** liệt kê thư mục rồi **sắp xếp ngược**:

```bash
ls | sort -r
```

Kết quả: danh sách nội dung thư mục được **sort -r** (reverse) thay vì in thô từ `ls`.

## Biến shell (shell variables)

**Biến shell** chỉ có **phạm vi trong shell** tạo ra chúng — shell khác **không thấy** biến của nhau.

### Liệt kê biến: `set`

```bash
set
```

In **mọi biến** và định nghĩa mà shell hiện tại **nhìn thấy** (kèm nhiều thông tin khác).

Chỉ xem vài dòng đầu:

```bash
set | head
```

*(Khóa học dùng `head` để xem khoảng 4 định nghĩa đầu — tùy môi trường số dòng có thể khác.)*

### Gán biến

Dùng dấu **`=`** — **không có khoảng trắng** quanh dấu bằng:

```bash
GREETINGS=hello
```

Xem giá trị: tiền tố **`$`** rồi dùng `echo`:

```bash
echo $GREETINGS
```

Gán thêm biến và in nhiều biến:

```bash
AUDIENCE=world
echo $GREETINGS $AUDIENCE
```

Kết quả: `hello world` (hai giá trị cách nhau bởi khoảng trắng mặc định của `echo`).

### Xóa biến: `unset`

```bash
unset AUDIENCE
```

Xóa biến **`AUDIENCE`** khỏi shell hiện tại.

## Biến môi trường (environment variables)

**Biến môi trường** giống biến shell nhưng **phạm vi rộng hơn**:

*   **Tồn tại** trong mọi **tiến trình con** (*child processes*) do shell đó tạo ra.

### Xuất biến: `export`

Biến shell → biến môi trường:

```bash
export GREETINGS
```

Sau `export`, **`GREETINGS`** được các process con kế thừa.

### Liệt kê biến môi trường: `env`

```bash
env
```

In **tất cả** biến môi trường.

Kiểm tra `GREETINGS` đã export chưa — lọc bằng pipe + `grep`:

```bash
env | grep GREE
```

Nếu thấy dòng chứa `GREETINGS`, biến đã là **environment variable**.

## So sánh nhanh

| | Shell variable | Environment variable |
|---|----------------|----------------------|
| Phạm vi | Chỉ shell hiện tại | Shell + **child processes** |
| Tạo / gán | `NAME=value` | `export NAME` (sau khi gán) |
| Liệt kê | `set` | `env` |
| Xóa | `unset NAME` | `unset NAME` (trong shell hiện tại) |

## Tóm tắt bài học

*   **Filter** = lệnh/chương trình stdin → stdout; có thể nối chuỗi.
*   **`|`** = nối filter; đầu ra lệnh trước là đầu vào lệnh sau (**pipeline**).
*   Biến shell: gán **`VAR=value`** (không space quanh `=`), đọc **`$VAR`**, liệt kê **`set`**, xóa **`unset`**.
*   Biến môi trường: biến shell + **`export`**; liệt kê bằng **`env`**; con process thừa hưởng.

## Ghi chú DevOps

*   Pipeline `cmd1 | cmd2 | grep ...` là mô hình xử lý log và kiểm tra nhanh trên server (không cần tải file về).
*   Biến như **`PATH`**, **`HOME`**, **`USER`** là env var — script và container thường phụ thuộc chúng.
*   Trong script, nên dùng **`export VAR`** chỉ khi process con cần đọc; tránh export dư thừa (lộ secret, khó debug).
*   Tên biến shell **phân biệt hoa thường**: `GREETINGS` ≠ `greetings`.

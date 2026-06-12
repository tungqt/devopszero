# Ví dụ về pipe (Examples of Pipes)

## Document Information
- **Doc ID:** 02-066
- **Summary:** Pipe `|` không giới hạn số lần nối; **`sort | uniq`** trên `pets.txt`; **`echo | tr`** và **`cat | tr`** (chữ hoa, thay ký tự); pipeline **`sort | uniq | tr`**; trích **`"price"`** từ JSON bằng **`cat | grep -oE`**; gợi ý **`curl | grep`**.
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập

Sau khi đọc xong, bạn sẽ có thể:

*   **Mô tả** pipe.
*   **Dùng** pipe để kết hợp lệnh khi làm việc với chuỗi và nội dung file văn bản.
*   **Dùng** pipe để trích thông tin từ URL / dữ liệu dạng văn bản (ví dụ JSON).

*(Khái niệm filter, pipe, biến: `[02-065]`.)*

## Pipe là gì?

**Pipe** cho phép dùng **đầu ra** của lệnh này làm **đầu vào** của lệnh khác.

**Cú pháp:**

```text
[command 1] | [command 2] | [command 3] ... | [command n]
```

**Không giới hạn** số lần nối pipe liên tiếp.

Trong lab/reading này, bạn dùng pipe và filter để giải các bài toán **xử lý dữ liệu cơ bản**.

## Ví dụ 1: `sort` và `uniq` trên file

File **`pets.txt`**:

```text
goldfish
dog
cat
parrot
dog
goldfish
goldfish
```

### Chỉ `sort`

```bash
sort pets.txt
```

```text
cat
dog
dog
goldfish
goldfish
goldfish
parrot
```

File **đã sắp xếp** nhưng vẫn **trùng** (`dog`, `goldfish`).

### Chỉ `uniq`

```bash
uniq pets.txt
```

```text
goldfish
dog
cat
parrot
dog
goldfish
```

`uniq` chỉ **gộp dòng trùng liên tiếp** — bản sao **không liền kề** của `dog` và `goldfish` vẫn còn.

### `sort | uniq` — thứ tự đúng

```bash
sort pets.txt | uniq
```

```text
cat
dog
goldfish
parrot
```

| Bước | Tác dụng |
|------|----------|
| **`sort`** | Đưa các dòng giống nhau **cạnh nhau** |
| **`uniq`** | Xóa **trùng liên tiếp** → chỉ còn dòng **duy nhất** |

**Quy tắc:** với `uniq`, gần như luôn **`sort` trước**, `uniq` sau.

## Ví dụ 2: `tr` qua pipe (chuỗi và file)

Lệnh **`tr`** (*translate*) thay ký tự trong văn bản đầu vào:

```text
tr [OPTIONS] [target characters] [replacement characters]
```

`tr` thường chỉ nhận **stdin** (bàn phím), **không** nhận tên file trực tiếp — dùng **pipe** để áp dụng lên **chuỗi** hoặc **nội dung file**.

### Chuỗi: `echo | tr`

Thay **nguyên âm** bằng `_`:

```bash
echo "Linux and shell scripting are awesome\!" | tr "aeiou" "_"
```

```text
L_n_x _nd sh_ll scr_pt_ng _r_ _w_s_m_!
```

Thay **phần bù** (mọi ký tự **không** trong tập nguyên âm) bằng `_` — tùy chọn **`-c`** (*complement*):

```bash
echo "Linux and shell scripting are awesome\!" | tr -c "aeiou" "_"
```

```text
_i_u__a_____e______i__i___a_e_a_e_o_e_
```

### File: `cat | tr`

Đổi toàn bộ nội dung file sang **chữ HOA**:

```bash
cat pets.txt | tr "[a-z]" "[A-Z]"
```

```text
GOLDFISH
DOG
CAT
PARROT
DOG
GOLDFISH
GOLDFISH
```

### Pipeline dài hơn

Chỉ các dòng **duy nhất**, rồi in HOA:

```bash
sort pets.txt | uniq | tr "[a-z]" "[A-Z]"
```

```text
CAT
DOG
GOLDFISH
PARROT
```

## Ví dụ 3: Trích thông tin từ JSON

Lưu JSON (ví dụ giá Bitcoin) vào **`Bitcoinprice.txt`**. Trường cần lấy dạng:

```text
"price": 57907.78008618953
```

### `grep` với regex mở rộng

```bash
grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

Pipe từ file:

```bash
cat Bitcoinprice.txt | grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

**Giải thích từng phần:**

| Phần | Ý nghĩa |
|------|--------|
| **`-o`** | Chỉ in **phần khớp**, không in cả dòng |
| **`-E`** | Extended regex (`?`, v.v.) |
| `\"price\"` | Chuỗi literal `"price"` |
| `\s*` | Không hoặc nhiều khoảng trắng |
| `:` | Dấu hai chấm |
| `[0-9]*` | Không hoặc nhiều chữ số |
| `\.?` | Dấu chấm thập phân (tùy chọn) |
| `[0-9]*` | Phần thập phân (tùy chọn) |

Kết quả mẫu: chỉ đoạn khớp kiểu `"price": 57907.78008618953` (tùy file).

### URL và `curl`

Tương tự, có thể pipe **đầu ra HTTP** vào `grep` để trích field từ JSON/text:

```bash
curl -s "https://example.com/api/coin.json" | grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

*(URL và `curl`: `[02-061]`.)*

## Tóm tắt

*   **Pipe** = đầu ra lệnh trước → đầu vào lệnh sau; nối **không giới hạn**.
*   **`sort | uniq`**: sắp xếp rồi bỏ trùng liên tiếp → danh sách dòng unique.
*   **`echo` / `cat` | `tr`**: áp dụng `tr` lên chuỗi hoặc file; **`tr -c`** cho phép bù.
*   **`cat` (hoặc `curl`) | grep -oE`**: trích mẫu từ file JSON hoặc phản hồi URL.

## Ghi chú DevOps

*   Pipeline one-liner thay cho script tạm khi debug log hoặc API response trên server.
*   JSON phức tạp: cân nhắc **`jq`** thay vì chỉ `grep` (regex dễ vỡ khi format đổi).
*   Luôn kiểm tra **thứ tự** filter: `sort` trước `uniq`; `grep` sau khi đã có text trên stdout.

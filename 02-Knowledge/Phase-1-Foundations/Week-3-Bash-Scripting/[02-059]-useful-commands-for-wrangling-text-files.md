# Lệnh hữu ích xử lý file văn bản (Useful Commands for Wrangling Text Files)

## Document Information
- **Doc ID:** 02-059
- **Summary:** **`sort`** / **`sort -r`**; **`uniq`** (chỉ gộp dòng **liên tiếp** trùng); **`grep`** / **`grep -i`**; **`cut -c`** (cắt theo ký tự) và **`cut -d` / `-f`** (theo trường); **`paste`** / **`paste -d`** (ghép cột từ nhiều file).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh hữu ích xử lý file văn bản". Trong video này, bạn sẽ học cách dùng lệnh thao tác trên file để:

*   Tạo bản xem **sắp xếp từng dòng**.
*   Tạo bản xem **bỏ dòng lặp** (sau khi hiểu điều kiện của `uniq`).
*   **Trích** các dòng chứa mẫu cho trước.
*   **Cắt** đoạn hoặc **trường** trên mỗi dòng.
*   **Ghép** dòng từ nhiều file thành bảng/cột.

## Sắp xếp dòng: `sort`

**`sort`** sắp xếp các **dòng** của file theo thứ tự **chữ-số** (*alphanumeric*) và in kết quả ra **stdout**.

```bash
sort pets.txt
```

*   Ví dụ: `cat` lặp nhiều lần, `dog` lặp — sau `sort` các dòng được xếp theo alphabet, vẫn có thể **còn trùng** nếu nội dung dòng giống nhau.

Sắp xếp **ngược**:

```bash
sort -r pets.txt
```

*   Thứ tự đảo — ví dụ các dòng `dog` có thể **trước** các dòng `cat`.

## Loại dòng trùng liên tiếp: `uniq`

**`uniq`** lọc bỏ dòng **trùng lặp** — nhưng **chỉ** khi các bản sao **nằm cạnh nhau** (*consecutive*).

Xem lại nội dung:

```bash
cat pets.txt
uniq pets.txt
```

*   Kết quả có thể còn **`cat` hai lần** và **`dog`**: vì sau khi `sort` hoặc trong file gốc, hai dòng `cat` **không liên tiếp** nếu có `dog` xen giữa — `uniq` **không** gộp các bản `cat` cách nhau bởi dòng khác.

**Thực hành tốt:** thường dùng **`sort file | uniq`** để gom mọi dòng trùng nội dung (sau khi sort, các bản sao giống nhau đứng liền nhau).

## Tìm dòng theo mẫu: `grep`

**`grep`** (*global regular expression print*) trả về các **dòng** khớp **mẫu** (có thể là biểu thức chính quy).

File ví dụ **`people.txt`** — danh sách tên:

```bash
cat people.txt
grep ch people.txt
```

*   Tìm dòng chứa chuỗi liên tiếp **`ch`** (chữ thường): ví dụ *Dennis Ritchie*, *Erwin Schrödinger*.

**Không phân biệt hoa thường:**

```bash
grep -i ch people.txt
```

*   **`-i`**: thêm kết quả như *Charles Babbage* (chữ **C** hoa).

## Cắt ký tự hoặc trường: `cut`

**`cut`** trích **phần** của mỗi dòng.

### Cắt theo vị trí ký tự: `-c`

Trích ký tự **thứ 2 đến 9** trên mỗi dòng:

```bash
cut -c 2-9 people.txt
```

*   Ví dụ *Alan Turing* → `Lan Turi` (cắt theo **vị trí ký tự**, không theo từ).

### Cắt theo trường (field): `-d` và `-f`

Mỗi dòng: **họ** và **tên**, cách nhau bởi **dấu cách**. Lấy **trường thứ 2** (tên / last name trong ví dụ video):

```bash
cut -d' ' -f2 people.txt
```

*   **`-d' '`** (hoặc `-d " "`): **delimiter** — ký tự phân tách trường là **space**.
*   **`-f2`**: in **trường thứ 2** mỗi dòng → danh sách **họ** (last name) trong ví dụ khóa học.

*(Nếu tên có nhiều từ, delimiter space đơn giản có thể không đủ — cần công cụ mạnh hơn như `awk` ở bài sau.)*

## Ghép cột từ nhiều file: `paste`

**`paste`** **ghép** dòng tương ứng từ **nhiều file** (cùng số dòng) thành **một dòng** nhiều cột.

Ba file cùng số dòng:

| File | Nội dung |
|------|----------|
| `first.txt` | Tên |
| `last.txt` | Họ |
| `yob.txt` | Năm sinh |

```bash
paste first.txt last.txt yob.txt
```

*   Mặc định dùng **TAB** làm delimiter giữa cột → ba cột **thẳng hàng** dạng bảng.
*   Ví dụ: thấy *Charles Babbage* sinh **1791**.

Đổi delimiter (ví dụ **dấu phẩy** CSV):

```bash
paste -d"," first.txt last.txt yob.txt
```

*   Mỗi trường cách nhau bởi **`,`** — ví dụ `Dennis,Ritchie,1941`.

## Tổng kết (Summary)

Trong video này, bạn đã học cách:

*   Xem dòng file **sắp xếp** bằng **`sort`** (và **`sort -r`**).
*   **Bỏ dòng trùng liên tiếp** trong luồng xem bằng **`uniq`** (nhớ điều kiện *consecutive*; thường kết hợp **`sort | uniq`**).
*   Lấy dòng **khớp tiêu chí** bằng **`grep`** (và **`-i`**).
*   **Cắt** đoạn hoặc **trường** trên mỗi dòng bằng **`cut`** (`-c`, `-d`, `-f`).
*   **Ghép** dòng từ nhiều file bằng **`paste`** (mặc định TAB; **`-d`** cho delimiter tùy chỉnh).

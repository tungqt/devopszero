# Xem nội dung tập tin (Viewing File Content)

## Document Information
- **Doc ID:** 02-058
- **Summary:** Lệnh xem nội dung file: **`cat`** (in toàn bộ ra stdout); **`more`** (phân trang, Space / `q`); **`head`** / **`tail`** (mặc định 10 dòng, **`-n`**); **`wc`** (dòng, từ, byte/ký tự; **`-l`**, **`-w`**, **`-c`**).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Xem nội dung tập tin". Trong video này, bạn sẽ học cách:

*   Dùng các lệnh thao tác trên **tập tin** để **xem nội dung** theo nhiều cách hữu ích.
*   **Đếm** số **dòng**, **từ**, và **ký tự** (byte) trong file.

Có nhiều lệnh hữu ích để xem nội dung file. Dưới đây là các lệnh chính trong bài.

## In toàn bộ file: `cat`

**`cat`** (*concatenate*) in **toàn bộ** nội dung tập tin ra **standard output** (stdout).

Giả sử thư mục hiện tại có một file **`numbers.txt`** (xem bằng **`ls`**):

```bash
cat numbers.txt
```

*   Ví dụ trong video: đầu ra có thể là các số **89–99** (hoặc toàn bộ dãy số trong file).
*   Đầu ra có thể **lấp đầy** cửa sổ terminal.

**Hạn chế:** file **dài** hơn vùng hiển thị — bạn chỉ thấy một phần (ví dụ 12 dòng) trong khi file còn nhiều dòng phía trên/dưới. Vì vậy **không phải lúc nào** cũng nên dùng `cat` để “đọc” file dài.

## Xem từng trang: `more`

**`more`** cho phép xem nội dung **theo trang** (*page by page*).

```bash
more numbers.txt
```

*   **Trang 1** có thể hiển thị số **0–8** (tùy kích thước cửa sổ terminal).
*   **“Trang”** ở đây = nội dung vừa **cửa sổ terminal hiện tại** — kéo **cao** terminal thì **trang** cũng **lớn hơn**.

Điều khiển:

| Thao tác | Kết quả |
|----------|---------|
| **Phím Space** | Trang tiếp theo (ví dụ số **9–17**) |
| **`q`** | Thoát `more`, về **prompt** |

## In phần đầu file: `head`

**`head`** in **10 dòng đầu** của file (mặc định).

```bash
head numbers.txt
```

*   Ví dụ: dòng / số **0–9** (10 dòng đầu).

Chỉ định số dòng bằng **`-n`**:

```bash
head -n 3 numbers.txt
```

*   Trả về **3 dòng đầu**: ví dụ `0`, `1`, `2`.

## In phần cuối file: `tail`

**`tail`** in **10 dòng cuối** của file (mặc định).

```bash
tail numbers.txt
```

*   Ví dụ: **90–99** (10 dòng cuối của `numbers.txt`).

Giống `head`, dùng **`-n`** để đổi số dòng:

```bash
tail -n 3 numbers.txt
```

*   Ba dòng cuối: ví dụ **97**, **98**, **99**.

## Đếm dòng, từ, byte: `wc`

**`wc`** (*word count*) đếm **ký tự (byte)**, **từ**, hoặc **dòng** trong file.

Ví dụ file **`pets.txt`**: mỗi dòng một từ (`cat`, `dog`, …).

```bash
cat pets.txt
wc pets.txt
```

Đầu ra kiểu:

```text
7  7  28  pets.txt
```

| Cột | Ý nghĩa |
|-----|--------|
| **7** (cột 1) | Số **dòng** |
| **7** (cột 2) | Số **từ** |
| **28** (cột 3) | Số **byte** (ký tự, gồm ký tự xuống dòng) |

**Vì sao 28 byte chứ không phải 7×3 = 21?**  
`wc` còn đếm **ký tự xuống dòng** (*newline*) — mỗi dòng thường có `\n` ở cuối; có thể có thêm ký tự **cuối file** tùy cách tạo file. Bạn **không thấy** newline khi `cat`, nhưng chúng vẫn được tính.

Chỉ lấy một loại đếm:

| Lệnh | Chỉ in |
|------|--------|
| `wc -l pets.txt` | Số **dòng** |
| `wc -w pets.txt` | Số **từ** |
| `wc -c pets.txt` | Số **byte** |

*(Trên một số hệ, `-c` là byte; `-m` đôi khi dùng cho ký tự — xem `man wc` trên máy bạn.)*

## Tổng kết (Summary)

Trong video này, bạn đã học cách:

*   Dùng **`cat`**, **`more`**, **`head`**, và **`tail`** để xem nội dung file theo **nhiều cách** (toàn bộ, phân trang, đầu/cuối file).
*   Dùng **`wc`** để biết **số dòng, từ và byte** của file, với tùy chọn **`-l`**, **`-w`**, **`-c`**.

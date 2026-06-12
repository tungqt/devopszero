# Lệnh lưu trữ và nén tập tin (File Archiving and Compression Commands)

## Document Information
- **Doc ID:** 02-062
- **Summary:** Phân biệt **archive** vs **compression**; **`tar -cf`** / **`-tf`** / **`-xf`**; **`tar -czf`** / **`-xzf`** (gzip, `.tar.gz`); **`zip -r`** / **`unzip`**; ví dụ cây thư mục `notes` (math, physics, week1, week2).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh lưu trữ và nén tập tin". Sau khi xem video này, bạn sẽ có thể:

*   **Phân biệt** **lưu trữ (archiving)** và **nén (compression)**.
*   **Tạo** file archive và **giải nén / giải archive**.
*   Dùng lệnh để **nén**, **giải nén**, và **trích** tập tin từ archive.

## Archive vs compression

**Lưu trữ** và **nén** là hai quá trình **khác nhau**, thường được **kết hợp**.

| Khái niệm | Ý nghĩa |
|-----------|--------|
| **Archiving** | Gom dữ liệu ít dùng hàng ngày nhưng cần **giữ lại** thành **một file** (nhiều file + thư mục → một archive). Dễ **di chuyển**, dùng như **backup** khi mất/hỏng dữ liệu. |
| **Compression** | **Giảm kích thước** file bằng cách tận dụng **dư thừa** trong nội dung. |

**Lợi ích chính của nén:**

*   Tiết kiệm **dung lượng lưu trữ**
*   **Tăng tốc** chuyển file
*   Giảm **tải băng thông**

## Ví dụ: thư mục `notes`

Bạn có thư mục **`notes`** cho tài liệu khóa học:

```text
notes/
├── math/
│   ├── week1
│   └── week2
└── physics/
    ├── week1
    └── week2
```

Liệt kê **đệ quy** cây thư mục:

```bash
ls -R
```

*   Khớp với cấu trúc trên: thư mục cha **notes**, con **math** / **physics**, file **week1**, **week2** trong mỗi nhánh.

## `tar` — archive và tarball

**`tar`** (*tape archiver*) **đóng gói** và **giải đóng gói** file/thư mục. File archive kiểu tar thường gọi **tarball**.

### Tạo archive (không nén)

```bash
tar -cf notes.tar notes
```

| Tùy chọn | Ý nghĩa |
|----------|--------|
| **`-c`** | **Create** — tạo archive mới |
| **`-f`** | Đọc/ghi archive từ **file** (tên file đứng ngay sau các tùy chọn) |

```bash
ls
```

*   Còn thư mục **`notes`** gốc và file **`notes.tar`**.

### Tạo archive **có nén** gzip (`.tar.gz`)

```bash
tar -czf notes.tar.gz notes
```

| Tùy chọn | Ý nghĩa |
|----------|--------|
| **`-z`** | Lọc archive qua **gzip** |

*   Hậu tố **`.gz`** giúp phần mềm (kể cả trên Windows) **nhận đúng** loại file.

```bash
ls
```

*   Thấy **`notes.tar.gz`**.

### Liệt kê nội dung tarball: `-t`

```bash
tar -tf notes.tar
```

*   **`-t`**: **list** — in mọi file/thư mục trong archive; cấu trúc khớp cây `notes` gốc.

### Giải archive: `-x`

```bash
tar -xf notes.tar
```

*   **`-x`**: **extract** — trích file/thư mục ra (đích mặc định thường là thư mục hiện tại, tạo lại cây `notes`).

```bash
ls -R
```

*   Xác nhận: **notes** → **math** / **physics** → **week1**, **week2**.

### Giải nén **và** giải archive `.tar.gz`

```bash
tar -xzf notes.tar.gz
```

*   **`-x`**: extract; **`-z`**: giải nén gzip.

```bash
ls -R
```

*   Cấu trúc thư mục/file như mong đợi.

## `zip` và `unzip`

### `zip` — nén rồi đóng gói

**`zip`** nén **từng file trước**, rồi gom vào **một** archive.

*(Khác với **`tar -z`**: `tar` **gom trước** thành tarball, **sau đó** gzip **cả** tarball.)*

```bash
zip -r notes.zip notes
```

*   **`-r`**: đệ quy — gồm thư mục con và file bên trong.

```bash
ls
```

*   Có **`notes.zip`**.

### `unzip` — giải nén và trích

```bash
unzip notes.zip
```

```bash
ls -R
```

*   Tạo lại thư mục **`notes`** và toàn bộ **math** / **physics** / **week1** / **week2**.

## So sánh nhanh: `tar`+gzip vs `zip`

| | **tar + gzip** (`tar -czf`) | **zip** (`zip -r`) |
|---|---------------------------|-------------------|
| Thứ tự | **Bundle** → rồi **nén** cả tarball | **Nén** từng phần → rồi **đóng gói** |
| Đuôi file thường gặp | `.tar`, `.tar.gz` | `.zip` |

## Tổng kết (Summary)

Trong video này, bạn đã học:

*   **Nén** giúp tiết kiệm dung lượng, tăng tốc chuyển file, giảm tải băng thông.
*   **`zip -r`**: nén và đóng gói thư mục/file vào **`.zip`**; **`unzip`**: giải nén và trích.
*   **`tar -cf`**: archive thành **tarball**; **`tar -czf`**: archive + **gzip** (`.tar.gz`); **`tar -tf`**: xem nội dung; **`tar -xf`** / **`tar -xzf`**: giải archive (và giải nén nếu có `-z`).

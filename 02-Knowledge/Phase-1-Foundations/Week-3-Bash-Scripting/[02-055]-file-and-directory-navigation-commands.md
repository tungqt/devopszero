# Lệnh điều hướng tập tin và thư mục (File and Directory Navigation Commands)

## Document Information
- **Doc ID:** 02-055
- **Summary:** **`ls`** (liệt kê, `-l` chi tiết); **`pwd`** thư mục hiện tại; **`cd`** chuyển thư mục; **đường dẫn tương đối và tuyệt đối** (`..`, `~`, đường dẫn đầy đủ); **`find`** (`.`, `-name`, `-iname`).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh điều hướng tập tin và thư mục". Trong video này, bạn sẽ học cách:

*   Dùng lệnh **list (`ls`)** để liệt kê nội dung thư mục.
*   **Chuyển** giữa các thư mục bằng lệnh **change directory (`cd`)**.
*   **Giải thích** đường dẫn **tương đối** và **tuyệt đối**.
*   **Tìm** tập tin bằng lệnh **`find`**.

## Liệt kê: `ls` (list)

**`ls`** liệt kê **tập tin** và **thư mục** bên trong một thư mục.

*   Gõ **`ls`** trong **thư mục home**: xem mọi tập tin và thư mục con **trực tiếp** trong home.
*   Có thể truyền **tên thư mục** làm tham số, ví dụ **`ls Downloads`** — liệt kê nội dung thư mục **Downloads**.

### Tùy chọn `-l` (long format)

Khi cần **thêm thông tin** (ví dụ đang ở thư mục Documents):

```bash
ls -l
```

*   Hiển thị danh sách **dài, chi tiết** hơn: tập tin và thư mục con kèm **quyền**, **ngày sửa cuối**, **chủ sở hữu**, v.v.

## Thư mục làm việc hiện tại: `pwd`

Khi cần biết **đang đứng ở thư mục nào**, dùng **`pwd`** (*print working directory*):

```bash
pwd
```

Ví dụ trong video: có thể thấy đang ở home, ví dụ đường dẫn kiểu **`/Users/me`**.

## Chuyển thư mục: `cd` (change directory)

**`cd`** đổi **thư mục làm việc hiện tại**.

*   Từ **home**, vào thư mục con **Documents**:

```bash
cd Documents
```

Sau đó **`pwd`** sẽ cho thấy đường dẫn tới **thư mục con Documents** (ví dụ `.../Documents`).

**`cd`** nhận đối số là **đường dẫn tương đối** hoặc **tuyệt đối**.

### Lên thư mục cha: `..` (tương đối)

Giả sử bạn đang ở thư mục **Notes** (nằm trong cây thư mục Documents — ví dụ cấu trúc có thư mục cha trung gian như **Math** chứa **Notes**). Để về **thư mục cha** ngay phía trên **Notes**:

```bash
cd ..
```

*   **`..`** là ký hiệu **thư mục cha** so với thư mục hiện tại (đường dẫn **tương đối**).
*   Trong ví dụ video, sau lệnh này thư mục làm việc trở thành **Math** (cha trực tiếp của **Notes**).

### Về home: `~`

Để **nhảy thẳng** về thư mục **home** của user:

```bash
cd ~
```

*   Ký hiệu **`~`** trong ngữ cảnh này biểu thị **home**; trong video được mô tả như cách tham chiếu **tuyệt đối** tới home (luôn trỏ về home của user hiện tại).

### Đường dẫn đầy đủ (tuyệt đối)

Bạn cũng có thể **`cd`** tới một thư mục bằng **đường dẫn đầy đủ** từ gốc filesystem, ví dụ quay lại thư mục **Notes** bằng pathname tuyệt đối — lệnh sẽ đưa bạn **trở lại** đúng thư mục **Notes** như mong đợi.

## Đường dẫn tương đối và tuyệt đối (tóm tắt)

| Loại | Ý nghĩa |
|------|--------|
| **Tương đối** | Phụ thuộc **thư mục hiện tại** (ví dụ `Documents`, `..`, tên file trong thư mục đang đứng). |
| **Tuyệt đối** | **Độc lập** với vị trí hiện tại — thường bắt đầu bằng **`/`** (đường dẫn đầy đủ từ gốc), hoặc dùng **`~`** cho home như một neo cố định tới home của user. |

## Tìm tập tin: `find`

**`find`** là công cụ mạnh: trả về **đường dẫn** tới mọi tập tin **thỏa tiêu chí** bạn chỉ định.

Ví dụ cấu trúc trong **Documents**: hai thư mục con, mỗi thư mục có vài file. Đang làm việc trong **Documents**, muốn tìm **mọi file tên** `a.txt` **trong** thư mục làm việc hiện tại:

```bash
find . -name 'a.txt'
```

*   **`.`** nghĩa là **“bắt đầu tìm từ đây”** — thư mục hiện tại; lệnh chỉ quét **trong** cây bắt đầu từ điểm đó (theo mô tả bài học).

### Không phân biệt hoa thường: `-iname`

Để tìm **không phân biệt** chữ hoa/thường, dùng **`-iname`** thay cho **`-name`**:

*   Có thể tìm thấy cùng một file như với `-name`, **và thêm** file trùng tên nhưng có chữ **A** hoa trong tên (ví dụ `A.txt`).

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   **`ls`** liệt kê tập tin và thư mục **trực tiếp** trong thư mục được chỉ định (hoặc thư mục hiện tại nếu không chỉ rõ) — mặc định **một cấp**; **`-l`** cho định dạng chi tiết hơn.
*   **`cd`** dùng để **điều hướng** giữa các thư mục.
*   **Đường dẫn tương đối** gắn với **thư mục làm việc hiện tại**; **đường dẫn tuyệt đối** (hoặc neo kiểu **`~`**) **không** phụ thuộc vào việc bạn đang đứng ở đâu theo cùng một nghĩa với đường dẫn tương đối thuần túy.
*   **`find`** dùng để **tìm** tập tin trong cây thư mục, với tiêu chí như **`-name`** / **`-iname`**.

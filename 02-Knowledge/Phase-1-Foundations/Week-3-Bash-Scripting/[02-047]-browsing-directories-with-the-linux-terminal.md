# Duyệt thư mục bằng Terminal Linux (Browsing directories with the Linux terminal)

## Document Information
- **Doc ID:** 02-047
- **Summary:** Bài đọc giới thiệu cách dùng Terminal Linux để tương tác với Shell; lệnh `pwd` để in đường dẫn thư mục làm việc hiện tại; lệnh `ls` để liệt kê nội dung thư mục (kể cả khi chỉ định path khác); quy ước đặt tên đường dẫn và gợi ý về cây thư mục từ root (`/`) và thư mục `home`.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập (Learning Objectives)

Sau khi hoàn thành bài đọc này, bạn sẽ có thể:

*   Mô tả **Terminal Linux** dùng để làm gì.
*   Dùng các lệnh **`pwd`** và **`ls`** để duyệt thư mục trong hệ thống tập tin Linux.

## Terminal Linux (The Linux terminal)

Bạn tương tác với **Linux shell** bằng cách nhập lệnh vào **Linux terminal**. Terminal còn thường được gọi là **command line** hoặc **command prompt**.

Trong ví dụ của khóa học, **thư mục làm việc hiện tại (present working directory)** là `/home/project`, thường được hiển thị (ví dụ bằng chữ màu xanh). Ký hiệu **dấu đô-la `$`** ngay sau đó gọi là **command prompt** (dấu nhắc lệnh).

Cửa sổ terminal là giao diện đơn giản cho phép bạn chạy bất kỳ lệnh nào bằng cách **gõ lệnh trên bàn phím** và nhấn **Enter**. Nhiều lệnh sẽ trả về **đầu ra (output)** — mặc định là văn bản hiển thị ngay trong cửa sổ terminal.

## Lệnh `pwd` (print working directory)

Lệnh **`pwd`** in ra **đường dẫn đầy đủ (path name)** của **thư mục làm việc hiện tại** (present working directory) trên dòng tiếp theo.

Ví dụ: sau khi chạy `pwd`, bạn thấy đúng như mong đợi là đường dẫn tới thư mục hiện tại, chẳng hạn `/home/project`. Sau đó **command prompt** xuất hiện lại ở dòng kế tiếp, sẵn sàng cho lệnh tiếp theo.

## Lệnh `ls` (list)

**Làm sao để xem bên trong thư mục làm việc hiện tại có gì?**

Dùng lệnh **`ls`** để **liệt kê nội dung** của thư mục bạn đang làm việc.

Nếu thư mục `/home/project` **mới tạo và chưa có gì**, chạy `ls` có thể **không in ra gì**. Chương trình `ls` thường **không in thêm một dòng trống** chỉ để “báo” là không có mục nào để liệt kê.

## Liệt kê thư mục khác: `ls <đường_dẫn>`

Bạn có thể liệt kê nội dung của **bất kỳ thư mục nào** bằng `ls` nếu **chỉ rõ tên / đường dẫn** thư mục muốn xem.

Ví dụ:

```bash
ls /home
```

liệt kê nội dung của thư mục `/home`. Trong ví dụ khóa học, `/home` chứa hai đối tượng: **`project`** và **`theia`**.

**Mẹo:** Hãy nghĩ **directory** như một **folder** chứa tập tin và thư mục con. Ở đây, `project` và `theia` là **thư mục con (subdirectories)** của `/home`. Thư mục con có thể chứa thêm tập tin và thư mục con; các lab sau sẽ đi sâu hơn.

## Quy ước đường dẫn (path naming)

*   Đường dẫn `/home/project` cho biết thư mục **`project`** là subdirectory của **`/home`**.
*   Tương tự, đường dẫn tới subdirectory **`theia`** là **`/home/theia`**.

## Root và nhánh `home`

Giống như một **cây**, hệ thống tập tin Linux có **thư mục gốc (root directory)** là **`/`** (đọc là “slash”), từ đó toàn bộ hệ thống **phân nhánh** ra.

Một subdirectory quan trọng của root là **`home`**. Bạn có thể tự kiểm chứng bằng:

```bash
ls /
```

để liệt kê nội dung của thư mục gốc `/` và thấy trong đó có **`home`** (và các mục khác tùy hệ thống).

## Tổng kết (Summary)

Trong bài đọc này, bạn đã học được rằng:

*   Bạn tương tác với **Linux shell** bằng cách nhập lệnh vào **Linux terminal**.
*   Lệnh **`pwd`** in ra **đường dẫn** tới **thư mục làm việc hiện tại** (present working directory).
*   Lệnh **`ls`** **liệt kê nội dung** của một thư mục — có thể là thư mục hiện tại hoặc thư mục bạn chỉ định sau `ls` (ví dụ `ls /home`, `ls /`).

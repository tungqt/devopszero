# Tổng quan về Terminal Linux (Linux Terminal Overview)

## Document Information
- **Doc ID:** 02-046
- **Summary:** Bài học giới thiệu Shell Linux, Terminal Linux, cách chúng phối hợp với nhau, luồng thực thi lệnh (user → Terminal → Shell → OS/kernel → phần cứng), khái niệm đường dẫn (path) và các ký hiệu đặc biệt (`~`, `/`, `..`, `.`), cùng lệnh `cd` để điều hướng thư mục và ví dụ với `ls`.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Tổng quan về Terminal Linux". Sau khi xem video này, bạn sẽ có thể:
*   Mô tả **Linux Shell** là gì.
*   Mô tả **Linux Terminal** là gì.
*   Giải thích cách **Terminal** và **Shell** làm việc cùng nhau.
*   Và sử dụng Terminal Linux để **điều hướng thư mục** (navigate directories).

## Linux Shell là gì?

**Linux Shell** là một ứng dụng cấp hệ điều hành (OS-level application) có nhiệm vụ **phiên dịch (interpret)** các lệnh.

Trong các phiên bản đầu của Unix và Linux, Shell là **cách duy nhất** để tương tác với hệ điều hành. Ngày nay bạn cũng có thể dùng giao diện đồ họa (GUI), nhưng Shell vẫn là lựa chọn phổ biến, linh hoạt, và là cách thuận tiện để chạy các **file script**.

Bạn có thể dùng lệnh Shell để thực hiện các tác vụ như:
*   Di chuyển và sao chép tập tin.
*   Ghi và đọc tập tin.
*   Trích xuất và lọc dữ liệu.
*   Tìm kiếm dữ liệu.

Có nhiều phiên bản Shell, nhưng **chức năng cơ bản** của hầu hết các Shell là tương đồng. Một số ví dụ phổ biến gồm **bash** và **zsh**.

## Linux Terminal là gì?

Bạn tương tác với Linux Shell thông qua **Linux Terminal**.

**Terminal** là một ứng dụng hoặc giao diện người dùng nơi bạn **nhập các lệnh** muốn chạy và **nhận đầu ra** (output) từ những lệnh đó.

Ví dụ: để khởi động ứng dụng Python và chạy chương trình tên `myprogram.py`, bạn gõ:

```bash
python myprogram.py
```

Khi bạn nhấn **Enter**, Shell sẽ chạy lệnh. Chương trình có thể in ra Terminal dòng chữ ví dụ như `Hello World`.

## Shell và Terminal hoạt động cùng nhau thế nào? (Luồng chạy lệnh)

**Cách lệnh được thực thi (tóm tắt luồng):**

1. Người dùng muốn chạy một lệnh.
2. Họ nhập lệnh trong **Terminal**; Terminal **chuyển tiếp** lệnh đó tới **Shell**.
3. Các thành phần cốt lõi của **hệ điều hành** và **kernel** dịch/chuyển yêu cầu để **phần cứng** thực hiện.
4. Khi phần cứng hoàn tất, kernel đọc các thay đổi hoặc kết quả, rồi gửi ngược lại qua **Shell** tới **Terminal** để người dùng xem.

Terminal là cách **mạnh mẽ** để chạy ứng dụng và tương tác với máy.

## Giao diện dòng lệnh: command line và prompt

Hầu hết Terminal có giao diện tương tự để bạn nhập lệnh.

*   **Command line (dòng lệnh):** vùng bạn nhập lệnh.
*   **Command prompt (dấu nhắc / con trỏ):** thường là một **đường thẳng dọc (cursor)** cho biết vị trí văn bản bạn gõ sẽ hiển thị.

Trong ví dụ của khóa học, **thư mục làm việc hiện tại (current working directory)** là thư mục `documents`, nằm trong thư mục `me`, nằm trong thư mục `home`.

**Current working directory** là vị trí mà Shell sẽ **tìm** các lệnh hoặc chương trình bạn chỉ định chạy (ví dụ chương trình Python trong ví dụ trước). Không phải Terminal nào cũng hiển thị **đầy đủ đường dẫn**; một số chỉ hiển thị tên thư mục cuối, ví dụ `documents`.

## Đường dẫn (path) và các ký hiệu đặc biệt

**Path** là vị trí con người có thể đọc được của một thư mục hoặc tập tin trong hệ thống tập tin Linux.

Cấu trúc **`A/B`** (trong tài liệu gốc: *A slash B*) biểu thị rằng tập tin hoặc thư mục tên **B** nằm **bên trong** thư mục tên **A**.

**Các path đặc biệt thường gặp:**

| Ký hiệu | Ý nghĩa |
|--------|---------|
| `~` (một dấu ngã) | Thư mục **home** của người dùng hiện tại. |
| `/` ở **đầu** path | **Thư mục gốc (root directory)** của hệ thống. |
| `..` (hai dấu chấm) | **Thư mục cha** của thư mục hiện tại. |
| `.` (một dấu chấm) | **Thư mục hiện tại**. |

## Dùng Terminal để đổi thư mục làm việc: lệnh `cd`

Lệnh **`cd`** (*change directory*) dùng để thay đổi **current working directory**.

*   `cd /` — chuyển tới **thư mục gốc** (root).
*   `cd bin` — (khi đang ở root) chuyển vào thư mục **`bin`** bên trong root.

Thư mục **`/bin`** chứa các chương trình mà hệ thống cần. Một trong các file thực thi đó là **`ls`**.

Để chạy chương trình `ls` **trong thư mục làm việc hiện tại** (khi file nằm ngay tại đó), bạn có thể gõ:

```bash
./ls
```

Dùng `ls` để **liệt kê** trong cửa sổ Terminal tên của mọi tập tin và thư mục trong thư mục hiện tại.

Nhiều lệnh nằm trong thư mục `bin` cũng được **tích hợp sẵn trong shell** (*built-in*), nên bạn có thể chạy chúng từ **các vị trí khác** mà không cần `./ls`.

Điều hướng về **home** của bạn:

```bash
cd ~
```

Dù current working directory là `/home/me` và **không** chứa file chương trình `ls` trong thư mục đó, bạn vẫn có thể chạy lệnh `ls` thành công nhờ cách Shell tìm lệnh (PATH / built-in).

## Thêm ví dụ điều hướng

Xuất phát từ `/home`, gõ:

```bash
cd ..
```

để chuyển thư mục làm việc lên **thư mục cha** của thư mục hiện tại. Trong ví dụ này, cha của `/home` là **`/`** (root).

Để vào ổ USB có tên (ví dụ) `my USB drive` trong thư mục `media`:

```bash
cd /media/my\ USB\ drive
```

(hoặc dùng dấu ngoặc kép tùy shell; ý chính là path tuyệt đối từ root.)

Bạn có thể **đi lên và xuống cây thư mục trong một lệnh**. Ví dụ, lên `media` rồi lên `root`:

```bash
cd ../..
```

Sau đó xuống thư mục `documents` trong `me` trong `home`:

```bash
cd /home/me/documents
```

Nhấn Enter để gửi lệnh và chuyển tới thư mục `documents`.

Di chuyển lên `/home/me`, rồi (ví dụ) chạy Python với file trong thư mục đó:

```bash
python myprogram.py
```

Chương trình có thể trả về thông điệp hiển thị trong cửa sổ Terminal.

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   **Linux shell** là ứng dụng cấp OS dùng để nhập lệnh và xem đầu ra của các lệnh đó.
*   Bạn dùng **terminal** để gửi lệnh tới shell.
*   Bạn có thể dùng lệnh **`cd`** để điều hướng trong hệ thống tập tin Linux.
*   **Terminal** và **Shell** phối hợp: Terminal nhận input và hiển thị output; Shell phiên dịch lệnh và phối hợp với kernel/OS để thực thi.

# Lệnh thông tin (Informational Commands)

## Document Information
- **Doc ID:** 02-053
- **Summary:** Lệnh xem **thông tin người dùng và hệ điều hành** (`whoami`, `id`, `uname`); **dung lượng đĩa** (`df`, `-h`, thư mục/`~`); **tiến trình** (`ps -e`, `top`); **in chuỗi/biến** (`echo`); **ngày giờ** (`date`, định dạng `+` và `%`); **trang hướng dẫn** (`man`).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh thông tin". Sau khi xem video này, bạn sẽ có thể:

*   **Tìm** thông tin người dùng và hệ điều hành.
*   **Xem** mức sử dụng đĩa của hệ thống.
*   **Giám sát** tiến trình đang chạy và mức dùng tài nguyên.
*   **In** chuỗi, biến và ngày giờ.
*   **Hiển thị** sổ tay hướng dẫn (manual) cho các lệnh.

Bạn dùng các **lệnh thông tin** trong terminal để hiển thị dữ liệu về **người dùng** và **hệ thống**. Các lệnh nhóm này hữu ích khi cần **xác minh** người đang đăng nhập hoặc **tài khoản** nào đang chạy một lệnh / tiến trình cụ thể.

## Thông tin người dùng: `whoami`

Lệnh **`whoami`** in **tên người dùng** hiện tại.

*   **Không** nhận đối số và **không** có tùy chọn (theo mô tả khóa học).
*   Ví dụ: người đăng nhập là *John Doe* → lệnh trả về tên user tương ứng.

## Thông tin người dùng và nhóm: `id`

Lệnh **`id`** trả về **ID người dùng** hoặc **ID nhóm** — con số gán cho mỗi user hoặc group trên hệ Linux.

*   **`id -u`**: chỉ in **số** (UID) của user hiện tại.
*   **`id -un`** (hoặc tổ hợp tương đương theo tài liệu video: `-u` kèm `-n`): in **tên** tương ứng với UID thay vì chỉ số.

*(Trên terminal thực tế, cú pháp chính xác là `id -u` và `id -un`; hãy dùng `man id` để xem đầy đủ tùy chọn trên máy bạn.)*

## Thông tin hệ điều hành: `uname`

**`uname`** (*Unix name*) trả về thông tin OS, ví dụ **tên kernel** và **phiên bản** — dùng để **nhận diện** loại hệ đang làm việc hoặc **chẩn đoán** vấn đề liên quan hệ thống.

*   Chỉ gõ **`uname`**: thường in **tên kernel** (trong ví dụ video là **Darwin** — đặc trưng của **macOS**; trên Linux thường thấy **`Linux`**).
*   **`uname -s`** và **`uname -r`**: có thể dùng kết hợp để xem **tên hệ/kernel** và **phiên bản phát hành**.
*   **`uname -v`**: thông tin phiên bản **chi tiết** hơn.

## Dung lượng đĩa: `df` (disk free)

**`df`** hiển thị **mức sử dụng đĩa** / không gian filesystem — hữu ích khi cần **theo dõi** dung lượng hoặc **kiểm tra** chỗ trống trên một filesystem.

*   Ví dụ: **`df -h ~`** — hiển thị bảng cho **thư mục home** (ký hiệu **`~`** là home của user hiện tại). Bảng phản ánh **filesystem** chứa đường dẫn đó: kích thước, đã dùng, còn trống, **phần trăm** đã dùng.
*   Trên Linux, **gắn (mount)** đĩa vào một thư mục nghĩa là filesystem của đĩa đó **truy cập được** qua thư mục đó.
*   **`-h`**: đầu ra **dễ đọc** (*human-readable*) — ví dụ GB, TB thay vì chỉ byte.
*   **`df -h`** (không chỉ thư mục): xem tóm tắt cho **các filesystem** được liệt kê (kích thước, đã dùng, còn trống).

## Tiến trình: `ps` (process status)

**`ps`** giúp xem **tiến trình đang chạy** — khi cần **giám sát** hoặc **quản lý** process.

*   **`ps -e`**: liệt kê **mọi** tiến trình trên hệ thống, **bất kể** user nào khởi chạy.
*   Đầu ra thường gồm: **tên** process, **PID**, thời gian chạy (phút/giây), …

## Tiến trình và tài nguyên: `top` (table of processes)

**`top`** giống **task manager**: bảng tiến trình và **mức dùng tài nguyên**.

*   Hữu ích khi cần theo dõi **hiệu năng** hoặc tìm process **ngốn** CPU/RAM.
*   Ví dụ trong video: **`top -n 3`** — hiển thị **ba** tác vụ hàng đầu (ví dụ Chrome, top, Spotify — tùy máy).
*   Mặc định thường **sắp xếp theo CPU**; `top` còn nhiều chi tiết khác (bộ nhớ, đường dẫn file thực thi, …) tùy phiên bản và cấu hình.

*(Lưu ý: cờ `-n` có thể khác nhau giữa `top` của BSD/macOS và procps trên Linux; dùng `man top` trên hệ của bạn.)*

## In chuỗi và biến: `echo`

**`echo`** đơn giản nhưng rất hữu ích để **in văn bản** hoặc **giá trị biến** ra terminal hoặc trong **shell script**.

*   Chỉ gõ **`echo`** không đối số: tương đương in **dòng trống** (xuống dòng).
*   **`echo hello`**: in từ `hello`.
*   Chuỗi có dấu cách: về mặt kỹ thuật đôi khi không bắt buộc **dấu ngoặc**, nhưng **nên** dùng **ngoặc kép** cho thói quen tốt và tránh lỗi.
*   **`echo 'Learning Linux is fun!'`**: in đúng nội dung trong ngoặc.
*   **`echo $PATH`**: in giá trị biến môi trường **`PATH`** (các thư mục cách nhau bởi **`:`**) — hữu ích khi **gỡ lỗi** hoặc viết script.

## Ngày và giờ: `date`

**`date`** hiển thị **ngày giờ** hiện tại của hệ thống.

*   **`date`** không định dạng: thường ra **thứ**, **ngày**, **tháng**, **năm**, **giờ**, **múi giờ**, …
*   **Định dạng tùy chỉnh**: dùng **`+`** rồi chuỗi gồm **văn bản** và **ký tự điều khiển** bắt đầu bằng **`%`** (đặt trong **ngoặc kép**).

Ví dụ khái niệm từ video:

*   **`%j`**: **số thứ tự ngày trong năm** (1–366).
*   **`%Y`**: **năm** bốn chữ số.

Ví dụ ý nghĩa: in ra kiểu **ngày thứ 97 trong năm**, chữ **"day of"**, và **năm** (video minh họa với năm 2023).

Có thể **kết hợp** nhiều `%` và chữ, ví dụ **`%A`** (tên đầy đủ của thứ trong tuần), **`%j`**, **`%Y`**, để in một dòng mô tả **thứ**, **ngày trong năm**, **năm**.

*(Chi tiết định dạng: xem `man date` trên máy bạn.)*

## Sổ tay lệnh: `man`

**`man`** (*manual*) mở **trang hướng dẫn** cho lệnh có sẵn trên hệ.

*   Ví dụ: **`man id`** — trang cho lệnh **`id`** (trên Linux tên lệnh thường **viết thường**).
*   Trang `man` thường có: **tóm tắt** lệnh làm gì (ví dụ *return user identity*), **danh sách tùy chọn** (ví dụ `-a`), **tham số tùy chọn** trong **`[...]`** (ví dụ chỉ định **username**).
*   Phần **mô tả dài** giải thích chi tiết hơn.
*   Bản thân **`man`** cũng có trang riêng: **`man man`** để học cách dùng hệ thống manual.

## Tổng kết (Summary)

Trong video này, bạn đã học cách:

*   Lấy thông tin user bằng **`whoami`** và **`id`**.
*   Lấy thông tin hệ điều hành bằng **`uname`**.
*   Kiểm tra dung lượng đĩa bằng **`df`** (kèm **`-h`** khi cần đơn vị dễ đọc).
*   Theo dõi tiến trình và tài nguyên với **`ps`** và **`top`**.
*   In chuỗi hoặc giá trị biến bằng **`echo`**.
*   In và **trích** thông tin ngày giờ với **`date`** và chuỗi định dạng sau **`+`**.
*   Đọc hướng dẫn cho mọi lệnh bằng **`man`**.

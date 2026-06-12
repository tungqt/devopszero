# Lệnh mạng (Networking Commands)

## Document Information
- **Doc ID:** 02-061
- **Summary:** **`hostname`** (`-s`, `-i`); **`ip a`** / **`ip addr show eth0`**; **`ping`** (ICMP, Ctrl+C, **`-c`**); **`curl`** (HTTP, **`-o`** ghi file); **`wget`** (tải URL, đặt tên file tự động, tải đệ quy).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh mạng". Sau khi xem video này, bạn sẽ có thể:

*   **Kiểm tra** cấu hình mạng của máy.
*   **Đánh giá** độ ổn định kết nối tới một URL/host.
*   **Nhận diện** và **lấy dữ liệu** từ URL.

*(Nền tảng khái niệm: xem bài tùy chọn `[02-060]` — packet, IP, URL, ping.)*

## Tên máy: `hostname`

**`hostname`** lấy hoặc (với quyền phù hợp) **đặt** tên host và thông tin định danh máy.

```bash
hostname
```

*   Ví dụ: `mylinuxmachine.local`
*   Hậu tố **`.local`** thường xuất hiện khi máy có **local domain**.

| Tùy chọn | Tác dụng |
|----------|----------|
| **`-s`** | Chỉ in **tên ngắn** (bỏ phần domain suffix). |
| **`-i`** | In **địa chỉ IP** gắn với hostname (hành vi có thể khác giữa distro; trên Linux hiện đại đôi khi dùng thêm `hostname -I` hoặc `ip a`). |

## Giao diện mạng: `ip`

**`ip`** là tiện ích mạnh để **cấu hình** và **hiển thị** thông tin giao diện mạng (thay thế / bổ sung cho `ifconfig` trên nhiều hệ Linux).

Xem **mọi** giao diện:

```bash
ip a
```

*(Tương đương `ip addr` — hiển thị địa chỉ.)*

*   Gồm **địa chỉ IP**, **MAC**, và chi tiết theo từng interface — hữu ích cho **quản trị viên** quản lý mạng.

Chi tiết **một** thiết bị (ví dụ Ethernet **`eth0`** — tên thực tế có thể là `enp0s3`, `wlan0`, …):

```bash
ip addr show eth0
```

*   IP, số **packet** nhận/gửi, **lỗi**, **dropped**, tổng dữ liệu, …

## Kiểm tra kết nối: `ping`

**`ping`** kiểm tra **kết nối** tới host hoặc địa chỉ IP.

*   Gửi gói **ICMP** (*Internet Control Message Protocol*) — **echo request**.
*   **Lắng nghe** phản hồi và **in kết quả**.

```bash
ping google.com
```

*   Mỗi phản hồi thành công → một dòng thông tin.
*   Chạy **liên tục** cho đến khi dừng bằng **Ctrl+C**.
*   Sau khi dừng: **thống kê tổng** — số gói gửi/nhận, % mất, min/avg/max/**stddev** thời gian **round-trip** (ms).

Thông tin hữu ích trên mỗi dòng (ví dụ):

*   **IP** của host (ví dụ `142.251.41.68` cho `google.com`).
*   **Thời gian round-trip** (milliseconds).

Giới hạn số lần ping:

```bash
ping -c 5 google.com
```

*   Gửi **5** echo request, **tự dừng**, rồi in **cùng loại thống kê** như khi dừng bằng Ctrl+C.

## Truyền dữ liệu qua URL: `curl`

**`curl`** (*client URL*) chuyển dữ liệu **tới/ từ URL**, hỗ trợ **nhiều giao thức** (HTTP, HTTPS, FTP, …).

In nội dung trang (HTML) ra terminal:

```bash
curl www.google.com
```

*   Mặc định thường dùng **HTTP** — thấy toàn bộ HTML landing page (đường dẫn ảnh PNG logo, …).

Ghi nội dung URL vào **file cục bộ**:

```bash
curl www.google.com -o google.txt
head google.txt
```

*   **`-o google.txt`**: lưu đầu ra vào file; dùng **`head`** để xác nhận nội dung khớp với lần `curl` in ra màn hình trước đó.

## Tải file từ URL: `wget`

**`wget`** dùng để **tải** tài nguyên tại URL.

| So với `curl` | Ghi chú |
|---------------|--------|
| Giống | Lấy **file** hoặc **HTML** tại URL. |
| Khác | **`wget`** thiên về **tải xuống**; hỗ trợ **tải đệ quy** (*recursive*) — hữu ích khi URL trỏ tới **thư mục** chứa nhiều file. |

Ví dụ tải file text từ w3.org:

```bash
wget https://www.w3.org/2001/tag/2001/iso8859-1/ISO_8859-1
```

*(URL và tên file trong video: `iso_8859-1.txt` — tên file lưu có thể do server/`wget` quyết định.)*

Trong khi tải, `wget` có thể hiển thị:

*   **Resolving** / **connecting** tới server  
*   **HTTP request** / chờ phản hồi  
*   **Saving** file — thường lưu vào **thư mục hiện tại** với tên do `wget` chọn

Xem nội dung đã tải:

```bash
head -12 iso_8859-1.txt
```

*   Nội dung file text **khớp** với dữ liệu trên trang web.

## Tổng kết (Summary)

Trong video này, bạn đã học:

*   **`hostname`**: lấy (hoặc đặt) **tên host**; **`-s`**, **`-i`** cho tên ngắn / IP.
*   **`ip`**: thông tin chi tiết **giao diện mạng** (`ip a`, `ip addr show <interface>`).
*   **`ping`**: kiểm tra **kết nối** host/IP (ICMP); **`-c`** giới hạn số lần.
*   **`curl`**: **chuyển dữ liệu** tới/ từ URL; **`-o`** ghi ra file.
*   **`wget`**: **tải** file (và có thể tải **đệ quy**) từ URL.

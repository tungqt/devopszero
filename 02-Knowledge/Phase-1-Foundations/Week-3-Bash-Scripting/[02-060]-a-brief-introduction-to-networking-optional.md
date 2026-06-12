# Giới thiệu ngắn về mạng máy tính (tùy chọn) (A Brief Introduction to Networking — Optional)

## Document Information
- **Doc ID:** 02-060
- **Summary:** Bài đọc **tùy chọn** về **mạng máy tính**, **tài nguyên mạng**, **node**; **host**, **client**, **server**; **packet** và **`ping`**; **IP address** và **URL** (protocol, hostname, path).
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập

Sau khi đọc xong, bạn sẽ có thể:

*   **Mô tả** mạng máy tính, tài nguyên mạng và network node.
*   **Giải thích** host, client và server.
*   **Mô tả** packet và lệnh ping.
*   **Giải thích** URL và địa chỉ IP.

## Mạng máy tính (Computer Networks)

**Mạng máy tính** (*computer network*) là tập các máy tính **có thể giao tiếp** với nhau và **chia sẻ tài nguyên** do các **network node** cung cấp.

Ví dụ:

*   **LAN** (*Local Area Network*) — mạng cục bộ.
*   **WAN** (*Wide Area Network*) — mạng diện rộng.
*   **Internet** — về bản chất là **mạng khổng lồ** gồm vô số mạng nhỏ hơn (World Wide Web).

### Tài nguyên mạng (network resource)

**Tài nguyên mạng** là mọi **đối tượng** (ví dụ **file**, **tài liệu**) mà mạng có thể **nhận diện**.

Đối tượng **có thể nhận diện** nếu được gán **tên** và **địa chỉ** duy nhất để mạng dùng để **truy cập**.

### Network node

**Network node** là **thiết bị tham gia** vào mạng — không nhất thiết là máy tính.

Ví dụ node hạ tầng: **modem**, **network switch**, **hub**, **điểm phát Wi‑Fi** (*wifi hotspot*).

## Host, client và server

**Host** là loại **node đặc biệt**: máy tính có thể đóng vai **server** hoặc **client** trên mạng.

| Vai trò | Mô tả ngắn |
|--------|------------|
| **Server** | Host **chấp nhận kết nối** từ client và **đáp ứng** yêu cầu tài nguyên (file, dịch vụ, …). |
| **Client** | Host **gửi yêu cầu** tới server. |

Nhiều host có thể **vừa client vừa server** tùy ngữ cảnh.

## Packet và ping

**Network packet** (*gói mạng*) là **khối dữ liệu có định dạng** truyền trên mạng.

Hầu hết giao thức hiện đại dựa trên **packet**. Mỗi gói gồm:

| Thành phần | Nội dung |
|------------|----------|
| **Control information** | Metadata: **cách** và **đi đâu** gửi payload — ví dụ địa chỉ **nguồn** và **đích**. |
| **Payload** | **Nội dung thông điệp** thực sự. |

### Lệnh `ping`

**`ping`** gửi các gói đặc biệt kiểu **echo request** tới một host và **chờ phản hồi**.

*   Có trên hầu hết hệ có **mạng**.
*   Trên **Linux**, `ping` dùng để **kiểm tra** và **gỡ lỗi** kết nối tới host khác.

*(Liên quan bài shell: `ping` đã được nhắc trong tổng quan lệnh mạng — xem `[02-052]`.)*

## URL và địa chỉ IP

### IP (Internet Protocol)

**IP** (*Internet Protocol*) định nghĩa **định dạng dữ liệu** truyền trên Internet hoặc mạng cục bộ.

**Địa chỉ IP** là mã **định danh duy nhất** một **host** trên mạng.

*   Dùng để **thiết lập kết nối** và **trao đổi packet** (ví dụ qua `ping`).
*   **IP packet** — gói tuân theo Internet Protocol — mang **địa chỉ IP nguồn và đích** ngoài payload.

### URL (Uniform Resource Locator)

**URL** (địa chỉ web) **định danh duy nhất** một **tài nguyên web** và cho phép **truy cập** tài nguyên đó.

*   Thường trỏ tới **trang web**, nhưng URL còn dùng cho **chuyển file**, **email**, **cơ sở dữ liệu**, …

Ví dụ trang Wikipedia về URL:

[https://en.wikipedia.org/wiki/URL](https://en.wikipedia.org/wiki/URL)

Cấu trúc điển hình:

| Phần | Ví dụ | Ý nghĩa |
|------|--------|---------|
| **Protocol** | `https` | Giao thức truy cập |
| **Hostname** | `en.wikipedia.org` | Máy chủ / tên miền |
| **Path / file** | `/wiki/URL` | Đường dẫn tài nguyên trên host |

*(Lệnh **`curl`** / **`wget`** trong shell dùng URL để lấy nội dung — xem `[02-052]`.)*

## Tổng kết (Summary)

Trong bài đọc này, bạn đã học:

*   **Mạng máy tính** = các máy **giao tiếp** và **chia sẻ tài nguyên**; **tài nguyên mạng** = đối tượng mạng nhận diện được; **node** = thiết bị tham gia mạng.
*   **Host** = máy có thể là **client** hoặc **server**; **server** nhận kết nối client và **phục vụ** yêu cầu tài nguyên.
*   **Packet** = đơn vị dữ liệu có định dạng; **`ping`** gửi **echo request** và chờ phản hồi.
*   **Địa chỉ IP** định danh host; **URL** định danh và cho phép truy cập **tài nguyên web** (và các loại tài nguyên khác).

# Tổng kết module & điểm nhấn (Module Summary & Highlights)

## Document Information
- **Doc ID:** 02-063
- **Summary:** Tổng hợp kiến thức sau khi hoàn thành module **lệnh shell Linux thường dùng** — shell, thông tin hệ thống, điều hướng file/thư mục, xem & xử lý văn bản, mạng, nén/lưu trữ.
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chúc mừng bạn đã hoàn thành module này. Tại thời điểm này, bạn đã nắm được các nội dung cốt lõi sau.

## Shell & điều hướng cơ bản

**Shell** là giao diện tương tác dòng lệnh. Bạn dùng lệnh shell để **điều hướng** và **làm việc** với file và thư mục.

| Chủ đề | Lệnh / khái niệm |
|--------|------------------|
| Liệt kê & chuyển thư mục | `ls`, `cd` |
| Tìm file | `find` |
| Đường dẫn | **Tương đối** — tính từ thư mục làm việc hiện tại; **tuyệt đối** — độc lập, bắt đầu từ gốc (`/`) |

*(Chi tiết: `[02-052]`, `[02-055]`.)*

## Thông tin hệ thống & trợ giúp

| Mục đích | Lệnh |
|----------|------|
| Người dùng hiện tại | `whoami`, `id` |
| Hệ điều hành | `uname` |
| Dung lượng đĩa | `df` |
| Tiến trình & tài nguyên | `ps`, `top` |
| In chuỗi / biến | `echo` |
| Ngày giờ | `date` |
| Hướng dẫn lệnh | `man` |

*(Chi tiết: `[02-053]`, `[02-054]`.)*

## Tải xuống, tạo, sao chép & xóa

| Mục đích | Lệnh |
|----------|------|
| Hiển thị / tải từ URL | `curl`, `wget` |
| Tạo file & thư mục | `touch`, `mkdir` |
| Xóa | `rm`, `rmdir` |
| Sao chép & di chuyển | `cp`, `mv` |

*(Chi tiết: `[02-056]`; quyền sở hữu: `[02-057]`.)*

## Xem nội dung file

| Mục đích | Lệnh |
|----------|------|
| In toàn bộ hoặc cuối file | `cat`, `tail` |
| Xem có phân trang / đầu–cuối file | `more`, `head`, `tail` |
| Đếm dòng, từ, ký tự | `wc` |

*(Chi tiết: `[02-058]`.)*

## Xử lý & trích xuất văn bản

| Mục đích | Lệnh |
|----------|------|
| Sắp xếp dòng theo thứ tự chữ-số | `sort` |
| Loại bỏ dòng trùng lặp (trên luồng đã sắp xếp) | `uniq` |
| Lọc dòng khớp mẫu | `grep` |
| Cắt cột / trường | `cut` |
| Ghép dòng từ nhiều file | `paste` |

*(Chi tiết: `[02-059]`.)*

## Mạng

| Mục đích | Lệnh |
|----------|------|
| Cấu hình mạng | `hostname`, `ip` |
| Kiểm tra kết nối | `ping` |
| Gửi / nhận dữ liệu qua URL | `curl`, `wget` |

*(Nền tảng: `[02-060]`; thực hành: `[02-061]`.)*

## Nén & lưu trữ (archive)

**Nén** giúp tiết kiệm dung lượng, tăng tốc truyền dữ liệu và giảm tải hệ thống.

| Thao tác | Lệnh / ghi chú |
|----------|----------------|
| Nén thư mục/file thành `.zip` | `zip` |
| Archive (và thường kèm nén gzip) thành tarball | `tar` (ví dụ `.tar.gz`) |
| Giải nén `.zip` | `unzip` |
| Giải nén / giải archive tarball | `tar -xzf` (và các tùy chọn tương ứng) |

*(Chi tiết: `[02-062]`.)*

## Bản đồ bài học trong module

```text
[02-052] Shell & tổng quan lệnh
[02-053] Lệnh thông tin
[02-054] man / trợ giúp
[02-055] Điều hướng thư mục
[02-056] Quản lý file & thư mục
[02-057] Quyền & sở hữu (bảo mật)
[02-058] Xem nội dung file
[02-059] Xử lý văn bản
[02-060] Mạng (tùy chọn, lý thuyết)
[02-061] Lệnh mạng
[02-062] Archive & nén
[02-063] Tổng kết module (tài liệu này)
```

## Ghi chú DevOps

*   **`curl` / `wget`**: thường dùng khi script hoặc pipeline cần tải artifact, manifest, hoặc kiểm tra endpoint HTTP.
*   **`df` / `ps` / `top`**: điểm khởi đầu khi máy “đầy đĩa” hoặc “chậm” trước khi đào sâu log/metrics.
*   **`tar` / `zip`**: chuẩn khi đóng gói backup, bundle cấu hình, hoặc chuẩn bị artifact triển khai.
*   Kết hợp **`grep`**, **`sort`**, **`uniq`**, **`cut`**: mô hình xử lý log và CSV trên terminal — nền tảng trước khi chuyển sang awk/sed nâng cao.

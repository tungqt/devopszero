# Sử dụng dpkg quản lý gói phần mềm (Demo)

## Document Information
- **Doc ID:** 02-030
- **Summary:** Bài lab hướng dẫn thực hành cách sử dụng lệnh `dpkg` (công cụ cấp thấp trên họ Debian/Ubuntu) để liệt kê gói, truy vấn thông tin, xem file và kiểm thử gỡ bỏ một gói phần mềm (Ví dụ: `bzip2`), nhằm thấy rõ giới hạn xử lý phụ thuộc (Dependency) của nó.
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

Tương tự như công cụ `rpm` trên Red Hat, **`dpkg` (Debian Package)** là công cụ dòng lệnh cấp thấp (low-level command) nền tảng của toàn bộ hệ sinh thái Debian/Ubuntu.

Bài thực hành này giúp chúng ta làm quen với cú pháp cơ bản của `dpkg` thông qua việc tìm hiểu về gói `bzip2`. Lưu ý rằng trong thực tế, các SysAdmin hiếm khi sử dụng `dpkg` trực tiếp để cài đặt hoặc gỡ bỏ phần mềm (tránh Dependency Hell), mà thay vào đó họ sử dụng công cụ cấp cao `apt-get` hoặc `apt`.

---

## 2. Truy vấn Thông tin Gói (Querying Packages)

### 2.1. Liệt kê tất cả các gói đang cài trên hệ thống
Để xem một danh sách bao gồm tất cả các phần mềm hiện đang nằm trên máy của bạn:
```bash
dpkg --list
```
Vì danh sách này rất dài, bạn nên sử dụng pipe (`|`) kết hợp với lệnh `less` để dễ đọc, hoặc dùng `grep` để tìm chính xác gói mong muốn:
```bash
dpkg --list | less
dpkg --list | grep bzip2
```
*Kết quả sẽ hiển thị một bảng tóm tắt về gói `bzip2`, bao gồm:*
- Tên gói (`bzip2`)
- Phiên bản (`1.0.6...`)
- Kiến trúc (`amd64` - dành cho chip 64-bit)
- Mô tả ngắn gọn ("high-quality block-sorting file compressor").

### 2.2. Liệt kê các file nằm bên trong gói (List Files)
Để biết chính xác khi phần mềm `bzip2` được cài đặt, nó đã đưa những file nào vào cấu trúc thư mục của Linux (Ví dụ: file chạy ở `/bin`, tài liệu ở `/usr/share/doc`):
```bash
dpkg --listfiles bzip2
# Hoặc kết hợp với less để đọc từ từ
dpkg --listfiles bzip2 | less
```
Việc này rất hữu ích khi bạn không biết một phần mềm cài xong thì lệnh thực thi (executable) của nó nằm ở đâu.

---

## 3. Thử nghiệm Gỡ bỏ Gói (Remove) và Vấn đề Dependency

Cũng giống như bài lab về `rpm`, chúng ta sẽ thử gỡ bỏ gói `bzip2` để xem công cụ cấp thấp này phản ứng ra sao.
*(Lưu ý: Mọi thao tác làm thay đổi hệ thống đều yêu cầu quyền root - `sudo`).*

```bash
sudo dpkg --remove bzip2
```

**Kết quả nhận được:**
Hệ thống sẽ từ chối và báo lỗi! Lệnh sẽ liệt kê một số gói khác đang phụ thuộc vào nó, ví dụ như:
- `dpkg-dev`: Gói thư viện phát triển.
- `file-roller`: Công cụ giải nén giao diện đồ họa phổ biến trên Linux.

`dpkg` nói với bạn rằng: *"Nếu muốn gỡ `bzip2`, bạn phải tự tay gỡ cả những phần mềm đang bám vào nó kia nữa"*. Nó **không** tự động gỡ các phần mềm phụ thuộc đi kèm. 

---

## 4. Tổng kết (Takeaway)

- `dpkg` là một công cụ xuất sắc để truy vấn thông tin hệ thống (xem gói nào đã cài, file nào thuộc về gói nào).
- Tuy nhiên, khi nói đến việc thao tác (Install/Remove), `dpkg` gặp phải hạn chế lớn giống hệt `rpm`: Nó không tự động xử lý rủi ro về mặt **Dependency**.
- Để giải quyết bài toán cài đặt / gỡ bỏ một cách thông minh và trơn tru hơn, ở bài tiếp theo chúng ta sẽ chuyển sang sử dụng công cụ cấp cao hơn là `apt-get` (hoặc `apt`).
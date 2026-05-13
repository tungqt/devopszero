# Thư mục Người dùng và Biến Môi trường (User Directories & Environments)

## Document Information
- **Doc ID:** 02-033
- **Summary:** Tìm hiểu về cấu trúc thư mục Home của người dùng trên Linux và các hệ điều hành UNIX-like khác, cơ chế Automount (NFS) trên mạng doanh nghiệp, và khái niệm về biến môi trường `$HOME`.
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Thư mục Home (Home Directory) trên Linux

Khác với Windows thường đặt dữ liệu người dùng tại `C:\Users\Tên_Người_Dùng`, Linux quy chuẩn một cấu trúc rất rõ ràng:
- **Thư mục Home mặc định:** Trên hệ thống Linux, các thư mục cá nhân của người dùng được đặt mặc định dưới thư mục gốc là `/home`.
  - Ví dụ: `/home/coop`, `/home/student`, `/home/tung`...
- **Chức năng:** Mọi cấu hình cá nhân (personal configuration), dữ liệu (data), và đôi khi cả các chương trình thực thi (executable programs) riêng của người dùng đó đều được lưu trữ trong hệ thống cây thư mục này. Người dùng thông thường (không phải root) chỉ có toàn quyền (Read/Write/Execute) trong chính thư mục Home của mình.

---

## 2. Sự khác biệt trên các hệ thống UNIX-like khác

Mặc dù khái niệm "Home directory" là tiêu chuẩn trên mọi hệ điều hành giống UNIX (UNIX-like), nhưng vị trí thực tế có thể khác nhau tùy thuộc vào truyền thống của từng hệ điều hành.

- **Ví dụ trên Solaris (Một hệ điều hành UNIX nổi tiếng của Sun Microsystems):**
  - Thư mục người dùng thường được tạo vật lý tại `/export/home`.
  - Tại sao lại là `export`? Từ khóa này ám chỉ rằng thư mục này được thiết kế để "xuất" (chia sẻ) qua mạng.

---

## 3. Cơ chế Automount và Mạng Doanh nghiệp (NFS)

Tại sao lại có sự rườm rà như việc đặt ở `/export/home` rồi ánh xạ (mount) về `/home`?
Điều này bắt nguồn từ nhu cầu thực tế của các mạng doanh nghiệp (Corporate Network) quy mô lớn:

- Trong một công ty, bạn có thể đăng nhập vào hàng chục máy trạm (workstations) khác nhau. Sẽ rất phi lý nếu mỗi máy lại lưu một bản sao dữ liệu của bạn.
- **Giải pháp:** Thư mục Home của toàn bộ nhân viên thực chất không nằm trên ổ cứng của các máy trạm, mà nằm tập trung trên một **Máy chủ Lưu trữ Mạng (NFS Server - Network File System)**.
- **Automount (Tự động gắn kết):** Bất cứ khi nào bạn đăng nhập vào một máy trạm, tính năng Automount của hệ điều hành sẽ tự động kết nối (mount) thư mục của bạn từ NFS Server về thư mục ảo `/home` trên máy đó. Quá trình này diễn ra hoàn toàn trong suốt (seamless) với người dùng.
- *Lưu ý:* Linux cũng có đầy đủ tính năng Automount này, nhưng với các hệ thống cá nhân chạy độc lập (self-contained systems) hoặc máy ảo đơn lẻ, người dùng thường không nhận ra và khái niệm NFS Mount không được áp dụng.

---

## 4. Biến môi trường `$HOME` và Dấu Tilde (`~`)

Hệ thống cung cấp các lối tắt cực kỳ tiện lợi để người dùng truy cập nhanh vào thư mục Home của họ mà không cần gõ toàn bộ đường dẫn tuyệt đối (Absolute path).

Hai cách viết sau đây là **hoàn toàn tương đương nhau**:
```bash
ls -l $HOME/public_html
ls -l ~/public_html
```

- **`$HOME`:** Là một biến môi trường (Environment Variable). Dù bạn đăng nhập bằng user nào, biến này cũng tự động chứa đường dẫn đến thư mục Home của user đó.
- **`~` (Dấu ngã - Tilde):** Là một ký hiệu viết tắt (shorthand) được Shell (như bash, zsh) tự động dịch ra thành đường dẫn thư mục Home.

---

## 5. Các Ngoại lệ Quan trọng (Exceptions)

Nguyên tắc "mọi user đều nằm trong `/home`" có một số ngoại lệ cốt lõi mà bạn buộc phải nhớ:

1. **Tài khoản Siêu quản trị (`root`):**
   - Thư mục Home của user `root` trên hệ thống Linux LUÔN LUÔN được đặt tại: **`/root`** (Trực thuộc ngay dưới thư mục gốc `/`).
   - Lý do: Tài khoản root cần được đảm bảo luôn có thể truy cập được dữ liệu của mình ngay cả khi phân vùng `/home` (thường được gắn từ một ổ cứng khác hoặc qua mạng) bị lỗi hoặc chưa kịp mount.

2. **Các tài khoản Hệ thống (System-provided accounts):**
   - Các tài khoản được tạo ra không phải cho người thật (Ví dụ: `daemon`, `bin`, `sshd`, `www-data`...).
   - Thư mục Home của các tài khoản này thường được trỏ về các vị trí khác không thuộc `/home` (Ví dụ: `/var/www`, `/bin`, hoặc `/nonexistent`) tùy thuộc vào mục đích dịch vụ của chúng.
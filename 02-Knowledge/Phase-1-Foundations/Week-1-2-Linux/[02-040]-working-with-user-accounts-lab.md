# Thực hành làm việc với Tài khoản Người dùng (Lab)

## Document Information
- **Doc ID:** 02-040
- **Summary:** Bài thực hành (Lab) hướng dẫn các bước tạo tài khoản, đặt mật khẩu, kiểm tra file cấu hình và thiết lập shell mặc định cho người dùng.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

## Yêu cầu thực hành

1.  **Tạo tài khoản:** Tạo một tài khoản có tên `user1` bằng cách sử dụng lệnh `useradd`.

2.  **Thử đăng nhập:** Đăng nhập vào tài khoản `user1` bằng lệnh `ssh`, `su` hoặc `sudo`. Ví dụ, bạn có thể thử thực hiện việc này bằng cách chạy:
    ```bash
    $ ssh user1@localhost
    ```
    *Lưu ý: Lệnh này sẽ thất bại, vì hệ thống yêu cầu mật khẩu cho `user1`, nhưng bạn chưa từng thiết lập nó.*

3.  **Thiết lập mật khẩu:** Đặt mật khẩu cho `user1` là `user1pw`, sau đó thử đăng nhập lại với tư cách `user1`.

4.  **Kiểm tra thay đổi:** Xem các bản ghi mới vừa được tạo và thêm vào các tập tin cấu hình `/etc/passwd` và `/etc/group`.

5.  **Khám phá cấu hình mặc định:** 
    - Xem nội dung tập tin `/etc/default/useradd` để biết các giá trị cấu hình mặc định hiện tại đang được thiết lập là gì. 
    - Đồng thời, xem thêm nội dung của tập tin `/etc/login.defs`.

6.  **Tạo người dùng với tuỳ chọn bổ sung:** 
    - Tạo một tài khoản người dùng mới cho `user2` và thiết lập sử dụng **Korn shell (ksh)** làm shell mặc định (Thay vì bash shell như thông thường). 
    - *(Nếu hệ thống của bạn không có `/bin/ksh`, hãy cài đặt nó trước hoặc sử dụng C shell tại đường dẫn `/bin/csh` thay thế).* 
    - Thiết lập mật khẩu cho tài khoản này thành `user2pw`.
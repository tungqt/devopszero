# Tạo, Sửa đổi và Xóa Tài khoản Người dùng (Demo)

## Document Information
- **Doc ID:** 02-037
- **Summary:** Thực hành chi tiết các bước tạo, kiểm tra, sửa đổi và xóa tài khoản người dùng mới trên Ubuntu.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

Hãy cùng thực hành việc tạo, sửa đổi và xóa một tài khoản người dùng mới. Chúng ta sẽ thực hiện việc này trên Ubuntu (trong ví dụ là Ubuntu 17.04). 

Có một số khác biệt giữa các bản phân phối Linux về những gì chính xác được tạo ra cùng với một tài khoản mới và những tập tin nào sẽ xuất hiện mặc định. Điều này phần lớn được kiểm soát bởi một tập tin nằm ở `/etc/default/useradd`. Ví dụ, trong tập tin này, bạn có thể thấy shell mặc định được thiết lập là `SHELL=/bin/sh`. Trên một số bản phân phối như Red Hat, tập tin này khá ngắn, nhưng trên Ubuntu thì nó khá dài và chi tiết.

## 1. Tạo tài khoản người dùng mới

Chúng ta sẽ tạo tài khoản bằng lệnh `useradd`.
*   Tùy chọn `-m` để đảm bảo hệ thống tạo thư mục nhà (home directory). Một số bản phân phối (như Ubuntu và openSUSE) không tự động làm điều này theo mặc định.
*   Tùy chọn `-c` để chỉ định họ tên đầy đủ (ở đây là "Eric Dolphy").
*   Tùy chọn `-s` để chỉ định shell mặc định (chọn `/bin/bash`).
*   Tên người dùng là `edolphy`.

Câu lệnh hoàn chỉnh:
```bash
sudo useradd -m -c "Eric Dolphy" -s /bin/bash edolphy
```

Tuy nhiên, tài khoản này vẫn chưa thể dùng được vì chưa có mật khẩu. Chúng ta thiết lập mật khẩu bằng lệnh:
```bash
sudo passwd edolphy
```
Sau đó, bạn gõ mật khẩu 2 lần theo lời nhắc của hệ thống. Bây giờ tài khoản đã hoàn toàn tồn tại.

## 2. Kiểm tra thông tin tài khoản

Bạn có thể xác minh tài khoản vừa tạo bằng cách xem các mục nhập đã được thêm vào `/etc/passwd` và `/etc/group` hay chưa:
```bash
grep edolphy /etc/passwd /etc/group
```

Kết quả sẽ cho thấy User ID (UID) của người dùng mới là `1001`. Hãy nhớ, ID của người dùng bình thường bắt đầu từ `1000` (ví dụ tài khoản mặc định `student` thường có ID `1000`), do đó ID `1001` là hoàn toàn hợp lý. Các thông tin khác bạn đã chỉ định cũng sẽ hiển thị: họ tên đầy đủ, shell `/bin/bash` và Group ID (GID) cũng là `1001` (trùng với UID). Mọi người dùng khi được tạo đều thuộc về ít nhất một nhóm chính mang tên và số ID giống với chính họ.

## 3. Đăng nhập và Thư mục `/etc/skel`

Hãy thử đăng nhập vào tài khoản đó thông qua SSH:
```bash
ssh edolphy@localhost
```

Sau khi nhập mật khẩu và đăng nhập thành công, bạn có thể kiểm tra xem có những tập tin gì trong thư mục nhà bằng lệnh `ls -la`. Bạn sẽ thấy không có nhiều tập tin, nhưng đây là những tập tin cấu hình mặc định (như `.bashrc`, `.profile`) mà mọi người dùng mới đều nhận được. 

Bạn có thể `exit` (đăng xuất). Những tập tin mặc định này thực chất được chép từ thư mục `/etc/skel` (skel là viết tắt của skeleton - bộ khung). Nếu bạn kiểm tra thư mục này:
```bash
ls -la /etc/skel
```
Bạn sẽ thấy nội dung bên trong. Bất cứ tập tin nào bạn đặt vào thư mục `/etc/skel` này đều sẽ tự động xuất hiện trong thư mục nhà của bất kỳ người dùng mới nào được tạo sau đó.

## 4. Xóa tài khoản

Bây giờ hãy dọn dẹp bằng cách xóa tài khoản vừa tạo:
```bash
sudo userdel -r edolphy
```

Tùy chọn `-r` (remove) là bắt buộc nếu bạn muốn xóa luôn thư mục nhà của người dùng này. Khi chạy lệnh, bạn có thể thấy một thông báo cảnh báo nhỏ về việc không tìm thấy/xóa tập tin cuộn thư (mail spool file), nhưng cảnh báo này hoàn toàn vô hại.

Bạn có thể kiểm tra lại thư mục `/home`:
```bash
ls -l /home
```
Thư mục `edolphy` đã biến mất, và tài khoản đã được dọn dẹp sạch sẽ.
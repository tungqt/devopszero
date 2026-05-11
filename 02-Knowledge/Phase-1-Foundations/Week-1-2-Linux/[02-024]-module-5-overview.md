# Tổng quan Module 5: Quản trị Hệ thống và Người dùng (System & User Management)

## Document Information
- **Doc ID:** 02-024
- **Summary:** Tổng quan nội dung của Module 5, bao gồm các chủ đề về cài đặt hệ điều hành, quản lý gói phần mềm (Package Management), quản lý người dùng/nhóm (User/Group) và phân quyền siêu quản trị (su vs sudo).
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

Trong Module 5, chúng ta sẽ tiếp tục đi sâu vào các khía cạnh cốt lõi của việc Quản trị Hệ thống (System Administration), chia làm hai mảng kiến thức cực kỳ quan trọng đối với bất kỳ DevOps hay SysAdmin nào: **Quản lý Phần mềm** và **Quản lý Người dùng**.

---

## 2. Quản trị Hệ thống và Gói Phần mềm (System & Package Management)

### Cài đặt Linux (Installing Linux)
- Thảo luận về các phương thức cài đặt Linux khác nhau.
- Sự khác biệt trong cách đóng gói và phân phối của các hệ điều hành (Distributions).
- Lựa chọn phương pháp cài đặt: qua mạng (network installation) hay sử dụng thiết bị lưu trữ vật lý (USB/Media).

### Quản lý Gói Phần mềm (Package Management)
Đây là một chủ đề trọng tâm của Linux. Thay vì tải file `.exe` như trên Windows, Linux quản lý phần mềm một cách tập trung và bài bản hơn:
- Mỗi bản phân phối đều có một cơ chế (method) riêng để nâng cấp hệ thống định kỳ.
- Cách áp dụng các bản vá lỗi (patches) hoặc cập nhật tính năng bảo mật.
- Cách cài đặt (install) và gỡ bỏ (remove) từng phần mềm riêng lẻ.
- Quản lý sự phụ thuộc (dependencies): Đảm bảo hệ thống giải quyết tốt các xung đột (collisions/contradictions) khi cài đặt nhiều phần mềm cần chung một thư viện.

---

## 3. Quản lý Người dùng và Nhóm (User and Group Management)

### Quản lý Người dùng (User Management)
- Điều gì thực sự xảy ra dưới nền hệ thống (under the hood) khi bạn thêm một User mới?
- Cách cấp phát (provision) một tài khoản.
- Những tính năng, tệp tin và cấu hình nào được tạo tự động (automatically), và những gì quản trị viên phải cấu hình thủ công sau đó.
- Quy trình xóa bỏ người dùng ra khỏi hệ thống một cách an toàn.

### Quản lý Nhóm (Group Management)
- Khái niệm về Nhóm: Mỗi người dùng (user) sẽ thuộc về một hoặc nhiều nhóm (groups).
- Mục đích của Group là tập hợp những người dùng có chung trách nhiệm, nhiệm vụ và cần chia sẻ chung một tài nguyên.
- **Phân quyền truy cập (Access Rights):** Thay vì cấp quyền file/thư mục cho từng cá nhân, Linux cho phép quản trị viên cấp quyền theo cấp độ Nhóm (Group basis) giúp việc quản lý hệ thống lớn trở nên dễ dàng hơn.

---

## 4. Đặc quyền Siêu Quản trị (The Superuser: `su` vs `sudo`)

Khái niệm về tài khoản tối cao (Superuser/Root) trên Linux, và cách sử dụng các đặc quyền này một cách an toàn. Bài học sẽ tập trung phân biệt rõ hai câu lệnh:
- **Lệnh `su` (Substitute User):** Cho phép người dùng chuyển đổi hoàn toàn sang tài khoản của Superuser, có toàn quyền làm mọi thứ trên hệ thống (Rủi ro cao).
- **Lệnh `sudo` (Superuser DO):** Phương pháp an toàn và tối ưu hơn. Nó chỉ cấp đặc quyền nâng cao (escalating privilege) cho **một câu lệnh duy nhất** hoặc một nhóm các lệnh được cấu hình sẵn. `sudo` cũng giúp hệ thống ghi log (audit) lại mọi hành động mà người dùng đã thực thi dưới quyền root.
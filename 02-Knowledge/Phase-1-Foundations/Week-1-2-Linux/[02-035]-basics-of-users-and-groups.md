# Kiến thức Cơ bản về Người dùng và Nhóm (Users and Groups)

## Document Information
- **Doc ID:** 02-035
- **Summary:** Khái niệm cơ bản về ID của người dùng và nhóm, các tập tin /etc/passwd và /etc/group.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

Hãy cùng xem qua các khái niệm cơ bản về người dùng và nhóm trên hệ thống Linux.

## User IDs (ID Người dùng)
Mỗi người dùng trên hệ thống đều được chỉ định một User ID (UID) duy nhất, thực chất chỉ là một số nguyên, và bạn cũng nhận được một Group ID (GID) (cũng là một số nguyên).

Hiện nay, tất cả các hệ thống Linux hiện đại đều lưu trữ người dùng thông thường với ID bắt đầu từ 1,000 và tăng dần. Vì vậy, người dùng đầu tiên khi cài đặt hệ thống sẽ là 1,000, người dùng tiếp theo được thêm vào sẽ là 1001, v.v. Các hệ thống Linux rất cũ từ Red Hat bắt đầu với 500, nhưng bây giờ tất cả đều sử dụng 1,000.

User ID dưới 1,000 dành cho một số người dùng hệ thống nhất định có mục đích đặc biệt, và người dùng siêu cấp (superuser - `root`) có User ID là 0.

## Các Tập tin Quan trọng

Có những tập tin rất quan trọng trên hệ thống nằm trong thư mục `/etc`: `/etc/passwd` và `/etc/group`.

### Tập tin `/etc/passwd`
Hãy cùng xem tập tin `/etc/passwd` trước. Tập tin này chứa một loạt các dòng, mỗi dòng xác định một người dùng khác nhau. Dòng đầu tiên có thể dành cho một người tên là George Metesky. Hãy xem các trường cụ thể trong tập tin này là gì:

*   **Tên tài khoản (Account Name):** Trường đầu tiên là tên tài khoản. Đây là tên bạn dùng để đăng nhập.
*   **Trình giữ chỗ mật khẩu (Password Placeholder):** Trường thứ hai (thường là chữ `x`) là trình giữ chỗ cho mật khẩu. Đây thực sự chỉ là nơi giữ chỗ cho mật khẩu đã được mã hóa. Có những tập tin khác trong thư mục `/etc` chứa dạng mã hóa của mật khẩu thực sự (như `/etc/shadow`). Trong các hệ thống Unix cũ, trường này thực sự chứa mật khẩu (có thể là văn bản thuần túy hoặc đã mã hóa), nhưng bây giờ tính bảo mật đã tốt hơn. Mật khẩu không thực sự được lưu trong tập tin này vì nó khá dễ để các người dùng khác trên hệ thống nhìn thấy.
*   **User ID (UID):** Trường tiếp theo là ID người dùng, ở đây là 1,000.
*   **Group ID (GID):** Trường tiếp theo là ID của nhóm chính mà người dùng thuộc về.
*   **Họ và tên (Full Name - GECOS):** Sau đó là trường tên. Trường này không nhất thiết phải là tên thật. Trường này có một lịch sử phức tạp, nhưng trong hầu hết các hệ thống ngày nay, nó chỉ đơn giản là họ và tên đầy đủ của người dùng.
*   **Thư mục nhà (Home Directory):** Tiếp theo, ở đây `/home/george` sẽ là thư mục nhà (home directory) của người dùng.
*   **Shell mặc định (Default Shell):** Sau đó, `/bin/bash` sẽ là shell mặc định cho người dùng đó. Nó có thể là bash, csh, hoặc ksh.

Nếu bạn xem qua `/etc/passwd`, bạn sẽ thấy rất nhiều dòng khác ngoài người dùng bình thường. Bạn sẽ thấy các người dùng chuyên biệt trên hệ thống được sử dụng bởi nhiều tiện ích khác nhau (ví dụ: một người dùng tên là `ftp`). Sẽ có nhiều người dùng khác nhau liên kết với các daemon (trình nền) chạy ngầm trên hệ thống, v.v. Hầu hết trong số họ sẽ không có shell đăng nhập; trường này chỉ ghi là `nologin` (hoặc tương tự) thay vì `/bin/bash`.

### Tập tin `/etc/group`
Tập tin thứ hai khá đơn giản: tập tin `/etc/group`. Nó chỉ chứa tên nhóm, trình giữ chỗ mật khẩu của nhóm, GID và danh sách những người dùng trong nhóm đó.

Nhóm (Groups) là gì? Đúng như tên gọi: chúng là các nhóm người dùng có chung một số mối quan tâm hay lợi ích. Có thể họ đang cùng làm việc trong một dự án, tất cả đều là sinh viên, tất cả là giảng viên, hoặc tất cả đều ở cùng một phòng ban, và họ muốn chia sẻ một số tài nguyên (thường là các tập tin).

Đối với bất kỳ người dùng nào, có ít nhất một nhóm chỉ dành riêng cho họ (nhóm chính - primary group), trong đó Group ID giống hệt với User ID. Nhưng sau đó, họ cũng có thể thuộc về các nhóm khác. Những thông tin đó sẽ được liệt kê dưới `/etc/group`, và chúng ta sẽ thảo luận về cách thiết lập các đặc quyền dựa trên tư cách thành viên nhóm sau.

Vậy, đó chỉ là cái nhìn lướt qua về một số khái niệm cơ bản về ID của nhóm và người dùng, cũng như những gì có trong các tập tin ở thư mục `/etc` dùng để mô tả người dùng và nhóm.
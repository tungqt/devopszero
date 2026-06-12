# Tập tin, Người dùng và Quyền hạn (Files, Users, and Permissions)

## Document Information
- **Doc ID:** 02-038
- **Summary:** Kiến thức cơ bản về quyền truy cập tập tin, quyền sở hữu (owner, group, world) và Access Control Lists (ACLs) trong Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

Giả sử bạn có một tập tin và muốn xem danh sách chi tiết các thuộc tính của nó:

```bash
$ ls -lF some_file
-rwxr-x--x 1 coop coop 42 Jun 18 13:59 some_file*
```

Sau dấu gạch ngang đầu tiên, có chín chữ cái, được chia thành ba nhóm, mỗi nhóm ba chữ cái, biểu thị quyền đọc (read - `r`), ghi (write - `w`) và thực thi (execute - `x`) cho **chủ sở hữu (owner)**, **nhóm (group)** và **những người khác (world/others)**. Trong ví dụ trên:
- Chủ sở hữu của tập tin có thể đọc, ghi và thực thi (`rwx`).
- Tất cả các thành viên trong nhóm có thể đọc hoặc thực thi (`r-x`).
- Tất cả những người khác (world) chỉ có thể thực thi (`--x`).

Các quyền truy cập tập tin này là một phần cực kỳ quan trọng trong hệ thống bảo mật của Linux. Bất kỳ yêu cầu truy cập nào vào một tập tin đều đòi hỏi phải so sánh thông tin xác thực và danh tính của người dùng đang yêu cầu với thông tin của chủ sở hữu tập tin đó.

Việc cấp quyền này được thực hiện dựa trên một trong ba tập hợp quyền sau đây, theo thứ tự:

1.  Nếu người yêu cầu là **chủ sở hữu tập tin (owner)**, các quyền của chủ sở hữu sẽ được áp dụng.
2.  Nếu không, nếu người yêu cầu thuộc **nhóm (group)** sở hữu tập tin, các quyền của nhóm sẽ được kiểm tra.
3.  Nếu không thỏa mãn cả hai điều kiện trên, các quyền của **những người khác (world)** sẽ được kiểm tra.

*Lưu ý rằng quyền hạn có thể được thay đổi bằng lệnh `chmod` và quyền sở hữu có thể được thay đổi bằng lệnh `chown`.*

Có một người dùng đặc biệt: người dùng siêu cấp (superuser) hoặc `root`. Người dùng này có quyền truy cập vào tất cả các tập tin trên hệ thống. Điều này về cơ bản tương đương với tài khoản hoặc đặc quyền quản trị viên (administrator) trong các hệ điều hành khác.

Linux bao gồm một bản triển khai đầy đủ của **POSIX ACLs (Access Control Lists - Danh sách kiểm soát truy cập)**, giúp mở rộng mô hình quyền hạn đơn giản gồm owner/group/world và read/write/execute ở trên.

Các đặc quyền cụ thể có thể được cấp cho những người dùng cụ thể hoặc các nhóm người dùng cụ thể khi truy cập vào một số đối tượng hoặc các lớp đối tượng nhất định.

Mặc dù nhân Linux (kernel) cho phép sử dụng ACL, nhưng nó vẫn phải được triển khai (implement) trên hệ thống tập tin (filesystem) cụ thể. Tất cả các hệ thống tập tin chính được sử dụng trong các bản phân phối Linux hiện đại đều đã tích hợp các phần mở rộng ACL này.
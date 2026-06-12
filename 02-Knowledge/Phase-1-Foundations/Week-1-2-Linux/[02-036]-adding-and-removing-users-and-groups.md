# Thêm và Xóa Người dùng và Nhóm (Adding and Removing Users and Groups)

## Document Information
- **Doc ID:** 02-036
- **Summary:** Hướng dẫn các câu lệnh thêm, sửa, xóa người dùng và nhóm trên hệ thống Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

Việc thêm một người dùng mới được thực hiện bằng lệnh `useradd` và xóa một người dùng hiện có được thực hiện bằng lệnh `userdel`. Ở dạng đơn giản nhất, tài khoản cho một người dùng mới tên là `bjmoose` sẽ được tạo bằng lệnh:

```bash
sudo /usr/sbin/useradd bjmoose
```

Theo mặc định, lệnh này sẽ thiết lập thư mục nhà (home directory) thành `/home/bjmoose`, điền vào đó một số tập tin cơ bản (được sao chép từ `/etc/skel`), thêm một dòng vào tập tin `/etc/passwd` tương tự như sau:

```text
bjmoose:x:1002:1002::/home/bjmoose:/bin/bash
```

và thiết lập shell mặc định là `/bin/bash`.

Bạn có thể chỉ định thêm các tùy chọn bổ sung để thay đổi các thuộc tính này và thiết lập các thuộc tính khác, chẳng hạn như tên người dùng đầy đủ, v.v. (bạn có thể xem thêm bằng lệnh `man useradd`).

Trước khi tài khoản có thể được sử dụng, người dùng cần phải được thiết lập một mật khẩu. Việc này có thể được thực hiện bằng tùy chọn `-p` cho `useradd`, hoặc bằng cách chạy lệnh:

```bash
sudo passwd bjmoose
```

Hệ thống sẽ hiển thị lời nhắc để bạn nhập và xác nhận mật khẩu.

**Lưu ý:** Chỉ người dùng siêu cấp (superuser, hoặc `root`) mới có quyền thiết lập (hoặc xóa) một tài khoản.

## Xóa Người dùng

Việc xóa một tài khoản người dùng cũng đơn giản như sau:

```bash
sudo /usr/sbin/userdel bjmoose
```

Tuy nhiên, lệnh này sẽ giữ nguyên cấu trúc thư mục `/home/bjmoose`. Điều này có thể hữu ích nếu đây chỉ là việc vô hiệu hóa tài khoản tạm thời. Để xóa cả thư mục nhà trong khi xóa tài khoản, bạn cần sử dụng tùy chọn `-r` cho lệnh `userdel`.

## Sửa đổi Người dùng

Bạn có thể thay đổi các đặc tính của người dùng sau khi tài khoản đã được thiết lập bằng lệnh `usermod`. Ví dụ, bạn có thể sử dụng tùy chọn `-d` để thay đổi thư mục nhà, hoặc tùy chọn `-p` để thay đổi mật khẩu.

## Quản lý Nhóm (Groups)

Việc thêm một nhóm mới được thực hiện bằng lệnh `groupadd`:

```bash
sudo /usr/sbin/groupadd anewgroup
```

Lệnh này thiết lập nhóm `anewgroup` với các thuộc tính mặc định. Có thể xóa nhóm bằng lệnh:

```bash
sudo /usr/sbin/groupdel anewgroup
```

### Thêm Người dùng vào Nhóm

Việc thêm một người dùng vào một nhóm đã tồn tại được thực hiện bằng lệnh `usermod`. Ví dụ, trước tiên bạn có thể xem người dùng đó đã thuộc về những nhóm nào:

```bash
$ groups bjmoose
bjmoose : bjmoose
```

Và sau đó, thêm người dùng vào nhóm mới (sử dụng `-aG` để **thêm** vào nhóm (Append) mà không xóa khỏi các nhóm hiện tại):

```bash
$ sudo /usr/sbin/usermod -aG anewgroup bjmoose
$ groups bjmoose
bjmoose : bjmoose anewgroup
```

Một lần nữa, các tiện ích này phải được chạy dưới quyền người dùng siêu cấp hoặc `root`, và chúng sẽ cập nhật tập tin `/etc/group` khi cần thiết. 

Tiện ích `groupmod` có thể được sử dụng để thay đổi các thuộc tính của nhóm, thường dùng nhất là thay đổi Group ID (GID) bằng số với tùy chọn `-g`, hoặc đổi tên nhóm. *(Lưu ý: để đổi tên nhóm dùng tùy chọn `-n`)*.

### Xóa Người dùng khỏi Nhóm

Việc xóa một người dùng khỏi nhóm có phần phức tạp hơn một chút. Tùy chọn `-G` của lệnh `usermod` (nếu không có chữ `a` đi kèm) sẽ thiết lập **lại toàn bộ** danh sách các nhóm phụ trợ của người dùng. Do đó, nếu bạn muốn đưa người dùng `rjsquirrel` chỉ còn thuộc nhóm chính của họ, bạn sẽ chạy:

```bash
$ sudo /usr/sbin/usermod -G rjsquirrel rjsquirrel
$ groups rjsquirrel
rjsquirrel : rjsquirrel
```

Khi đó, chỉ còn lại nhóm `rjsquirrel` trong danh sách nhóm của người dùng đó.

## Lệnh `id`

Một lệnh bổ sung, `id`, có thể được sử dụng để xem nhanh thông tin của người dùng. Nếu không có đối số truyền vào, nó sẽ cung cấp thông tin về người dùng hiện tại, như sau:

```bash
$ id
uid=1000(george) gid=1000(george) groups=106(fuse),1000(george)
```

Nếu bạn cung cấp tên của một người dùng khác làm đối số (ví dụ: `id bjmoose`), lệnh `id` sẽ báo cáo thông tin chi tiết về người dùng đó.
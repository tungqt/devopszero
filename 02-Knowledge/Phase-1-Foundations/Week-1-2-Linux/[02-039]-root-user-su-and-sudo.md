# Người dùng root, lệnh `su` và `sudo`

## Document Information
- **Doc ID:** 02-039
- **Summary:** Giải thích về người dùng root, cách sử dụng lệnh su và sudo để quản lý đặc quyền quản trị trên Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** N/A
- **Status:** Draft

---

Có thể truy cập vào hệ thống với tư cách là người dùng `root` để thực hiện một loạt các thao tác hoặc chỉ một thao tác duy nhất. Theo nguyên tắc chung, bạn chỉ nên đảm nhận các đặc quyền `root` khi thực sự cần thiết và trong một khoảng thời gian ngắn nhất có thể.

## Lệnh `su`

Để tạm thời đăng nhập với tư cách là một người dùng khác, bạn có thể sử dụng lệnh `su`, như trong các ví dụ sau:

```bash
$ su anotheruser
$ su root
$ su
```

Bạn sẽ được nhắc nhập mật khẩu của người dùng có tên đã được chỉ định. Nếu bạn không cung cấp tên người dùng (như trong ví dụ thứ ba), hệ thống sẽ ngầm hiểu đó là `root`.

Phiên làm việc của người dùng siêu cấp (superuser) kết thúc khi bạn nhập lệnh `exit` trong shell.

Nếu bạn sử dụng một dấu gạch ngang (`-`) làm tùy chọn, như trong:

```bash
$ su -
```

có một sự khác biệt tinh tế; bạn được đăng nhập vào một login shell (shell đăng nhập), điều đó có nghĩa là thư mục làm việc của bạn sẽ chuyển sang thư mục nhà (home directory) của tài khoản bạn đang đăng nhập, các biến môi trường (như PATH) sẽ thay đổi theo tài khoản đó, v.v.

Nếu bạn sử dụng tùy chọn `-c` như trong:

```bash
$ su root -c ls
$ su - root -c ls
```

bạn chỉ thực thi một lệnh duy nhất, trong trường hợp này là `ls`. Trong trường hợp đầu tiên, lệnh này sẽ được chạy trong thư mục làm việc hiện tại, trong trường hợp thứ hai, nó sẽ chạy trong thư mục nhà của `root`.

Giả sử một người dùng bình thường cần đặc quyền `root` tạm thời để thực thi một lệnh, ví dụ để đặt một tập tin vào một thư mục yêu cầu quyền `root`. Bạn có thể làm điều đó với lệnh `su`, nhưng có một hạn chế rõ ràng; người dùng cần phải có mật khẩu `root` để thực hiện điều này.

Một khi bạn đã cho một người dùng bình thường biết mật khẩu `root`, bạn đã từ bỏ mọi nguyên tắc về bảo mật. Mặc dù đây có thể là một phương pháp sử dụng hàng ngày chấp nhận được trên một hệ thống mà bạn là người dùng bình thường duy nhất và bạn đang cố gắng tôn trọng các thói quen hệ thống tốt bằng cách tránh leo thang đặc quyền trừ khi hoàn toàn cần thiết, vẫn có một phương pháp tốt hơn liên quan đến lệnh `sudo`.

## Lệnh `sudo`

Để sử dụng `sudo`, bạn chỉ cần chạy:

```bash
$ sudo -u anotheruser command
$ sudo command
```

Trong đó ở dạng thứ hai, người dùng ngầm định là `root`. Mặc dù điều này trông giống với việc sử dụng `su -c`, nó khá khác biệt ở chỗ **yêu cầu mật khẩu của chính người dùng hiện tại**; trong khi `su` yêu cầu mật khẩu của người dùng đích (thường là mật khẩu của `root`).

Tuy nhiên, điều này sẽ không hoạt động trừ khi superuser đã cập nhật tập tin `/etc/sudoers` để cấp cho bạn quyền sử dụng lệnh `sudo`. Hơn nữa, bạn có thể giới hạn chính xác những nhóm lệnh nào mà một người dùng hoặc nhóm cụ thể có quyền truy cập, và cho phép sử dụng có hoặc không cần nhắc mật khẩu.

### Chỉnh sửa tập tin sudoers

Không nên chỉnh sửa tập tin này bằng các trình soạn thảo văn bản theo cách thông thường. Thay vào đó, bạn nên sử dụng chương trình `visudo` hoặc `sudoedit` (với quyền `root`), ví dụ:

```bash
# /usr/sbin/visudo
# /usr/sbin/sudoedit /etc/sudoers
```

Bởi vì chúng sẽ kiểm tra tập tin kết quả đã chỉnh sửa để đảm bảo không có lỗi cú pháp nào trước khi lưu và thoát. Nếu không, bạn có thể rơi vào tình huống rất khó sửa chữa, đặc biệt là trên các bản phân phối Linux như Ubuntu, nơi hệ thống ẩn tài khoản `root` và phụ thuộc rất nhiều vào việc sử dụng `sudo`.

Thay vì đi sâu vào chi tiết cách tinh chỉnh như vậy, chúng tôi khuyên bạn nên đọc trực tiếp tập tin này vì nó tự có phần chú thích giải thích rất rõ ràng, hoặc chạy lệnh `man sudoers`.

Dòng đơn giản nhất bạn có thể thêm vào tập tin này (ví dụ cho người dùng `student`) sẽ là:

```text
student ALL=(ALL) ALL
```

Dòng này sẽ cho phép người dùng `student` có tất cả các đặc quyền `root` thông thường.

Trên tất cả các bản phân phối Linux gần đây, **bạn không nên sửa đổi trực tiếp tập tin `/etc/sudoers`**. Thay vào đó, có một thư mục con là `/etc/sudoers.d` nơi bạn có thể tạo các tập tin cấu hình riêng lẻ cho từng người dùng.

Do đó, bạn có thể chỉ cần tạo một tập tin ngắn trong `/etc/sudoers.d` chứa dòng cấu hình trên, và sau đó cấp cho nó các quyền hạn thích hợp, ví dụ:

```bash
$ chmod 440 /etc/sudoers.d/student
```

Lưu ý rằng một số bản phân phối Linux có thể yêu cầu quyền `chmod 400 /etc/sudoers.d/student`.

### Lưu ý về chuyển hướng I/O với sudo

Nếu một tập tin có tên `/tmp/rootfile` thuộc sở hữu của `root`, lệnh sau:

```bash
$ sudo echo hello > /tmp/rootfile
```

sẽ thất bại do vấn đề về quyền truy cập (Permission denied).

Cách thích hợp để làm điều này là:

```bash
$ sudo bash -c "echo hello > /tmp/rootfile"
```

Bạn có hiểu tại sao không? 
*(Giải thích: Bởi vì quá trình chuyển hướng `>` được thực thi bởi shell của người dùng bình thường hiện tại trước khi lệnh `sudo` có tác dụng, do đó shell hiện tại không có quyền ghi vào `/tmp/rootfile`. Bằng cách bọc toàn bộ lệnh trong chuỗi `bash -c "..."` và chạy nó với `sudo`, việc chuyển hướng luồng dữ liệu sẽ xảy ra bên trong một shell con có đặc quyền root).*

## Ubuntu và người dùng root

Một số bản phân phối Linux, đáng chú ý nhất là Ubuntu, không làm việc với tài khoản người dùng `root` theo kiểu UNIX truyền thống.

Thay vào đó, dường như chỉ có một tài khoản người dùng bình thường, và cùng một mật khẩu đó được sử dụng để đăng nhập vào hệ thống như một người dùng bình thường, cũng như để sử dụng `sudo`. Trên thực tế, tài khoản root bị khóa mật khẩu và không có lệnh `su` trực tiếp để đăng nhập vào root. Tuy nhiên, điều tương đương có thể dễ dàng đạt được thông qua lệnh: `sudo su`.

Đối với một số người theo chủ nghĩa truyền thống của UNIX, phương pháp giải quyết tài khoản `root` này là một ý tưởng tồi, và một số người cho rằng việc sử dụng nó là một nỗ lực để làm cho người dùng Windows cảm thấy quen thuộc và thoải mái hơn khi họ chuyển sang sử dụng Linux.
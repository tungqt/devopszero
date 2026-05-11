# Quản lý Gói Phần mềm bằng Giao diện Đồ họa (Graphical Package Management)

## Document Information
- **Doc ID:** 02-026
- **Summary:** Bài lab tham khảo cách quản lý các gói phần mềm trên môi trường Linux Desktop (Ubuntu) sử dụng các công cụ giao diện đồ họa như GNOME Software và Synaptic Package Manager.
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

Mặc dù các Quản trị viên hệ thống (SysAdmins) và Kỹ sư DevOps giàu kinh nghiệm thường sử dụng dòng lệnh (CLI) để cài đặt, cập nhật hay gỡ bỏ phần mềm (như `apt`, `yum`), nhưng mọi bản phân phối Linux Desktop đều cung cấp ít nhất một công cụ Quản lý Gói bằng Giao diện Đồ họa (Graphical Package Manager). 

Các công cụ này mang lại trải nghiệm trực quan, dễ tiếp cận cho người dùng mới, tương tự như việc sử dụng App Store trên macOS hay Play Store trên Android. Bài viết này trình bày hai công cụ phổ biến trên Ubuntu.

> 📌 **Lưu ý đối với DevOps:** Bài học này mang tính chất **Tham khảo (Demo)**. Trên môi trường máy chủ thực tế (như AWS EC2), chúng ta sẽ không có giao diện đồ họa (GUI) này, mà bắt buộc phải sử dụng các công cụ Command Line tương ứng. Dù vậy, việc hiểu cơ chế của GUI sẽ giúp bạn dễ hình dung hơn về cách Linux phân loại và giải quyết các phần mềm phụ thuộc (dependencies).

---

## 2. Công cụ GNOME Software (Cửa hàng Ứng dụng hiện đại)

Đây là công cụ mặc định và phổ biến nhất trên môi trường Ubuntu sử dụng GNOME Desktop.

### Đặc điểm nổi bật
- **Giao diện hiện đại:** Trông rất giống một "App Store". Các ứng dụng được trình bày với icon lớn, phân chia theo từng danh mục (Utilities, Games, Development...).
- **Tab quản lý rõ ràng:** Thường được chia làm 3 tab chính:
  - **Explore:** Khám phá và tìm kiếm ứng dụng mới.
  - **Installed:** Xem danh sách toàn bộ các phần mềm đã cài đặt.
  - **Updates:** Xem danh sách các phần mềm hoặc bản vá bảo mật hệ thống đang cần được cập nhật.

### Thao tác cơ bản
- **Tìm kiếm:** Nhấp vào biểu tượng kính lúp (Search) và gõ từ khóa (ví dụ: `terminal` hay `cheese` - một ứng dụng dùng webcam).
- **Cài đặt / Gỡ bỏ:** Chỉ với một nút bấm `Install` hoặc `Remove`.
- **Bảo mật:** Giống như mọi hành động thay đổi hệ thống khác, bất cứ khi nào bạn nhấn Install/Remove, GNOME Software đều sẽ yêu cầu bạn nhập mật khẩu (Password của user có quyền `sudo`).

---

## 3. Công cụ Synaptic Package Manager (Quản trị viên cổ điển)

Synaptic là một trong những trình quản lý gói đồ họa lâu đời nhất trên Ubuntu/Debian. Nó không bóng bẩy như GNOME Software, nhưng lại cung cấp chi tiết sâu hơn rất nhiều về hệ thống.

### Đặc điểm nổi bật
- **Giao diện kỹ thuật:** Không có các icon ứng dụng khổng lồ. Synaptic liệt kê chi tiết tên gói (package name), phiên bản hiện tại, và mô tả ngắn gọn. Gói nào đã cài đặt sẽ được đánh dấu màu xanh (green).
- **Quản lý phụ thuộc (Dependencies) rõ ràng:** Khi bạn chọn cài một phần mềm (Ví dụ: `qterminal`), Synaptic sẽ bật ra một bảng thông báo giải thích chi tiết rằng: *"Để phần mềm này chạy được, hệ thống bắt buộc phải cài đặt thêm 5 thư viện (packages) khác đi kèm"*. Đây chính là khái niệm **Dependency Resolution** cốt lõi của Linux.

### Thao tác cơ bản
- Mở Synaptic từ `Applications > System Tools`. (Hệ thống sẽ hỏi mật khẩu ngay khi vừa mở phần mềm).
- Sử dụng ô **Search** để tìm gói (có thể lọc tìm theo tên hoặc cả mô tả).
- **Đánh dấu (Mark):** Thay vì cài đặt ngay lập tức, Synaptic cho phép bạn lên danh sách các hành động:
  - `Mark for Installation` (Đánh dấu để cài)
  - `Mark for Removal` (Đánh dấu để xóa)
- Sau khi đánh dấu xong một loạt các gói, bạn mới nhấn nút **Apply** (Áp dụng) trên thanh công cụ để phần mềm thực thi toàn bộ các lệnh đó cùng lúc.

---

## 4. Bài học rút ra (Takeaway)

Dù giao diện là GNOME Software bóng bẩy hay Synaptic chi tiết, phía sau hậu trường (under the hood), cả hai công cụ này đều đang gọi xuống cùng một hệ thống quản lý gói cấp thấp của Debian/Ubuntu là `apt` (Advanced Package Tool) và `dpkg`. 

Việc hiểu rằng **"một phần mềm thường cần kéo theo nhiều thư viện khác mới chạy được"** (như bài học từ Synaptic) là kiến thức bản lề để bạn nắm bắt phần Package Management bằng Command Line trong các bài học tiếp theo.
# Cài đặt Hệ thống Linux (System Installation)

## Document Information
- **Doc ID:** 02-025
- **Summary:** Các khái niệm cơ bản, phương tiện và phương pháp cài đặt hệ điều hành Linux (Live Media, Network, Automated Install) cũng như các tuỳ chọn chạy Linux trên máy ảo.
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Lịch sử và Sự phát triển của Phương tiện Cài đặt

Quá trình cài đặt Linux đã tiến hóa rất nhiều qua các thời kỳ, từ chỗ là một ác mộng kỹ thuật trở nên thân thiện và dễ dàng như hiện nay:
- **Đầu những năm 90:** Phải dùng từ 12 đến 24 đĩa mềm (floppy disks), tải qua đường truyền điện thoại chậm chạp. Chỉ cần lỗi ở giữa chừng là phải làm lại từ đầu.
- **Giữa/Cuối thập niên 90:** Chuyển sang dùng đĩa CD-ROM.
- **Thập niên 2000:** Chuyển sang DVD-based để chứa được lượng phần mềm lớn hơn.
- **Hiện nay:** Phổ biến nhất là sử dụng **USB-based methods** (USB boot) hoặc cài đặt trực tiếp qua mạng (**Network installation**).

---

## 2. Quá trình Thiết lập Cài đặt (Installation Choices)

Trong những ngày đầu, đĩa cài đặt Linux bắt người dùng phải lựa chọn chi li mọi thứ ngay từ đầu (Desktop manager là GNOME hay KDE? Máy dùng cho server hay workstation?...).

Tuy nhiên, hầu hết các nhà phân phối Linux hiện đại (distributors) đã tối giản hóa quá trình này:
- Bạn chỉ cần chọn vài thông số cốt lõi: Múi giờ (Time zone), Tên người dùng (Username), Mật khẩu (Password).
- Một quá trình cài đặt đầy đủ giờ đây thường **chỉ mất vài phút**.
- Bạn luôn có thể "dọn dẹp" (tidy up) hoặc cài đặt thêm các gói phần mềm (Desktop Environment, Development Tools) **sau khi** hệ thống đã cài đặt xong thông qua trình quản lý gói (Packaging programs).

---

## 3. Cài đặt Song song (Multi-boot / Dual-boot System)

Thường thì Linux được cài đặt song song với các hệ điều hành khác (như Windows).
- **Rất dễ dàng** nếu ổ cứng của bạn có sẵn không gian trống chưa phân vùng (unpartitioned free space), hoặc nếu bạn có một ổ cứng vật lý thứ hai.
- **Khá phức tạp và rủi ro** nếu ổ cứng đã đầy dữ liệu hoặc nhà sản xuất máy tính đã chiếm hết toàn bộ các phân vùng chính (primary partitions) cho hệ thống Recovery của họ. Khi đó, việc chia lại vùng (repartition) cần thực hiện hết sức cẩn thận, khuyến nghị nên nhờ chuyên gia nếu bạn không rành.

---

## 4. Live Media (Dùng thử không cần cài đặt)

Hầu hết các bản phân phối Linux ngày nay đều cung cấp **Live Media** (CD, DVD, hoặc USB).
- **Lợi ích 1 (Dùng thử):** Cho phép bạn boot trực tiếp vào một hệ điều hành Linux hoàn chỉnh đang chạy trên RAM và USB mà **không hề động chạm hay cài đặt gì vào ổ cứng** máy tính của bạn.
- **Lợi ích 2 (Kiểm tra phần cứng):** Live Media làm rất tốt việc dò tìm (detect) phần cứng. Nó giúp bạn kiểm tra xem Linux có nhận diện đúng Card mạng (Network card), Card âm thanh (Sound card), và Ổ cứng không. Phát hiện vấn đề thiếu driver *trước khi* bắt đầu cài đặt thực sự là một điều tuyệt vời.
- **Thao tác cài đặt:** Nếu bạn ưng ý sau khi dùng thử, luôn có một biểu tượng "Install" ngay trên màn hình Desktop của Live Media để tiến hành cài đặt chính thức lên ổ cứng.
- **Nhược điểm:** Hiệu năng khi chạy Live Media khá chậm (vì tốc độ đọc của USB/CD chậm hơn nhiều so với ổ cứng) và nó tiêu tốn nhiều RAM hơn.

---

## 5. Các Phương thức Cài đặt Nâng cao

### Cài đặt qua Mạng (Network-based Installations)
- Boot từ một file ảnh (image) cực nhỏ chứa hạt nhân cơ bản.
- Toàn bộ các gói phần mềm cần thiết sẽ được tải xuống thông qua kết nối Internet trong quá trình cài đặt.
- **Ưu điểm:** Kích thước tải ban đầu rất nhỏ; bạn chỉ tải đúng những phần mềm bạn chọn thay vì tải toàn bộ file ISO vài GB.
- **Nhược điểm:** Tốc độ phụ thuộc hoàn toàn vào mạng internet của bạn; tốn băng thông nếu phải cài cho nhiều máy.

### Cài đặt Tự động (Automated Utilities)
Dành cho việc triển khai ở quy mô lớn (Enterprise/Datacenter).
- Thay vì cấu hình tay cho từng máy, bạn tạo ra một file cấu hình (ví dụ: file **`kickstart`** trong họ Red Hat).
- File này chứa sẵn mọi câu trả lời (phân vùng, mật khẩu, gói phần mềm cần cài).
- Bạn có thể dùng file `kickstart` này (kết hợp với Install Server) để cài đặt tự động cùng lúc cho hàng trăm máy tính trong phòng máy (Server room).

---

## 6. Máy ảo (Virtual Machines) và Hypervisors

Thay vì cài trực tiếp lên phần cứng vật lý (Bare-metal), một giải pháp cực kỳ phổ biến và an toàn cho người mới học là dùng **Máy ảo**.
- Bạn vẫn giữ nguyên hệ điều hành cũ (Windows/macOS).
- Sử dụng các phần mềm **Hypervisor** như **Oracle VirtualBox**, **VMware Player/Workstation**, hoặc **KVM** (Hypervisor bản địa của Linux).
- Máy ảo chạy Linux như một ứng dụng (application) bình thường bên trong máy tính của bạn, không gây tổn hại gì đến hệ thống thật.
- **Nhược điểm:** Sẽ có sự sụt giảm đôi chút về hiệu năng (drop in performance) và tiêu tốn thêm RAM/CPU từ máy thật (Host). Tuy nhiên, với nhu cầu học tập và cấu hình server cơ bản, tài nguyên này hoàn toàn không đáng kể.

> 💡 **Takeaway đối với lộ trình Cloud / AWS EC2:**
> Trong thực tế làm Cloud/DevOps, bạn hiếm khi phải tự tay cắm USB vào máy chủ để cài đặt. Bạn thường sẽ:
> 1. Nhấn nút tạo EC2 từ một **AMI (Amazon Machine Image)** đã có sẵn Linux.
> 2. Sử dụng **Automated Provisioning** (Terraform, Ansible) kết hợp với khái niệm tương tự kickstart (như `cloud-init` / `User Data`) để hệ thống tự động cài đặt các cấu hình ngay khi máy chủ Cloud vừa boot lên.
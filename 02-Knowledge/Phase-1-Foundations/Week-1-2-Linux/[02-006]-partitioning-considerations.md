# Chiến lược Phân vùng Ổ đĩa (Partitioning Considerations)

## Document Information
- **Doc ID:** 02-006
- **Summary:** Phân tích các yếu tố cần cân nhắc khi thiết kế phân vùng ổ đĩa cho hệ thống Linux. Các mô hình phân vùng phổ biến từ cơ bản đến nâng cao.
- **Version:** 0.0.1
- **Date:** 2026-05-06
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

### 1.1. Tại sao phải suy nghĩ về chiến lược phân vùng?
Khi cài đặt một hệ điều hành Linux (hoặc khi thiết lập một server mới), bạn có quyền quyết định xem ổ cứng sẽ được chia nhỏ như thế nào. Việc chia phân vùng hợp lý mang lại nhiều lợi ích sống còn cho hệ thống:

- **Bảo vệ hệ điều hành (OS Protection):** Nếu một ứng dụng (ví dụ: database) ghi log liên tục làm đầy ổ đĩa, nó sẽ không làm sập toàn bộ hệ thống nếu log đó nằm trên một phân vùng riêng biệt.
- **Bảo mật (Security):** Bạn có thể gắn các quyền hạn chế (như `noexec` - không cho phép chạy script, hoặc `nosuid`) lên các phân vùng chứa dữ liệu người dùng (`/home` hoặc `/tmp`) để ngăn chặn mã độc.
- **Tối ưu hiệu năng (Performance):** Các loại dữ liệu khác nhau có thể cần các loại Filesystem khác nhau (ví dụ: dùng `xfs` cho database partition, `ext4` cho OS partition).
- **Dễ dàng Backup & Phục hồi:** Bạn có thể backup riêng phân vùng dữ liệu mà không cần backup toàn bộ hệ điều hành.

---

## 2. Các mô hình phân vùng phổ biến

### 2.1. Mô hình Đơn giản (Simple Scheme) - Dành cho Desktop/Test Server
Đây là mô hình dễ nhất, thường được dùng trên máy tính cá nhân hoặc các server thử nghiệm nhỏ.

- **`/` (Root partition):** Chứa TẤT CẢ mọi thứ (hệ điều hành, ứng dụng, dữ liệu người dùng, log...).
- **`swap` (Swap partition):** Phân vùng bộ nhớ ảo. Thường có dung lượng bằng hoặc gấp đôi dung lượng RAM vật lý. *(Lưu ý: Nhiều bản phân phối hiện đại như Ubuntu mới đã chuyển sang dùng Swap File thay vì Swap Partition để linh hoạt hơn).*

**Ưu điểm:** Cực kỳ dễ cài đặt, không phải đau đầu tính toán dung lượng cho từng thư mục.
**Nhược điểm:** Rủi ro cao. Nếu `/var/log` đầy, toàn bộ hệ thống `/` sẽ hết chỗ và server có thể bị treo.

### 2.2. Mô hình Tiêu chuẩn (Standard Scheme) - Khuyến nghị cho Server cơ bản
Mô hình này tách biệt phần khởi động (boot) ra khỏi phần hệ điều hành chính.

- **`/boot`:** (Khoảng 500MB - 1GB). Chứa Linux Kernel, Initial RAM disk (initrd), và cấu hình GRUB (bootloader).
  - *Lý do tách riêng:* Dữ liệu ở đây rất ít khi thay đổi và cực kỳ quan trọng. Tách riêng giúp bảo vệ nó khỏi bị lỗi (corruption) và đôi khi cần thiết cho các cấu hình mã hóa toàn bộ ổ đĩa (Full Disk Encryption).
- **`/` (Root):** Chứa hệ điều hành và các ứng dụng.
- **`swap`:** Bộ nhớ ảo.

### 2.3. Mô hình Nâng cao (Advanced/Multi-user Scheme) - Dành cho Production Server
Trên các server thực tế (Production), đặc biệt là server có nhiều người dùng hoặc chạy các dịch vụ quan trọng, việc chia nhỏ phân vùng là bắt buộc.

Ngoài `/boot`, `/`, và `swap`, người ta thường tách riêng các thư mục sau thành các phân vùng độc lập:

- **`/home`:** Chứa dữ liệu cá nhân của người dùng.
  - *Lý do:* Nếu cài lại hệ điều hành (format lại `/`), dữ liệu người dùng ở `/home` vẫn được giữ nguyên. Có thể áp dụng quota để giới hạn dung lượng của từng user.
- **`/var`:** Chứa các dữ liệu thường xuyên thay đổi (volatile data) như log files (`/var/log`), database files (`/var/lib/mysql`), email queues.
  - *Lý do:* Đây là thư mục dễ bị "phình to" nhất. Tách riêng `/var` đảm bảo rằng dù log có đầy thì hệ điều hành (nằm ở `/`) vẫn hoạt động bình thường.
- **`/tmp`:** Chứa các file tạm thời.
  - *Lý do:* Rất nhiều mã độc lợi dụng thư mục `/tmp` (vì nó cho phép mọi user ghi file). Tách riêng `/tmp` cho phép quản trị viên áp dụng các cờ bảo mật như `noexec` (không cho phép chạy file thực thi từ thư mục này).
- **`/usr`:** Chứa các ứng dụng cài đặt thêm, thư viện, tài liệu.
  - *Lý do:* Dữ liệu ở đây khá tĩnh (static). Tách riêng có thể giúp mount nó dưới dạng Read-Only (chỉ đọc) để tăng tính bảo mật.

---

## 3. Các giải pháp lưu trữ linh hoạt hơn

### 3.1. Network Filesystems (NFS)
Trong môi trường doanh nghiệp, một số "phân vùng" thậm chí không nằm trên ổ cứng vật lý của server đó, mà được mount qua mạng (Network).
- Ví dụ: Thư mục `/home` của nhân viên có thể được lưu tập trung trên một máy chủ NAS (Network Attached Storage) và được mount vào tất cả các server qua giao thức NFS.
- *Lưu ý:* Các phân vùng mạng này có thể sẽ không khả dụng cho đến khi hệ thống mạng của server khởi động xong.

### 3.2. LVM (Logical Volume Manager)
Chia phân vùng vật lý (Physical Partitions) truyền thống có một nhược điểm lớn: **Rất khó để thay đổi kích thước (resize) sau khi đã chia.**

**LVM** là một lớp trừu tượng hóa (abstraction layer) nằm giữa ổ đĩa vật lý và Filesystem. Nó mang lại sự linh hoạt tuyệt vời:
- Bạn có thể gộp nhiều ổ cứng vật lý nhỏ thành một "ổ đĩa ảo" khổng lồ.
- Bạn có thể **tăng/giảm dung lượng** của các phân vùng (Logical Volumes) một cách cực kỳ dễ dàng ngay cả khi hệ thống đang chạy (on-the-fly) mà không cần khởi động lại.
- Dễ dàng tạo snapshot (bản sao nhanh) để backup dữ liệu.

*(LVM là một chủ đề lớn và sẽ được học chi tiết trong các bài Lab tiếp theo).*

---

## 4. Tổng kết

Quyết định chia phân vùng như thế nào phụ thuộc vào **nhu cầu sử dụng** và **phần cứng** của bạn. 
- Nếu chỉ là Lab học tập: Dùng mô hình đơn giản (`/` và `swap`).
- Nếu là Server Production: Bắt buộc phải tách ít nhất `/var` và `/tmp` để đảm bảo an toàn, và nên cân nhắc sử dụng **LVM** để dễ dàng mở rộng trong tương lai.

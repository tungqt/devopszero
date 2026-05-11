# Quản lý Mô-đun Nhân (Kernel Modules)

## Document Information
- **Doc ID:** 02-020
- **Summary:** Tìm hiểu về kiến trúc Mô-đun nhân (Kernel Modules) của Linux, cách hoạt động, và các công cụ quản lý module như `lsmod`, `insmod`, `rmmod`, `modprobe` và `modinfo`.
- **Version:** 0.0.1
- **Date:** 2026-05-10
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Kernel Modules?**
Nhân Linux (Linux Kernel) là phần lõi giao tiếp trực tiếp với phần cứng máy chủ. Một trong những sức mạnh lớn nhất của Linux là khả năng thêm vào (load) hoặc gỡ bỏ (unload) các thành phần lõi này ngay lập tức khi hệ thống đang chạy (runtime) mà không cần phải khởi động lại (reboot) máy chủ. 
Các thành phần này được gọi là **Kernel Modules** (Mô-đun nhân). Chúng có thể là trình điều khiển thiết bị (device drivers), giao thức mạng (network protocols), hoặc một hệ thống tệp (filesystem) mới. Việc nắm vững cách kiểm tra và quản lý module giúp bạn chẩn đoán lỗi phần cứng (như không nhận card mạng) hoặc tải các tính năng nâng cao vào hệ thống một cách chủ động.

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Bất kỳ hệ thống Linux nào (Local VM hoặc AWS EC2).
- **Yêu cầu:** 
  - Các lệnh xem thông tin (`lsmod`, `modinfo`) có thể chạy bằng user thường.
  - Các lệnh tải/gỡ module (`insmod`, `rmmod`, `modprobe`) BẮT BUỘC phải chạy bằng quyền `root` (`sudo`).

## 3. Đặc điểm của Kernel Modules

Mặc dù Linux sử dụng module cực kỳ phổ biến, nó vẫn duy trì kiến trúc **Monolithic Kernel** (Nhân nguyên khối) chứ không phải Microkernel. Lý do là vì:
- Khi một module được tải vào, nó trở thành một phần hoàn chỉnh của Kernel, không có bất kỳ hạn chế nào về quyền lực.
- Nó giao tiếp với các phân hệ khác của Kernel bằng cách chia sẻ tài nguyên trực tiếp (bộ nhớ, locks) thay vì phải truyền tin nhắn (message passing) chậm chạp như kiến trúc Microkernel.
- Các module luôn có phần mở rộng (extension) là **`.ko`** (Kernel Object) — ví dụ: `e1000e.ko`, `ext4.ko`.

## 4. Các Công cụ Quản lý Modules

### 4.1. Xem các module đang chạy: `lsmod`
Lệnh này liệt kê tất cả các module hiện đang được tải vào Kernel.
```bash
lsmod
```
*Output cung cấp thông tin: Tên module (Module), Kích thước (Size), và quan trọng nhất là cột `Used by` (Chỉ ra module này đang bị phụ thuộc bởi module hoặc tiến trình nào khác).*

### 4.2. Tải và Gỡ module trực tiếp (Cách cơ bản)
Nếu bạn biết chính xác đường dẫn file `.ko`, bạn có thể dùng công cụ cấp thấp:
- **Tải module:**
  ```bash
  sudo /sbin/insmod /duong_dan/toi/module_name.ko
  ```
  *(Bạn có thể truyền thêm tham số ngay lúc tải, ví dụ: `insmod module.ko irq=12`)*
- **Gỡ module:**
  ```bash
  sudo /sbin/rmmod module_name
  ```
  *(Khi gỡ, không cần ghi đường dẫn hay đuôi `.ko`)*

### 4.3. Quản lý module thông minh với `modprobe` (Khuyến nghị)
Trong thực tế, DevOps hiếm khi dùng `insmod/rmmod` mà luôn dùng **`modprobe`** vì nó **thông minh hơn rất nhiều**:
- Nó tự động tìm module trong thư mục chuẩn `/lib/modules/$(uname -r)/`.
- **Tự động xử lý Dependency (Phụ thuộc):** Khi tải một module, nếu module đó cần một module khác chạy trước, `modprobe` sẽ tự động tải cả hai.
- **Tải module:**
  ```bash
  sudo /sbin/modprobe module_name
  ```
- **Gỡ module:**
  ```bash
  sudo /sbin/modprobe -r module_name
  ```

### 4.4. Xem thông tin chi tiết của module: `modinfo`
Lệnh `modinfo` cho phép bạn đọc các metadata (thông tin mô tả) của một module, ngay cả khi nó chưa được tải vào hệ thống.
```bash
/sbin/modinfo e1000e
```
*Kết quả sẽ hiển thị file `.ko` nằm ở đâu, người viết (author), giấy phép (license), danh sách tham số (parm) hỗ trợ, và các alias phần cứng (như trong ảnh bài giảng đính kèm).*

## 5. Những Nguyên tắc Quan trọng (Best Practices)
1. **Quy tắc không thể gỡ (Unload Restrictions):**
   - Không thể gỡ một module nếu nó đang nằm trong cột `Used by` của một module khác trong `lsmod`.
   - Không thể gỡ một module nếu có một tiến trình (process) đang sử dụng nó.
2. **Thư mục Cấu hình `/etc/modprobe.d/`:**
   - Thư mục này chứa các file cấu hình quy định cách `modprobe` hoạt động. 
   - Quan trọng nhất là tính năng **Blacklist** (Danh sách đen). Khi một module bị đưa vào blacklist trong thư mục này, hệ thống sẽ vĩnh viễn không bao giờ tự động tìm và tải module đó lên (dùng để chặn các driver bị lỗi hoặc driver xung đột).

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Kiểm tra danh sách các module đang chạy và thực hành đọc thông tin chi tiết của một module (đóng vai trò là Driver card mạng).

**Bước 1:** Xem các Kernel Modules đang hoạt động trên hệ thống.
```bash
lsmod | head -n 10
```
*Ghi nhận tên của một module bất kỳ xuất hiện trong danh sách (Cột bên trái cùng).*

**Bước 2:** Kiểm tra driver card mạng bạn đang dùng.
Thường các module card mạng rất phổ biến. Bạn có thể dùng lệnh sau để xem tên module card mạng (driver) mà hệ thống đang dùng (ví dụ trên AWS EC2 thường là `ena` hoặc `ixgbevf`).
```bash
ethtool -i eth0  # (Thay eth0 bằng tên card mạng của bạn nếu cần)
# Hoặc đơn giản hơn:
lsmod | grep -i -e "ena" -e "e1000"
```

**Bước 3:** Đọc thông tin chi tiết của một module.
Giả sử máy chủ của bạn có module driver tên là `e1000` (dành cho card Intel) hoặc `ena` (dành cho AWS), hãy dùng `modinfo` để đọc nó:
```bash
modinfo e1000
# Hoặc: modinfo ena
```
*Trong kết quả in ra, hãy chú ý đến đường dẫn tuyệt đối của file `.ko` ở dòng `filename:`, và đoạn `description:` giải thích công dụng của module này.*

---

## 7. Lab Nâng cao: Khắc phục lỗi nạp Kernel Module (Demo)

**Mục tiêu:** Thử nghiệm việc nạp một module có phụ thuộc (dependency) bằng `insmod` để thấy nó thất bại ra sao, sau đó dùng `modprobe` để giải quyết vấn đề tự động.

> 📌 **Lưu ý:** Bài lab này là sự tóm tắt từ bài giảng Demo. Module `3c59x` (driver card mạng 3com cổ điển) có thể không tồn tại sẵn trên các hệ thống EC2 hiện đại. Do đó, phần này chủ yếu để bạn **đọc hiểu tư duy** gỡ lỗi (troubleshoot) của một DevOps.

### Kịch bản lỗi:
Bạn đang đứng tại thư mục chứa các module mạng, và bạn tìm thấy một module driver tên là `3c59x.ko`.
Bạn dùng lệnh `modinfo` để kiểm tra thông tin của nó trước khi nạp:
```bash
modinfo 3c59x
```
Trong hàng loạt thông tin trả về, có một dòng cực kỳ quan trọng:
**`depends: mii`**
Dòng này có nghĩa là: Để driver `3c59x` hoạt động, module `mii` (Media Independent Interface) bắt buộc phải được nạp vào Kernel trước.

### Bước 1: Thử nạp thủ công bằng `insmod` (Sẽ gặp lỗi)
Bạn cố tình bỏ qua cảnh báo dependency và dùng lệnh cấp thấp `insmod` để nạp trực tiếp file `.ko`:
```bash
sudo insmod 3c59x.ko
```
**Kết quả:** Hệ thống báo lỗi `Unknown symbol in module`. Lệnh `insmod` quá "ngu ngốc" để tự hiểu rằng nó cần phải đi tìm và nạp module `mii` trước. Nó chỉ đơn giản là cố nhét file `3c59x.ko` vào Kernel và thất bại do thiếu thư viện.

Để xem chi tiết nguyên nhân lỗi ở tầng Kernel, bạn dùng lệnh:
```bash
dmesg | tail -n 30
```
Trong log `dmesg`, Kernel sẽ phàn nàn rõ ràng: `Unknown symbol mii_ethtool_gset...` (Không tìm thấy các hàm liên quan đến mii).

### Bước 2: Nạp thông minh bằng `modprobe` (Thành công)
Bây giờ, bạn chuyển sang dùng công cụ tiêu chuẩn `modprobe`. Lưu ý với `modprobe`, bạn chỉ cần gõ tên module (không cần đuôi `.ko` hay đường dẫn).
```bash
sudo modprobe 3c59x
```
**Kết quả:** Không có thông báo lỗi nào hiện ra (nghĩa là đã thành công). 

Hãy dùng lệnh `lsmod` để kiểm chứng:
```bash
lsmod | head -n 5
```
Bạn sẽ thấy `modprobe` đã tự động tìm và nạp **cả 2 module**: `3c59x` và `mii` vào Kernel cùng lúc.

### Bước 3: Gỡ module an toàn bằng `modprobe -r`
Sau khi xong việc, bạn muốn dọn dẹp hệ thống bằng cách gỡ driver mạng này ra.
```bash
sudo modprobe -r 3c59x
```
Lệnh này cũng rất thông minh. Khi nó gỡ `3c59x` ra khỏi bộ nhớ, nó sẽ kiểm tra xem module `mii` có đang bị một ứng dụng hay module nào khác dùng nữa không. Nếu không ai dùng `mii` nữa, nó sẽ tự động dọn dẹp và gỡ luôn cả `mii` ra khỏi Kernel.

**Bài học rút ra (Takeaway):**
Khi làm việc với các trình điều khiển phần cứng trên Linux, **luôn luôn dùng `modprobe`** thay cho `insmod/rmmod` để tránh đau đầu với bài toán phụ thuộc lẫn nhau (Dependency Hell). Đồng thời, tập thói quen đọc log bằng `dmesg` để hiểu được Kernel đang muốn nói gì với bạn khi có lỗi xảy ra.
# Quản lý Thiết bị (Device Management)

## Document Information
- **Doc ID:** 02-021
- **Summary:** Khái niệm về các loại thiết bị trong Linux (Character, Block, Network), cách hoạt động của Device Nodes (mknod) và vai trò của hệ thống `udev` trong việc quản lý thiết bị động.
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Quản lý Thiết bị?**
Hệ điều hành Linux tương tác với phần cứng (hardware) thông qua một triết lý vô cùng nổi tiếng: *"Everything is a file"* (Mọi thứ đều là một tệp). Khi bạn cắm một ổ cứng mới, một chiếc USB, hay một card mạng, Linux sẽ tạo ra các tệp đặc biệt (gọi là Device Nodes) trong thư mục `/dev/` để đại diện cho phần cứng đó. Việc hiểu cách Linux phân loại thiết bị và cách hệ thống `udev` tự động nhận diện, cấu hình chúng giúp bạn tự tin xử lý các vấn đề liên quan đến việc gắn thêm ổ đĩa (EBS/Volume) hoặc xử lý lỗi mất kết nối phần cứng trên server.

## 2. Phân loại Thiết bị trong Linux

Hầu hết các thiết bị được kết nối với hệ thống Linux sẽ rơi vào 1 trong 3 loại chính sau:

### 2.1. Character Devices (Thiết bị dạng Ký tự)
- **Đặc điểm:** Truyền tải dữ liệu tuần tự dưới dạng một luồng byte (byte streams). Không thể đọc/ghi ngẫu nhiên (random access) vào một vị trí bất kỳ.
- **Cơ chế:** Thường chỉ hỗ trợ các hàm cơ bản: `open`, `close`, `read`, `write`.
- **Ví dụ:** Cổng Serial, máy in (printer), card âm thanh, bàn phím, chuột.

### 2.2. Block Devices (Thiết bị dạng Khối)
- **Đặc điểm:** Đây là những thiết bị lưu trữ (storage devices). Nó chỉ xử lý dữ liệu theo các "khối" (blocks) có kích thước cố định.
- **Cơ chế:** Có thể truy xuất ngẫu nhiên (đọc block số 100 rồi quay lại block 2). Các thao tác I/O thường được hệ thống **cache (đệm)** lại trên RAM để tăng tốc (nên mới sinh ra độ trễ).
- **Ví dụ:** Ổ cứng HDD/SSD, ổ CD-ROM, USB Flash drive.

### 2.3. Network Devices (Thiết bị Mạng)
- **Đặc điểm:** Rất khác biệt so với hai loại trên. Nó không truyền stream hay block, mà truyền dữ liệu theo **gói (packets)**.
- **Cơ chế:** Thay vì các hàm read/write thông thường, nó sử dụng **giao diện Socket** với các hàm thu/phát (reception / transmission).
- **Ví dụ:** Card mạng có dây (`eth0`), card wifi (`wlan0`). Thiết bị mạng **KHÔNG** xuất hiện dưới dạng một file thiết bị trong `/dev/`.

*(Lưu ý: Ngoài ra còn có các Driver hoạt động hoàn toàn ở User-space (không nằm trong Kernel), ví dụ điển hình là driver cho Máy in).*

## 3. Nút Thiết bị (Device Nodes) và Major/Minor Numbers

Để hệ thống và người dùng có thể tương tác với các thiết bị Character và Block, Linux sử dụng khái niệm **Device Nodes**. Chúng được đặt trong thư mục `/dev/`.
- Ví dụ: `/dev/sda` là toàn bộ ổ cứng thứ nhất, `/dev/sda1` là phân vùng số 1 của ổ cứng đó. `/dev/lp0` là máy in số 1.

**Cấu tạo của một Device Node:**
Hệ thống tạo ra các file này bằng lệnh **`mknod`**. Mỗi thiết bị được định danh bằng hai con số rất quan trọng bắt buộc theo chuẩn POSIX:
1. **Major number (Số chính):** Xác định **Loại** thiết bị (Type/Driver nào sẽ điều khiển nó).
2. **Minor number (Số phụ):** Xác định **Phiên bản** cụ thể của thiết bị đó (Ví dụ: phân vùng số mấy, máy in số mấy).

## 4. Sự tiến hóa của Quản lý Thiết bị và Hệ thống `udev`

### 4.1. Vấn đề của phương pháp cũ (Static /dev)
Trong những năm đầu của Linux, các bản phân phối tạo sẵn **hàng ngàn** Device Nodes trong thư mục `/dev/` cho mọi thiết bị có thể tồn tại trên đời. Điều này cực kỳ lãng phí và lộn xộn, vì 99% số phần cứng đó sẽ không bao giờ được cắm vào máy tính của bạn.

### 4.2. Kỷ nguyên của `udev` (User Device Management)
Để khắc phục, hệ thống **`udev`** ra đời. Nó hoàn toàn thay thế các phương pháp cũ (như devfs, hotplug).
- **Cơ chế hoạt động:** Nó là một giải pháp tạo/xóa Device Node **động (dynamic)**. Khi bạn cắm một thiết bị vào (ví dụ: ổ USB), Kernel phát hiện và gửi một tín hiệu sự kiện (event). Dịch vụ `udev` sẽ bắt được tín hiệu đó, và ngay lập tức tạo ra MỘT file Device Node tương ứng trong `/dev/`. Khi bạn rút USB ra, file đó tự động biến mất.
- **Persistent Naming (Đặt tên cố định):** `udev` giải quyết được vấn đề tên thiết bị bị thay đổi khi cắm rút lộn xộn. Nó đảm bảo ổ cứng hay card mạng của bạn luôn được gán cho một cái tên cố định dựa trên các thông số phần cứng (MAC, UUID, PCI Slot).

### 4.3. Các thành phần của `udev`
- **`libudev`**: Thư viện dùng chung.
- **`udevd`**: Daemon (dịch vụ ngầm) chịu trách nhiệm theo dõi và quản lý thư mục `/dev`. (Hiện nay đã được tích hợp chặt chẽ vào `systemd`).
- **`udevadm`**: Công cụ dòng lệnh (CLI) để quản trị và chẩn đoán `udev`.

### 4.4. Cấu hình `udev`
- **File cấu hình chính:** `/etc/udev/udev.conf` (chứa các thiết lập quyền hạn, vị trí).
- **Thư mục quy tắc (Rules):** `/etc/udev/rules.d/` — Nơi cực kỳ quyền lực. Bạn có thể tự viết ra các "Quy tắc" (Rules) bắt hệ thống phải làm gì khi một phần cứng cụ thể được cắm vào (Ví dụ: Khi cắm USB hãng Sony vào, tự động mount và sao lưu dữ liệu, đồng thời đổi tên thiết bị thành `/dev/my_sony_usb`).

---

## 5. Tóm tắt nhanh (Cheat Sheet)
- **Character Device:** Giao tiếp dạng chuỗi byte (chuột, bàn phím, cổng serial).
- **Block Device:** Giao tiếp dạng khối lưu trữ có cache (HDD, SSD).
- **Network Device:** Giao tiếp dạng Packet/Socket (Card mạng - Không có file trong `/dev`).
- **Device Node:** File đại diện cho phần cứng trong `/dev`, có Major/Minor number.
- **`udev`:** Hệ thống tự động sinh và hủy các file thiết bị trong `/dev` dựa trên các sự kiện cắm/rút phần cứng thực tế.

---

## 6. Lab Nâng cao: Khám phá hoạt động của `udev` (Demo)

**Mục tiêu:** Quan sát trực tiếp cách hệ thống `udev` phát hiện một thiết bị mới được cắm vào (ví dụ: ổ USB), tạo ra Device Node trong thư mục `/dev/` và sau đó tự động dọn dẹp khi rút thiết bị ra.

> 📌 **Lưu ý:** Bài lab này được tóm tắt từ bài Demo trong khóa học. Để thực hành bài này, bạn cần sử dụng máy tính Linux vật lý hoặc một Local VM có hỗ trợ Passthrough USB. Nếu bạn đang dùng AWS EC2, bạn có thể tạo/attach một EBS volume mới thay cho USB để quan sát hiệu ứng tương tự, nhưng các thông báo log sẽ có chút khác biệt.

### Bước 1: Chuẩn bị 2 Terminal song song
Để quan sát toàn bộ quá trình một cách trực quan, hãy mở 2 cửa sổ terminal.

- **Terminal 1 (Theo dõi Kernel log):**
  Lệnh `dmesg` dùng để xem các thông điệp của Kernel. Ta thêm cờ `-w` (wait) để log liên tục cuộn lên mỗi khi có sự kiện mới (giống như xem camera giám sát).
  ```bash
  dmesg -w
  ```
  *(Mẹo: Bạn có thể nhấn Enter vài lần để tạo khoảng trống trên màn hình, giúp dễ nhận biết log mới sắp hiện ra).*

- **Terminal 2 (Kiểm tra Device Nodes):**
  Chúng ta sẽ liệt kê các ổ đĩa hiện tại đang có trong hệ thống.
  ```bash
  ls -l /dev/sd*
  ```
  *(Giả sử kết quả trả về chỉ có `/dev/sda`, `/dev/sda1`, `/dev/sdb`... - Đây là các ổ cứng có sẵn).*

### Bước 2: Cắm thiết bị mới vào (Plug-in)
Cắm một chiếc USB Flash Drive (hoặc gắn thêm một EBS volume nếu trên Cloud) vào máy.

1. **Quan sát Terminal 1 (`dmesg`):**
   Bạn sẽ lập tức thấy hàng loạt thông báo mới chớp lên. Kernel đã nhận diện được thiết bị phần cứng mới, đọc được nó là "Mass storage device" (thiết bị lưu trữ USB) hoặc "SCSI removable disk".
2. **Quan sát Terminal 2 (`/dev/`):**
   Gõ lại lệnh kiểm tra các file thiết bị:
   ```bash
   ls -l /dev/sd*
   ```
   Bạn sẽ thấy sự xuất hiện của các file mới tinh (ví dụ: `/dev/sdc`, `/dev/sdc1`, `/dev/sdc2`). 
   - `sdc` đại diện cho toàn bộ cái USB bạn vừa cắm.
   - `sdc1`, `sdc2` chứng tỏ bên trong USB đó có 2 phân vùng (partitions) khác nhau.
   Ai đã tạo ra các file này? Chính là **`udev`**! Nó đã bắt được tín hiệu từ Kernel ở Terminal 1 và tự động gán Major/Minor number để tạo file trong `/dev/`.

### Bước 3: Rút thiết bị ra (Plug-out)
Bây giờ, bạn hãy tháo chiếc USB đó ra (rút nóng).

1. **Terminal 1 (`dmesg`):**
   Kernel lập tức phát ra cảnh báo: "USB disconnect".
2. **Terminal 2 (`/dev/`):**
   Chạy lại lệnh:
   ```bash
   ls -l /dev/sd*
   ```
   Bạn sẽ thấy `/dev/sdc` và các phân vùng của nó đã **hoàn toàn biến mất**. Hệ thống `udev` đã tự động dọn dẹp sạch sẽ để tránh rác trong `/dev/`.

**Bài học rút ra (Takeaway):**
Qua demo này, bạn đã thấy sự kết hợp hoàn hảo giữa **Kernel** (phát hiện phần cứng ở tầng thấp) và **`udev`** (tạo file giao tiếp ở tầng người dùng). Nhờ `udev`, thư mục `/dev/` của chúng ta luôn gọn gàng và chỉ chứa các file của những thiết bị thực sự đang được cắm vào hệ thống ngay tại thời điểm đó.
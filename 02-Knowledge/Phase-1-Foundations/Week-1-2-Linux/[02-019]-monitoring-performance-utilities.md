# Công cụ Giám sát và Hiệu năng (Monitoring and Performance Utilities)

## Document Information
- **Doc ID:** 02-019
- **Summary:** Phân loại và tổng hợp các công cụ giám sát hệ thống trên Linux, bao gồm giám sát CPU/Tiến trình, Bộ nhớ, I/O (Ổ cứng) và Mạng.
- **Version:** 0.0.1
- **Date:** 2026-05-10
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
Các bản phân phối Linux luôn đi kèm với nhiều công cụ cấu hình và giám sát hiệu năng tiêu chuẩn. Rất nhiều công cụ trong số này thu thập dữ liệu trực tiếp từ hệ thống file ảo `/proc`. 

Để dễ dàng tra cứu, dưới đây là danh sách các công cụ giám sát dòng lệnh (CLI) được chia thành 4 nhóm chính. *(Cột "Package" biểu thị tên gói phần mềm chứa lệnh đó, tuy nhiên tên gói có thể thay đổi đôi chút tùy thuộc vào bản phân phối Linux bạn đang dùng).*

---

## 2. Công cụ Giám sát Tiến trình và Tải hệ thống (Process and Load Monitoring Utilities)

Nhóm công cụ này giúp bạn theo dõi mức sử dụng CPU, trạng thái quá tải (load average) và các tiến trình đang chạy.

| Công cụ (Utility) | Mục đích (Purpose) | Gói phần mềm (Package) |
|---|---|---|
| **`top`** | Xem hoạt động của các tiến trình, cập nhật liên tục theo thời gian thực (dynamically updated). | `procps` |
| **`uptime`** | Xem thời gian hệ thống đã chạy liên tục và Tải trung bình (average load). | `procps` |
| **`ps`** | Xem thông tin chi tiết (dạng tĩnh) về các tiến trình. | `procps` |
| **`pstree`** | Xem các tiến trình và mối liên hệ cha-con dưới dạng cây (tree). | `psmisc` (hoặc `pstree`) |
| **`mpstat`** | Xem mức sử dụng của nhiều CPU / Core. | `sysstat` |
| **`iostat`** | Xem mức độ sử dụng CPU và thống kê I/O (ổ cứng). | `sysstat` |
| **`sar`** | Hiển thị và thu thập lịch sử thông tin hoạt động của hệ thống. | `sysstat` |
| **`numastat`** | Xem thông tin về kiến trúc NUMA (Non-Uniform Memory Architecture). | `numactl` |
| **`strace`** | Theo dõi chi tiết tất cả các lời gọi hệ thống (system calls) mà một tiến trình thực hiện. | `strace` |

---

## 3. Công cụ Giám sát Bộ nhớ (Memory Monitoring Utilities)

Nhóm công cụ giúp chẩn đoán tình trạng RAM và Swap.

| Công cụ (Utility) | Mục đích (Purpose) | Gói phần mềm (Package) |
|---|---|---|
| **`free`** | Tóm tắt nhanh gọn về tình trạng sử dụng bộ nhớ (RAM & Swap). | `procps` |
| **`vmstat`** | Hiển thị chi tiết thống kê bộ nhớ ảo (virtual memory) và block I/O, cập nhật liên tục. | `procps` |
| **`pmap`** | Xem sơ đồ vùng nhớ (memory map) của một tiến trình cụ thể. | `procps` |

---

## 4. Công cụ Giám sát I/O Ổ cứng (I/O Monitoring Utilities)

Nhóm công cụ giúp tìm ra nguyên nhân gây nghẽn cổ chai (bottleneck) do ổ cứng đọc/ghi chậm.

| Công cụ (Utility) | Mục đích (Purpose) | Gói phần mềm (Package) |
|---|---|---|
| **`iostat`** | Xem mức độ sử dụng CPU và thống kê I/O (ổ cứng). | `sysstat` |
| **`iotop`** | Xem thống kê I/O trực tiếp (real-time) chi tiết đến từng tiến trình (giống như `top` nhưng dành cho ổ cứng). | `iotop` |
| **`sar`** | Hiển thị và thu thập lịch sử thông tin hoạt động của hệ thống (bao gồm cả I/O). | `sysstat` |
| **`vmstat`** | Hiển thị chi tiết thống kê bộ nhớ ảo và block I/O, cập nhật liên tục. | `procps` |

---

## 5. Công cụ Giám sát Mạng (Network Monitoring Utilities)

Nhóm công cụ theo dõi lưu lượng mạng và phân tích gói tin.

| Công cụ (Utility) | Mục đích (Purpose) | Gói phần mềm (Package) |
|---|---|---|
| **`netstat`** | Thống kê chi tiết mạng, các port đang mở và kết nối (hiện đang dần được thay thế bằng lệnh `ss` và `ip`). | `netstat` |
| **`iptraf`** | Thu thập và hiển thị thông tin trực quan về các giao diện mạng. | `iptraf` |
| **`tcpdump`** | Phân tích chi tiết từng gói tin (network packets) đi qua card mạng bằng command line. | `tcpdump` |
| **`wireshark`** | Công cụ cực kỳ mạnh mẽ để phân tích lưu lượng mạng (thường dùng qua giao diện đồ họa). | `wireshark` |

---

## 6. Công cụ Giám sát Giao diện Đồ họa (Graphical Monitoring Tools)

Dù phần lớn thời gian DevOps và SysAdmin sẽ làm việc qua Command Line (CLI), Linux vẫn cung cấp các công cụ đồ họa trực quan (GUI) rất mạnh mẽ nếu bạn sử dụng phiên bản Desktop.

### 6.1. GNOME System Monitor
- **Môi trường:** Đi kèm mặc định với GNOME Desktop (như Ubuntu Desktop).
- **Đặc điểm:** Tương tự như *Task Manager* trên Windows.
- **Tính năng:**
  - Hiển thị thống kê theo thời gian thực (real-time).
  - Tab **Resources:** Cung cấp biểu đồ trực quan về mức sử dụng CPU (từng core riêng biệt), Memory/Swap, và lưu lượng Mạng (Network nhận/gửi).
  - Tab **Processes:** Liệt kê tất cả các tiến trình đang chạy (giống lệnh `top`), cho phép bạn dễ dàng kill/stop một ứng dụng bị treo.
  - Tab **File Systems:** Xem dung lượng đã dùng/còn trống của tất cả các ổ đĩa và phân vùng.
- **Hạn chế:** Không có khả năng lưu trữ dữ liệu lịch sử để xem lại sau, và ít tùy chọn tùy biến giao diện hiển thị.

### 6.2. KSysGuard
- **Môi trường:** Đi kèm mặc định với KDE Desktop.
- **Đặc điểm:** Mạnh mẽ và tùy biến cao hơn rất nhiều so với GNOME System Monitor.
- **Tính năng nổi bật:**
  - Cho phép chọn chính xác những cảm biến (sensors) hoặc thông số nào bạn muốn hiển thị.
  - Có thể tạo nhiều cửa sổ (windows) theo dõi khác nhau cùng lúc.
  - **Giám sát từ xa (Remote monitoring):** Có thể kết nối và hiển thị biểu đồ của các máy chủ từ xa ngay trên màn hình máy bạn.
  - Có thể lưu lại các thiết lập (workspaces) đang làm việc.

> 💡 **Lưu ý:** Trên các phiên bản Linux dành cho Doanh nghiệp (Enterprise Linux), còn có rất nhiều công cụ đồ họa cấp cao khác chuyên biệt cho từng bản phân phối (ví dụ như Cockpit trên RHEL/CentOS). Tuy nhiên, những công cụ GUI này chỉ mang tính chất tham khảo (taste of what can be done), nền tảng thực sự của bạn vẫn phải là CLI.

---

## 7. Cài đặt và Chạy thử KSysGuard (Tùy chọn)

Nếu bạn đang sử dụng một môi trường Desktop Linux (như máy ảo Ubuntu Desktop) và muốn thử nghiệm KSysGuard, bạn có thể thực hiện theo các bước sau:

**Bước 1: Kiểm tra xem ksysguard đã được cài đặt chưa**
```bash
which ksysguardd
```
*Nếu lệnh không trả về đường dẫn nào, bạn cần phải cài đặt nó.*

**Bước 2: Cài đặt (Khuyến nghị dùng trình quản lý gói của hệ điều hành)**
- **Trên Ubuntu / Debian:**
  ```bash
  sudo apt-get install ksysguard
  ```
- **Trên Red Hat / CentOS:**
  ```bash
  sudo yum install ksysguard*
  ```
- **Trên SUSE:**
  ```bash
  sudo zypper install kdebase4-workspace
  ```
*(Lưu ý: Tên gói chính xác có thể thay đổi tùy phiên bản, bạn có thể phải tìm kiếm tên gói tương đương trong Software Center).*

**Bước 3: Chạy và Tùy biến KSysGuard**
1. Mở terminal và gõ `ksysguard` để khởi động (hoặc tìm trong menu ứng dụng).
2. **Tạo worksheet mới:** `File > New Worksheet`.
3. **Cấu hình worksheet:** `Edit > Properties` để tùy chỉnh số lượng hàng/cột hiển thị và tần suất cập nhật dữ liệu.
4. **Thêm cảm biến (Sensors):** Ở cột bên trái, bạn sẽ thấy tên máy của mình. Hãy nhấp vào đó để mở rộng danh sách các "sensor" (như CPU Load, Memory Used...). Bạn có thể **kéo và thả (drag & drop)** các sensor này sang cửa sổ worksheet bên phải để tạo biểu đồ. Bạn hoàn toàn có thể hiển thị nhiều CPU/Core vào cùng một cửa sổ biểu đồ.
5. **Kết nối máy chủ từ xa:** Chọn `File > Connect Host`. Nhập IP/Hostname của máy tính khác. *(Yêu cầu máy tính đó cũng phải cài đặt phiên bản ksysguard tương tự và cấu hình cho phép kết nối qua cổng ksysguardd).*

---

## 8. Thực hành (Hands-on Lab): Sử dụng Công cụ Giám sát Hệ thống (CLI)

**Mục tiêu:** Thử nghiệm chạy kết hợp nhiều công cụ giám sát dòng lệnh để theo dõi phản ứng của hệ thống khi phải xử lý một tác vụ ngốn CPU (Ví dụ: Biên dịch Kernel).

> 📌 **Lưu ý:** Trong bài Demo gốc, giảng viên dùng việc biên dịch (compile) Kernel để làm nặng CPU. Tuy nhiên, thao tác đó yêu cầu tải mã nguồn rất lớn. Thay vào đó, chúng ta sẽ **tự tạo ra một tác vụ ngốn CPU (CPU Stress)** bằng một mẹo dòng lệnh đơn giản có thể chạy ngay trên AWS EC2.

**Bước 1: Mở 2 cửa sổ Terminal (SSH) song song**
- **Terminal 1:** Sẽ dùng để chạy lệnh theo dõi hệ thống.
- **Terminal 2:** Sẽ dùng để tạo tải (stress) cho CPU.

**Bước 2: Bật công cụ theo dõi (Terminal 1)**
Trong Terminal 1, hãy gõ lệnh sau để theo dõi tổng thể Memory và I/O:
```bash
vmstat -a 2 1000
```
*(Mẹo: Mở thêm một Terminal thứ 3 và chạy lệnh `top` nếu màn hình của bạn đủ rộng).*

**Bước 3: Ép CPU chạy 100% (Terminal 2)**
Chạy lệnh vô nghĩa (nhưng ngốn CPU) sau đây ở Terminal 2:
```bash
# Lệnh này sẽ tạo ra một vòng lặp vô hạn, bắt 1 Core của CPU phải chạy hết công suất
while true; do true; done
```
*(Lưu ý: Lệnh này không in ra gì cả, nó chỉ âm thầm "ăn" CPU của bạn).*

**Bước 4: Quan sát sự quá tải (Terminal 1)**
Nhìn vào kết quả của lệnh `vmstat` đang chạy ở Terminal 1, bạn sẽ thấy:
- Cột **`us` (User CPU time)**: Đột ngột tăng vọt. Nếu EC2 của bạn có 1 Core (như t2.micro), nó sẽ nhảy lên gần 100%. (Ví dụ: Từ dòng thứ 7 trở đi, cột `us` nhảy từ `0` lên `47`, rồi `50` đều đặn).
- Cột **`id` (Idle time)**: Sẽ giảm mạnh về gần 0 (hoặc giảm còn 50% nếu server của bạn có 2 vCPUs mà bạn chỉ chạy 1 luồng tải).
- Cột **`r` (Runnable processes)**: Chuyển từ `0` sang `1` hoặc `2`, nghĩa là luôn có ít nhất 1 tiến trình đang xếp hàng để tranh giành CPU.
- Nếu bạn có mở `top` ở cửa sổ thứ 3, bạn sẽ thấy tiến trình `bash` đang đứng đầu bảng xếp hạng ăn CPU. (Nhấn phím `1` trong `top` để xem từng Core riêng biệt).

**Bước 5: Dọn dẹp (Cleanup) cực kỳ quan trọng**
Quay lại **Terminal 2** (nơi đang chạy vòng lặp) và nhấn **`Ctrl + C`** để ngắt (kill) tiến trình gây tải.
Ngay lập tức, bạn sẽ thấy cột `us` trong `vmstat` giảm xuống và cột `id` (rảnh rỗi) tăng vọt trở lại trạng thái bình thường.

**Bài học rút ra (Takeaway):**
Khi hệ thống có dấu hiệu quá tải, một DevOps giỏi không bao giờ chỉ nhìn vào một công cụ duy nhất. Bạn cần mở nhiều cửa sổ terminal: một bên dùng `top` (để tìm tiến trình tốn CPU), một bên dùng `vmstat` (để theo dõi sự lưu thông của RAM và ổ cứng) để có cái nhìn toàn cảnh về điểm nghẽn (bottleneck) của máy chủ.
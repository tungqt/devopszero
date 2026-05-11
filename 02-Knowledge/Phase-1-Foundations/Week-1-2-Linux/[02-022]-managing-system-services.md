# Quản lý Dịch vụ Hệ thống (Managing System Services)

## Document Information
- **Doc ID:** 02-022
- **Summary:** Tìm hiểu cách Linux quản lý các dịch vụ ngầm (services) chạy cùng hệ thống bằng công cụ `systemctl` (thuộc hệ sinh thái `systemd`).
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Quản lý Dịch vụ?**
Mọi hệ điều hành đều có những dịch vụ (services/daemons) được khởi chạy tự động khi máy bật lên (như SSH server, Web server Nginx, Database MySQL) và chúng sẽ chạy ngầm cho tới khi tắt máy. Là một DevOps, bạn thường xuyên phải khởi động lại (restart) ứng dụng sau khi thay đổi cấu hình, dừng (stop) ứng dụng khi có lỗi, hoặc cài đặt để ứng dụng tự động chạy lại (auto-start) nếu server bị reboot. Tất cả những việc này trên Linux hiện đại đều được quản lý thông qua một công cụ duy nhất: **`systemd`** và tiện ích dòng lệnh **`systemctl`**.

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Bất kỳ hệ thống Linux hiện đại nào (Ubuntu 16.04+, CentOS 7+, RHEL 7+, Debian 8+).
- **Yêu cầu:** 
  - Các lệnh **xem trạng thái** (`status`, `list-units`) có thể chạy bằng user thường.
  - Các lệnh **thay đổi trạng thái** (`start`, `stop`, `restart`, `enable`, `disable`) BẮT BUỘC phải dùng quyền `root` (`sudo`).

## 3. Công cụ `systemctl`

Như đã học ở bài System Initialization, tất cả các bản phân phối Linux mới đều đã chuyển sang dùng hệ thống **`systemd`**. Để quản lý mọi thứ trong `systemd`, bạn chỉ cần một công cụ là `systemctl`.

**Cú pháp cơ bản:**
```bash
systemctl [options] command [name]
```
*(Trong hầu hết các trường hợp, bạn có thể bỏ qua phần đuôi `.service` khi gõ tên dịch vụ. Ví dụ: gõ `sshd` thay vì `sshd.service`).*

### 3.1. Các lệnh Liệt kê (Liệt kê tất cả dịch vụ)
- **Xem trạng thái của MỌI THỨ do `systemd` quản lý:**
  ```bash
  systemctl
  ```
- **Liệt kê TẤT CẢ các dịch vụ (Kể cả đang chạy hay đã tắt):**
  ```bash
  systemctl list-units -t service --all
  ```
- **Chỉ liệt kê các dịch vụ ĐANG HOẠT ĐỘNG (Active):**
  ```bash
  systemctl list-units -t service
  ```

### 3.2. Các lệnh Bật / Tắt dịch vụ (Tạm thời)
Những lệnh này sẽ tác động ngay lập tức đến dịch vụ, nhưng **KHÔNG** làm thay đổi việc dịch vụ đó có tự bật lại khi khởi động lại server hay không.

- **Bật (Start) một dịch vụ:**
  ```bash
  sudo systemctl start foo
  # Hoặc: sudo systemctl start foo.service
  ```
- **Tắt (Stop) một dịch vụ:**
  ```bash
  sudo systemctl stop foo
  ```
- **Khởi động lại (Restart) một dịch vụ:** (Rất hay dùng khi vừa sửa file cấu hình của Nginx/MySQL).
  ```bash
  sudo systemctl restart foo
  ```
*(Lưu ý: Các lệnh này tương đương với lệnh cũ `sudo service foo start|stop` trên các hệ thống dùng SysVinit ngày xưa).*

### 3.3. Các lệnh Bật / Tắt tự khởi động (Cố định)
Những lệnh này quyết định việc một dịch vụ có được quyền **chạy tự động (auto-start)** cùng với hệ điều hành khi máy chủ (server) khởi động lên hay không. Nó **KHÔNG** làm dịch vụ bật hay tắt ngay tại thời điểm bạn gõ lệnh.

- **Cho phép tự khởi động cùng OS (Enable):**
  ```bash
  sudo systemctl enable sshd
  ```
- **Ngăn chặn tự khởi động cùng OS (Disable):**
  ```bash
  sudo systemctl disable sshd
  ```
*(Lưu ý: Lệnh này tương đương với công cụ `chkconfig on|off` trên hệ thống cũ).*

---

## 4. Thực hành (Hands-on Lab)

**Mục tiêu:** Thử nghiệm sử dụng `systemctl` để kiểm tra trạng thái và điều khiển một dịch vụ quan trọng đang chạy trên máy của bạn (Ví dụ: dịch vụ `ssh` hoặc `sshd`).

**Bước 1: Xem trạng thái một dịch vụ cụ thể**
Đây là lệnh DevOps dùng nhiều nhất khi cần debug xem vì sao app không chạy.
```bash
# Trên Ubuntu dùng tên ssh, trên CentOS/Amazon Linux dùng sshd
systemctl status ssh
```
*Kết quả sẽ hiển thị màu xanh lá cây `Active: active (running)` nếu nó đang hoạt động bình thường, và thường sẽ có kèm theo vài dòng log mới nhất của dịch vụ đó.*

**Bước 2: Xem các dịch vụ đang chạy trên máy**
```bash
systemctl list-units -t service
```
*Hãy thử tìm xem dịch vụ mạng, cron (lên lịch), và ssh đang chạy dưới tên gì.*

**Bước 3: Khởi động lại dịch vụ (An toàn)**
Việc khởi động lại dịch vụ SSH sẽ làm ngắt kết nối trong chớp mắt rồi chạy lại, nhưng thường sẽ không làm văng cửa sổ terminal hiện tại của bạn.
```bash
sudo systemctl restart ssh
```
*Sau khi chạy xong, hãy dùng lại lệnh `systemctl status ssh` (như Bước 1), bạn sẽ thấy thời gian ở dòng `Active:` vừa được reset lại thành vài giây trước.*

---

## 5. Lab Nâng cao: Thực hành Quản lý Dịch vụ với `systemctl` (Demo)

**Mục tiêu:** Thử nghiệm việc sử dụng `systemctl` để kiểm tra trạng thái, bật/tắt (start/stop) và tự động khởi động (enable/disable) các dịch vụ. Bài Lab này mô phỏng bài giảng về việc quản lý hai dịch vụ ảo hóa là VirtualBox (`vboxdrv`) và VMware (`vmware`).

> 📌 **Lưu ý:** Bài lab này được tóm tắt từ bài giảng Demo. Trên AWS EC2 của bạn có thể không cài sẵn VirtualBox hay VMware, vì vậy chúng ta sẽ **đọc hiểu quy trình phân tích** từ bài Demo này. (Nếu muốn thực hành thực tế, bạn có thể thay thế `vboxdrv` bằng dịch vụ `nginx` hoặc `cron` trên EC2 của bạn).

### Kịch bản 1: Quản lý Dịch vụ Chuẩn (VirtualBox - `vboxdrv`)

**Bước 1: Kiểm tra trạng thái hiện tại**
Giảng viên kiểm tra xem dịch vụ VirtualBox có đang chạy không:
```bash
sudo systemctl status vboxdrv
```
*Kết quả báo:* `Inactive (dead)`. Nghĩa là dịch vụ hiện không chạy.

**Bước 2: Khởi động dịch vụ (Start) và tác động của nó**
```bash
sudo systemctl start vboxdrv
sudo systemctl status vboxdrv
```
*Kết quả:* Trạng thái chuyển thành `Active (running)`. Đồng thời, dịch vụ này đã âm thầm tải các **Kernel Module** của VirtualBox vào hệ thống. Nếu bạn gõ lệnh `lsmod`, bạn sẽ thấy các module của VirtualBox vừa xuất hiện.

**Bước 3: Dừng dịch vụ (Stop)**
Giảng viên minh họa việc bạn có thể thêm đuôi `.service` vào sau lệnh (dù có thể bỏ qua):
```bash
sudo systemctl stop vboxdrv.service
```

### Kịch bản 2: Quản lý Dịch vụ Cũ / Tương thích ngược (VMware - `vmware`)

**Bước 1: Kiểm tra trạng thái của VMware**
```bash
sudo systemctl status vmware
```
*Kết quả thú vị:* Trạng thái báo `Inactive`, nhưng trong output có nhắc đến dòng chữ **`systemd-sysv-generator`**. Điều này có nghĩa là VMware **chưa được viết chuẩn theo `systemd`** mà vẫn dùng script khởi động theo kiểu `SysVinit` cũ kỹ của thập kỷ trước. Tuy nhiên, `systemd` rất thông minh, nó có một "lớp tương thích" (compatibility layer) để tự động dịch các lệnh `systemctl` sang lệnh cũ để chạy mượt mà.

**Bước 2: Bật và Tắt VMware**
Mọi thứ diễn ra y hệt như VirtualBox.
```bash
sudo systemctl start vmware
# Chạy lsmod sẽ thấy 5 kernel modules của VMware vừa được load lên RAM
sudo systemctl stop vmware
```

### Kịch bản 3: Thiết lập Khởi động cùng Hệ thống (Enable / Disable)

Nếu muốn máy chủ mỗi lần bật lên là có sẵn VMware và VirtualBox (mà không phải gõ lệnh start bằng tay), bạn dùng lệnh `enable`:
```bash
sudo systemctl enable vmware
sudo systemctl enable vboxdrv
```
*Lưu ý khi enable VMware:* Do nó là dịch vụ kiểu cũ, bạn sẽ thấy thông báo lệnh bị điều hướng (rerouted) tới công cụ `/sbin/chkconfig` (công cụ cấu hình khởi động của SysVinit cũ).

> 🚨 **CẢNH BÁO: Rất quan trọng!**
> Việc chạy lệnh `enable` **KHÔNG HỀ LÀM DỊCH VỤ ĐÓ START NGAY LẬP TỨC**. Nếu lúc nãy bạn đã gõ `stop`, thì bây giờ gõ `enable`, dịch vụ đó vẫn đang tắt (chạy `lsmod` sẽ không thấy module). Nó chỉ quy định hành vi cho **LẦN KHỞI ĐỘNG TIẾP THEO** của máy chủ mà thôi.

**Kết bài:** Vì VirtualBox và VMware làm cùng một việc (ảo hóa) và thường "đá" nhau khi giành quyền điều khiển phần cứng của Linux Kernel, giảng viên quyết định tắt chế độ khởi động tự động của VirtualBox đi để tránh xung đột vào lần boot sau:
```bash
sudo systemctl disable vboxdrv
```

**Bài học rút ra (Takeaway):**
Qua demo này, bạn thấy sự liên kết chặt chẽ giữa **System Services** (quản lý bởi `systemctl`) và **Kernel Modules** (quản lý bởi `modprobe/lsmod`). Khi một service chạy, nó thường gọi các module tương ứng vào Kernel. Đồng thời, bạn cũng thấy sự tương thích ngược tuyệt vời của `systemd` khi nó vẫn có thể quản lý các script `SysVinit` cũ kỹ.
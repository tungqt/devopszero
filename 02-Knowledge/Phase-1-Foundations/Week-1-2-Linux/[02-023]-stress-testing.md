# Giả lập Tải Hệ thống (Using `stress` and `stress-ng`)

## Document Information
- **Doc ID:** 02-023
- **Summary:** Hướng dẫn cài đặt và sử dụng công cụ `stress` và `stress-ng` để giả lập tình trạng quá tải (CPU, RAM, I/O) phục vụ cho việc kiểm thử và theo dõi hiệu năng hệ thống Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-11
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

Đôi khi, để kiểm tra tính ổn định của hệ thống hoặc để xem cách các công cụ giám sát hoạt động dưới áp lực lớn, chúng ta cần chủ động "gây khó dễ" (stress) cho hệ thống bằng cách ép CPU làm việc cật lực, tiêu tốn RAM (Memory) một cách lãng phí, hoặc đẩy các hoạt động đọc/ghi ổ đĩa (I/O) lên mức tối đa.

- **`stress`**: Là một chương trình viết bằng ngôn ngữ C do Amos Waterland tại Đại học Oklahoma phát triển. Nó được thiết kế đơn giản để tạo ra một lượng tải (workload) có thể cấu hình được trên bất kỳ hệ thống POSIX nào.
- **`stress-ng`**: Là một phiên bản nâng cấp, mới hơn của `stress`. Công cụ này bao gồm số lượng tùy chọn gần như vô hạn với hơn 105 bài kiểm tra stress khác nhau. Đáng chú ý là `stress-ng` **tương thích ngược** (backwards compatible) hoàn toàn với cú pháp lệnh của `stress`.

Vì `stress-ng` bao hàm `stress`, bạn có thể dùng `stress-ng` thay cho `stress` trong mọi trường hợp bằng cách gọi lệnh `stress-ng --help` để xem các tùy chọn.

---

## 2. Môi trường Thực hành (Environment Setup)

- **Môi trường:** Máy chủ Linux bất kỳ (AWS EC2 hoặc Local VM).
- **Quyền hạn:** Cần quyền `sudo` để cài đặt gói phần mềm.
- **Công cụ hỗ trợ:** Bạn nên mở sẵn một Terminal khác chạy các công cụ giám sát như `top`, `htop`, hoặc `vmstat` để theo dõi hệ thống. Trên môi trường Desktop Linux, bạn có thể dùng GUI như `gnome-system-monitor` hoặc `ksysguard`.

---

## 3. Cài đặt công cụ

Ngày nay, phần lớn các bản phân phối Linux (distributions) lớn đều có sẵn cả hai công cụ này trong kho lưu trữ (repository) mặc định của họ.

Bạn có thể cài đặt dễ dàng bằng trình quản lý gói tương ứng:

- **Trên Red Hat / CentOS cũ (Dùng `yum`):**
  ```bash
  sudo yum install stress stress-ng
  ```
- **Trên Fedora / RHEL mới (Dùng `dnf`):**
  ```bash
  sudo dnf install stress stress-ng
  ```
- **Trên Ubuntu / Debian (Dùng `apt-get`):**
  ```bash
  sudo apt-get install stress stress-ng
  ```
- **Trên openSUSE (Dùng `zypper`):**
  ```bash
  sudo zypper install stress stress-ng
  ```

---

## 4. Các lệnh và Tham số cơ bản

Cú pháp tổng quát khi sử dụng lệnh để đưa hệ thống vào trạng thái quá tải:
```bash
stress [OPTIONS]
# Hoặc
stress-ng [OPTIONS]
```

### Ví dụ minh họa:
```bash
stress -c 8 -i 4 -m 6 -t 20s
```

**Giải thích ý nghĩa từng tham số từ lệnh trên:**
- **`-c 8` (CPU):** Sinh ra (Fork) 8 tiến trình sử dụng CPU liên tục. Mỗi tiến trình sẽ liên tục tính toán hàm căn bậc hai `sqrt()` để ép CPU phải làm việc.
- **`-i 4` (I/O):** Sinh ra 4 tiến trình gây tải I/O lên ổ cứng. Mỗi tiến trình sẽ liên tục gọi hàm `sync()` (ép dữ liệu từ RAM ghi xuống ổ đĩa).
- **`-m 6` (Memory):** Sinh ra 6 tiến trình gây tải cho RAM. Mỗi tiến trình sẽ gọi hàm `malloc()` để cấp phát mặc định **256 MB RAM**. (Bạn có thể điều chỉnh dung lượng bằng tham số `--vm-bytes 128M`).
- **`-t 20s` (Timeout):** Thiết lập thời gian chạy bài kiểm tra là **20 giây** sau đó tự động kết thúc.

---

## 5. Lab Nâng cao: Thực hành quá tải Hệ thống (Demo)

**Mục tiêu:** Thấy được cách hệ thống phản ứng khi bị ép hết tài nguyên, và làm quen với việc sử dụng công cụ `stress` kết hợp với các công cụ giám sát.

### Bước 1: Chuẩn bị công cụ giám sát
Trước khi gây tải, bạn cần phải có mắt để nhìn hệ thống "chịu đựng" như thế nào.
- **Nếu dùng Linux Desktop (Local VM):** Mở trình quản lý đồ họa như `gnome-system-monitor` hoặc `ksysguard`.
- **Nếu dùng AWS EC2 (CLI):** Hãy mở một Terminal SSH thứ 2 và chạy lệnh:
  ```bash
  top
  ```
  *(Nhấn phím `1` để xem từng Core CPU, và phím `M` lớn để ưu tiên sắp xếp theo mức dùng RAM).*

### Bước 2: Bắt đầu gây tải (Stress)
Trở lại Terminal chính, hãy chạy lệnh sau:
```bash
stress -m 4 -t 20s
```
**Phân tích lệnh:** Lệnh này chỉ gây tải lên RAM (Memory stressor) với 4 tiến trình (tương đương 4 * 256MB = 1GB RAM) trong vòng 20 giây.

### Bước 3: Quan sát Hiện tượng
* Mặc dù lệnh chỉ mục đích đánh vào **Memory (`-m`)**, nhưng bạn hãy nhìn sang cửa sổ `top` hoặc System Monitor.
* Rất có thể bạn sẽ thấy **CPU cũng bị chiếm dụng tối đa (100%)**. Điều này xảy ra do quá trình liên tục xin cấp phát RAM (`malloc`) và giải phóng nó đòi hỏi Kernel phải làm việc cực kỳ vất vả, dẫn đến việc CPU cũng bị kéo theo vào quá trình này.

### Bước 4: Thử nghiệm thêm (Tự do)
Tùy thuộc vào số lượng Core CPU và dung lượng RAM thực tế trên máy của bạn (Ví dụ: trên EC2 `t2.micro` thường chỉ có 1 Core, 1GB RAM), bạn hãy tự điều chỉnh và phối hợp các tham số:

* Thử chỉ stress CPU: `stress -c 2 -t 30s`
* Thử kết hợp CPU và I/O: `stress -c 1 -i 2 -t 15s`

> 💡 **Takeaway:** Chơi đùa với các công tắc này sẽ giúp bạn hiểu sâu sắc về cách các tài nguyên hệ thống (CPU, RAM, Disk) có mối liên hệ mật thiết và tác động qua lại với nhau. Trong các tình huống sửa lỗi thực tế sau này, việc dùng `stress` để giả lập "mức tải cao" sẽ rất hữu ích.
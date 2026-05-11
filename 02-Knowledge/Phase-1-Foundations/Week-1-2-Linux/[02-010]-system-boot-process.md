# Quá trình Khởi động Hệ thống (System Boot Process)

## Document Information
- **Doc ID:** 02-010
- **Summary:** Tìm hiểu chi tiết về quá trình khởi động của hệ thống Linux, vai trò của BIOS/CMOS, Bootloader (GRUB2, Das U-Boot) và Kernel.
- **Version:** 0.0.1
- **Date:** 2026-05-07
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
Tại sao DevOps cần hiểu về quá trình khởi động hệ thống? 
Khi quản lý hoặc triển khai máy chủ, sẽ có những trường hợp hệ thống gặp lỗi ngay từ lúc khởi động (kernel panic, sai cấu hình boot, mất bootloader, v.v.). Việc nắm rõ các bước từ lúc nhấn nút nguồn đến khi hệ điều hành sẵn sàng giúp bạn khoanh vùng và xử lý sự cố nhanh chóng. Ngoài ra, nó cũng giúp bạn tinh chỉnh hệ thống, thay đổi kernel, hoặc cấu hình tham số khởi động (boot parameters) phục vụ cho các yêu cầu bảo mật, hiệu năng.

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Local Linux VM hoặc AWS EC2 instance.
- **Yêu cầu:** Bất kỳ bản phân phối Linux nào (Ubuntu, CentOS, RHEL...). Hầu hết các lệnh xem file cấu hình GRUB đều có thể thực thi ở môi trường user thường, tuy nhiên cần quyền `root` (thông qua `sudo`) nếu bạn muốn cập nhật GRUB.

## 3. Các Giai đoạn Khởi động Cơ bản (Boot Phases)

### 3.1. BIOS / CMOS Phase
- Khi hệ thống mới khởi động, phần mềm đầu tiên chạy là **BIOS** (Basic Input/Output System) hoặc **UEFI** (Unified Extensible Firmware Interface) trên các hệ thống mới.
- **Nhiệm vụ:**
  - POST (Power-On Self-Test): Kiểm tra và khởi tạo các thiết bị phần cứng quan trọng (RAM, Keyboard, Hard Disk...).
  - Dựa trên cấu hình BIOS/CMOS, nó sẽ tìm thiết bị khởi động (Boot Device) như ổ cứng, USB, Network... và tải **Bootloader** từ thiết bị đó vào bộ nhớ.
- Trên các hệ thống cũ, BIOS sẽ tìm **MBR** (Master Boot Record) - một khu vực nhỏ ở đầu ổ cứng chứa bảng phân vùng (partition table) và bootloader.

### 3.2. Bootloader Phase (GRUB)
**Bootloader** là chương trình chịu trách nhiệm tải hệ điều hành.

- **Trên hệ thống x86 (PC, Laptop, Server):** Hầu hết sử dụng **GRUB** (GRand Unified Bootloader).
- **Trên thiết bị nhúng (Embedded devices):** Các thiết bị như Raspberry Pi, router, thiết bị y tế thường dùng các bootloader nhẹ hơn như **Das U-Boot**.

**Các tính năng nổi bật của GRUB:**
- Chọn hệ điều hành (Multi-boot): Nếu máy có Windows, Ubuntu, RedHat... bạn có thể chọn OS lúc khởi động.
- Chọn phiên bản Kernel: Cho phép bạn khởi động bằng kernel cũ nếu kernel mới bị lỗi.
- Chỉ định Initial Ramdisk (initrd/initramfs) và các tham số khởi động (boot options).
- **Interactive Mode:** GRUB cho phép bạn nhấn phím (thường là `e`) lúc boot để sửa các tham số khởi động *trực tiếp trên RAM (on the fly)* mà không cần chỉnh sửa file cấu hình vật lý. Điều này rất hữu ích để cứu hộ hệ thống (ví dụ: reset mật khẩu root).

**Lưu ý:** Hầu hết các bản phân phối Linux hiện đại đều đã chuyển sang dùng **GRUB 2**. Phiên bản GRUB 1 (Legacy GRUB) hiện không còn phổ biến.

### 3.3. Kernel Phase (Thư mục `/boot`)
Sau khi Bootloader hoàn thành, nó sẽ tải **Kernel** (nhân hệ điều hành) vào RAM. Các file liên quan đến quá trình này thường nằm trong thư mục `/boot`.

Trong một hệ thống, có thể có nhiều phiên bản kernel được cài đặt song song. Mỗi kernel thường sẽ có 4 file đi kèm mang cùng một hậu tố phiên bản (ví dụ: `-5.9.13`):

1. **`vmlinuz`**: Là file Kernel đã được nén (compressed). Chữ `z` ở cuối biểu thị điều này. Khi tải vào RAM, nó sẽ tự động giải nén.
2. **`initramfs` (hoặc `initrd`)**: Chứa một root filesystem khởi tạo hoàn chỉnh được tải dưới dạng ramdisk (ổ đĩa trên RAM). Nó cũng chứa các kernel module thiết yếu (thường là driver thiết bị) và các chương trình cần thiết để tải filesystem thực sự của hệ điều hành. Sau khi filesystem thực sự được mount, initramfs sẽ bị loại bỏ.
3. **`config`**: Chứa tất cả các thông tin chi tiết về cách Kernel này được biên dịch (compiled). File này không cần thiết cho quá trình hoạt động bình thường của hệ thống.
4. **`System.map`**: Liệt kê bảng biểu tượng (symbol table) hoàn chỉnh của Kernel. File này chủ yếu được sử dụng cho mục đích gỡ lỗi (debugging).

Sau khi Kernel được giải nén và `initramfs` hoàn thành nhiệm vụ, Kernel sẽ tiếp quản phần cứng và gọi tiến trình đầu tiên của hệ thống (`init` hoặc `systemd`).

---

## 4. Cấu hình GRUB 2

Việc cấu hình GRUB 2 phức tạp hơn vì nó tự động sinh ra file cấu hình chính dựa trên các file mẫu. Các thành phần quan trọng bao gồm:

### 4.1. File `grub.cfg`
- Thường nằm ở `/boot/grub/grub.cfg` hoặc `/boot/grub2/grub.cfg` (tùy OS).
- Đây là file cấu hình chính. **TUYỆT ĐỐI KHÔNG SỬA TRỰC TIẾP TẬP TIN NÀY**. Nó được tạo tự động khi bạn chạy lệnh cập nhật GRUB, cài mới kernel, v.v.

### 4.2. Thư mục `/etc/grub.d/`
- Chứa các shell scripts điều khiển cách GRUB hoạt động và sinh ra file cấu hình (ví dụ: script tự động quét tìm hệ điều hành khác, script cài đặt giao diện/theme).

### 4.3. File `/etc/default/grub`
- Chứa các tham số cơ bản mà GRUB cần (như thời gian đếm ngược `GRUB_TIMEOUT`, tham số truyền cho kernel `GRUB_CMDLINE_LINUX`).
- **Đây là file bạn sẽ sửa** nếu muốn thay đổi tham số khởi động mặc định.
- **Quy trình chuẩn để cập nhật GRUB:** Sửa file `/etc/default/grub` ➔ Chạy lệnh `update-grub` (trên Ubuntu/Debian) hoặc `grub2-mkconfig -o /boot/grub2/grub.cfg` (trên RHEL/CentOS).

---

## 5. Thực hành (Hands-on Lab)

**Mục tiêu:** Kiểm tra cấu hình GRUB trên máy chủ của bạn (chỉ xem, không thay đổi để đảm bảo an toàn).

**Bước 1:** Xem nội dung file cấu hình GRUB mặc định.
```bash
cat /etc/default/grub
```
*Quan sát tham số `GRUB_TIMEOUT` (thời gian chờ mặc định) và `GRUB_CMDLINE_LINUX` (các cờ gửi cho Kernel).*

**Bước 2:** Liệt kê các scripts sinh cấu hình GRUB.
```bash
ls -l /etc/grub.d/
```
*Bạn sẽ thấy các file đánh số như `00_header`, `10_linux`... Các file này sẽ được thực thi theo thứ tự số từ nhỏ đến lớn.*

**Bước 3:** Xem file Kernel hiện tại đang được sử dụng.
```bash
ls -lh /boot/vmlinuz*
```
*Để ý các file bắt đầu bằng `vmlinuz`, chữ `z` là dấu hiệu Kernel được nén.*

**Bước 4:** Kiểm tra phiên bản Kernel đang chạy.
```bash
uname -r
```
*So sánh phiên bản hiện tại với các file `vmlinuz` trong thư mục `/boot/`.*

---

## 6. Lab Nâng cao: Chỉnh sửa GRUB On-the-fly (Interactive Mode)

**Mục tiêu:** Thử nghiệm thay đổi tham số khởi động của Kernel trực tiếp trên RAM (không làm thay đổi cấu hình vĩnh viễn) để xem quá trình khởi động dạng Verbose (hiển thị chi tiết log).

**Cảnh báo:** Bạn **CHỈ** có thể thực hiện lab này trên **Local VM (máy ảo cài trên máy cá nhân như VMware, VirtualBox)**. **KHÔNG** nên/không thể thực hiện trực tiếp trên Cloud VM (như AWS EC2) vì Cloud VM không cung cấp màn hình console trực tiếp (VGA Console) lúc hệ thống đang boot để bạn có thể nhấn phím.

> 📌 **Note (2026-05-07):** Hiện tại đang thực hành trên môi trường AWS EC2 nên không thể làm bài Lab Nâng cao này. Đã đánh dấu để quay lại thực hành sau khi cài đặt được máy ảo (Local VM).

**Các bước thực hiện:**
1. **Khởi động lại (Reboot) hệ thống.** Nếu bạn đang dùng máy ảo (như VMware/VirtualBox), hãy chọn Restart.
2. Ngay khi hệ thống vừa bật lên, màn hình menu của GRUB sẽ xuất hiện. Nếu không thấy, bạn có thể cần giữ phím `Shift` (với BIOS) hoặc nhấn liên tục phím `Esc` (với UEFI) trong lúc máy vừa khởi động để ép GRUB menu hiện ra.
3. Dùng phím mũi tên di chuyển đến dòng chứa Kernel bạn muốn khởi động (thường là tuỳ chọn đầu tiên như `Ubuntu` hoặc nằm trong `Advanced options for Ubuntu`).
4. Nhấn phím **`e`** (Edit) để vào chế độ chỉnh sửa.
5. Sử dụng phím mũi tên để cuộn xuống dòng bắt đầu bằng chữ **`linux`** (dòng này chứa đường dẫn tới file `vmlinuz` và các tham số khởi động).
6. Tìm chữ **`quiet`** trên dòng đó và xóa nó đi (hoặc thay bằng từ khác). Tùy chọn `quiet` có chức năng ẩn đi các log hệ thống lúc khởi động để màn hình boot nhìn "sạch sẽ" hơn. Khi xóa nó đi, hệ thống sẽ chuyển sang chế độ **Verbose**.
7. Nhấn **`Ctrl + X`** hoặc **`F10`** để bắt đầu boot với cấu hình vừa sửa.
8. **Quan sát kết quả:** Bạn sẽ thấy rất nhiều dòng log (chữ trắng nền đen) chạy liên tục trên màn hình thể hiện chi tiết từng module và dịch vụ đang được khởi chạy, cho đến khi màn hình đăng nhập (login) hiện ra.
9. Đăng nhập vào hệ thống bình thường. Lần khởi động tiếp theo, thiết lập này sẽ tự động biến mất do chúng ta chỉ chỉnh sửa trên RAM.
# Các Lệnh và Công cụ Cơ bản (Basic Commands and Utilities)

## Document Information
- **Doc ID:** 02-017
- **Summary:** Liệt kê và phân loại các nhóm lệnh Linux cơ bản thường dùng trong quản trị hệ thống, xử lý file, phân quyền và mạng.
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần nắm vững các lệnh cơ bản?**
Hầu hết các lệnh cơ bản trên Linux đều giống hoặc tương tự với các hệ điều hành họ UNIX khác (như macOS, FreeBSD). Mặc dù có thể có đôi chút khác biệt về tùy chọn (options) hoặc cú pháp (syntax) tùy theo phiên bản, nhưng mục đích sử dụng vẫn giữ nguyên. Việc thành thạo những nhóm lệnh này là yêu cầu bắt buộc đối với bất kỳ ai làm việc với Linux server, giúp bạn điều hướng hệ thống, xử lý text, quản lý tiến trình và xử lý sự cố mạng một cách nhanh chóng.

## 2. Phân loại Các Nhóm Lệnh Cơ bản

Dưới đây là danh sách các lệnh thiết yếu được phân loại theo mục đích sử dụng. (Đây là tài liệu tra cứu - Cheat Sheet, bạn không cần học thuộc lòng ngay nhưng cần biết chúng tồn tại để tra `man` khi cần).

### 2.1. Nén và Giải nén File (File Compression)
Sử dụng để giảm dung lượng file hoặc gộp nhiều file lại với nhau (đặc biệt quan trọng khi sao lưu / backup log):
- **bzip2 family:** `bzip2`, `bunzip2`, `bzcat`, `bdiff`, `bzless`
- **gzip family (rất phổ biến):** `gzip`, `gunzip`, `gzexe`, `zcat`, `zless`
- **zip family (tương thích Windows):** `zip`, `unzip`
- **xz family (tỉ lệ nén cực cao):** `xz`, `unxz`, `xzcat`

### 2.2. Sở hữu, Quyền hạn và Thuộc tính (File Ownership, Permissions & Attributes)
Dùng để quản lý bảo mật và kiểm soát truy cập trên hệ thống:
- `chown`: Thay đổi chủ sở hữu (Owner) của file.
- `chgrp`: Thay đổi nhóm (Group) sở hữu file.
- `chmod`: Thay đổi quyền (Permissions: Read, Write, Execute).
- `attr`: Quản lý các thuộc tính mở rộng (Extended attributes).

### 2.3. Xử lý File và Văn bản (Files & Text Processing)
Đây là nhóm lệnh cực kỳ quan trọng giúp bạn đọc log, lọc dữ liệu và tự động hóa:
- **Đọc và xem file:** `cat`, `less`, `more`, `head`, `tail`
- **Thao tác sao chép/di chuyển:** `cp`, `dd`
- **Lọc và tìm kiếm:** `grep`, `egrep`, `fgrep`
- **Thao tác text / xử lý chuỗi:** `awk`, `sed`, `cut`, `split`, `csplit`, `join`, `fmt`, `expand`
- **Xử lý đường dẫn:** `basename`, `dirname`
- **Lưu trữ (Archive):** `tar`, `cpio`
- **Thông tin file:** `file`

### 2.4. Hệ thống Tập tin và Thư mục (Filesystem)
Dùng để điều hướng và quản lý ổ đĩa, thư mục:
- **Điều hướng thư mục:** `cd`, `pushd`, `popd`, `dirs`
- **Tạo / Xóa:** `mkdir`, `rmdir`, `rm`
- **Liệt kê và Đổi tên:** `ls`, `mv`
- **Liên kết (Links):** `ln`
- **Quản lý ổ đĩa:** `df` (dung lượng trống), `du` (dung lượng thư mục), `fdisk` (phân vùng), `fsck` (sửa lỗi filesystem)
- **Hệ thống:** `chroot` (thay đổi root directory), `fuser` (xem tiến trình nào đang dùng file)

### 2.5. Mạng (Networking)
Dùng để xem thông tin, cấu hình và xử lý sự cố kết nối mạng:
- **Thông tin Host:** `hostname`, `domainname`
- **Giao diện & Cấu hình:** `ip`, `ifconfig`, `route`
- **Kiểm tra kết nối/Port:** `netstat`
- **Phân giải tên miền:** `host`
- **Công cụ khác:** `arp`, `finger`, `ftp`

### 2.6. Quản lý Tiến trình và Công việc (Job Control)
Kiểm soát các chương trình đang chạy và lập lịch tự động:
- **Điều khiển tiến trình:** `kill`, `killall`, `exec`, `exit`
- **Lập lịch (Scheduling):** `crontab` (định kỳ), `at`, `atrm`, `batch` (chạy 1 lần)
- **Quản lý IPC (Inter-Process Communication):** `ipcs`, `ipcrm`

### 2.7. Biểu thức và Tính toán (Expression Evaluation)
Dùng trong bash script để tính toán toán học hoặc đánh giá logic:
- **Toán học:** `bc` (máy tính console), `dc`, `factor`
- **Logic / Đánh giá:** `eval`, `expr`, `true`, `false`

> 💡 **Note:** Còn rất nhiều lệnh khác không được liệt kê ở đây. Tuy nhiên, việc nắm vững nhóm lệnh `Xử lý File` (như `grep`, `awk`, `sed`, `tar`) và `Mạng` (`ip`, `netstat`) sẽ giúp bạn giải quyết được 90% công việc hàng ngày của một DevOps Engineer.

---

## 3. Thực hành (Hands-on Lab)

**Mục tiêu:** Sử dụng kết hợp nhiều lệnh cơ bản để giải quyết một bài toán thực tế (Piping).

**Kịch bản:** Bạn muốn tìm 5 file/thư mục có dung lượng lớn nhất trong thư mục `/var/log` để dọn dẹp ổ cứng.

**Lệnh thực thi:**
```bash
sudo du -sh /var/log/* | sort -rh | head -n 5
```

**Giải thích sự kết hợp (Piping):**
1. `du -sh /var/log/*`: Tính toán dung lượng của từng file/thư mục bên trong `/var/log` ở định dạng dễ đọc (Human readable: MB, GB).
2. `|`: Đẩy kết quả của lệnh trước làm đầu vào cho lệnh sau.
3. `sort -rh`: Sắp xếp (`sort`) theo thứ tự ngược (`-r`: từ lớn đến bé), hiểu định dạng Human readable (`-h`).
4. `| head -n 5`: Chỉ lấy 5 dòng đầu tiên (`head`).

Bài thực hành nhỏ này cho thấy sức mạnh thực sự của Linux nằm ở việc kết hợp (Piping) các lệnh cơ bản lại với nhau để tạo ra các công cụ xử lý mạnh mẽ.
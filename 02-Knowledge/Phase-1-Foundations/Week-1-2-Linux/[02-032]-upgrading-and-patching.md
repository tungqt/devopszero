# Cập nhật và Vá lỗi (Upgrading and Patching)

## Document Information
- **Doc ID:** 02-032
- **Summary:** Lý thuyết về quy trình nâng cấp và vá lỗi phần mềm trên Linux, sự khác biệt so với các OS khác (cài mới nguyên gói thay vì patch nhỏ), và bảng tổng hợp đối chiếu các lệnh quản lý gói giữa họ RPM và DEB.
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tại sao phải cập nhật hệ thống?

Thỉnh thoảng, phần mềm trên hệ thống bắt buộc phải được cập nhật (upgraded/updated) hoặc vá lỗi (patched) vì một trong các lý do sau:
- Tích hợp các tính năng mới (New features).
- Tối ưu hóa và cải thiện hiệu năng (Performance improvements).
- **Sửa các lỗi bảo mật và bug (Security and bug fixes).**

Trên nhiều hệ điều hành (như Windows), việc cập nhật thường tốn nhiều công sức, dễ sinh lỗi và thường xuyên **bắt buộc phải khởi động lại máy (reboots)**. Việc reboot này cực kỳ gây gián đoạn cho các máy chủ (server systems), do đó các System Administrator (SysAdmin) thường rất cẩn trọng khi triển khai bản vá.

---

## 2. Mô hình Cập nhật của Linux

**Linux không sử dụng "mô hình vá lỗi" (patching model) truyền thống.**
Thay vì tải một file patch nhỏ để sửa vài dòng code, Linux thường **cài đặt lại một gói (package) hoàn toàn mới**.

- **Ví dụ với công cụ cấp thấp (Low-level):**
  Trên hệ thống RPM, bạn sẽ dùng lệnh nâng cấp (Update - chữ `U` viết hoa):
  ```bash
  sudo rpm -Uvh libaio-devel-0.3.109-12.el7.x86_64.rpm
  ```
  *(Tương tự với hệ thống Debian là dùng lệnh `dpkg` cài đặt đè lên phiên bản cũ).*

- **Ví dụ với công cụ cấp cao (High-level - Khuyên dùng):**
  Các công cụ này sẽ tự động ra ngoài Internet, kiểm tra xem có bản cập nhật không, tải về, cài đặt và đồng thời cập nhật luôn các thư viện phụ thuộc đang bị lỗi thời.
  ```bash
  sudo dnf update libaio-devel   # Trên Fedora/RHEL/CentOS
  sudo zypper update libaio-devel # Trên SUSE
  sudo apt upgrade libaio-dev    # Trên Debian/Ubuntu
  ```

---

## 3. Cập nhật Toàn bộ Hệ thống (System-wide Update)

Để nâng cấp tất cả các phần mềm trên hệ thống lên phiên bản mới nhất (có sẵn trong repository):
```bash
sudo dnf update          # Fedora/RHEL/CentOS
sudo zypper update       # SUSE
sudo apt update          # Debian/Ubuntu (Lệnh này chỉ tải danh sách mới về)
sudo apt dist-upgrade    # Debian/Ubuntu (Lệnh này mới thực sự cài đặt cập nhật)
```
*(Lưu ý: Đối với Debian/Ubuntu, bạn luôn phải chạy `apt update` trước để đồng bộ danh sách, sau đó mới chạy `upgrade` hoặc `dist-upgrade`).*

### Delta RPMs
Để tránh việc phải tải toàn bộ file package lớn (dù chỉ thay đổi một chút), một số hệ điều hành hỗ trợ **Delta RPMs**. Nó chỉ tải về phần dung lượng chênh lệch (patch), sau đó dùng phần chênh lệch đó để "tái tạo" (recreate) lại toàn bộ file rpm gốc trên máy bạn.
*(Tuy nhiên, nếu mạng của bạn đủ nhanh, thời gian CPU bỏ ra để tái tạo file có khi còn chậm hơn là tải thẳng file gốc về).*

### Không cần Khởi động lại (Zero Reboots)
**Tuyệt tác của Linux:** Lần duy nhất bạn bắt buộc phải khởi động lại (reboot) một máy chủ Linux trong quá trình cập nhật là **khi bạn cập nhật chính Kernel của nó**. Mọi phần mềm khác đều có thể cập nhật và chạy tiếp mà không cần reboot.

---

## 4. Bảng Tra cứu Nhanh (Cheat Sheet) Các Lệnh Quản lý Gói

Dưới đây là bảng so sánh tương đương giữa các thao tác quản lý gói trên hệ thống RPM (Red Hat/CentOS/Fedora) và DEB (Debian/Ubuntu).
*(Lưu ý: Các lệnh `zypper` của SUSE gần như y hệt `yum/dnf`).*

| Hành động (Operation) | Hệ thống RPM (Red Hat/CentOS) | Hệ thống DEB (Debian/Ubuntu) |
|---|---|---|
| **Cài đặt file tải về (Offline)** | `rpm -i foo.rpm` | `dpkg --install foo.deb` |
| **Cài đặt từ kho (Online + Dependencies)** | `dnf install foo` | `apt-get install foo` |
| **Gỡ bỏ gói (Low-level)** | `rpm -e foo.rpm` | `dpkg --remove foo.deb` |
| **Gỡ bỏ gói + Phụ thuộc (High-level)** | `dnf remove foo` | `apt remove foo` |
| **Cập nhật một file gói mới (Offline)** | `rpm -U foo.rpm` | `dpkg --install foo.deb` |
| **Cập nhật gói từ kho (Online)** | `dnf update foo` | `apt install foo` |
| **Cập nhật toàn bộ hệ thống** | `dnf update` | `apt dist-upgrade` |
| **Liệt kê TẤT CẢ các gói đã cài** | `rpm -qa` hoặc `yum list installed` | `dpkg --list` |
| **Xem chi tiết & các file trong 1 gói đã cài** | `rpm -qil foo` | `dpkg --listfiles foo` |
| **Tìm kiếm gói có tên chứa "foo" (Online)** | `dnf list foo` | `apt-cache search foo` |
| **Xem toàn bộ danh sách gói (Online)** | `dnf list` | `apt-cache dumpavail foo` |
| **File này thuộc về gói phần mềm nào?** | `rpm -qf file` | `dpkg --search file` |

> 💡 **Chú ý:** Các hệ thống RHEL/CentOS cũ sử dụng lệnh `yum` thay vì `dnf`. Cú pháp cơ bản của hai lệnh này là hoàn toàn giống nhau. Tuy nhiên, nếu bạn gõ `yum` trên các hệ điều hành mới, nó có thể hiện cảnh báo và tự động chuyển hướng sang `dnf`.
# Sử dụng dnf quản lý gói phần mềm (Demo)

## Document Information
- **Doc ID:** 02-031
- **Summary:** Bài lab hướng dẫn thực hành cách sử dụng lệnh `dnf` (công cụ cấp cao trên Fedora/Red Hat/CentOS) để tìm kiếm, cài đặt và gỡ bỏ phần mềm (Ví dụ: `lbzip2`), đồng thời thấy được khả năng tự động giải quyết phụ thuộc (Dependency Resolution) xuất sắc của nó.
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

`dnf` (Dandified YUM) là công cụ quản lý gói dòng lệnh cấp cao (high-level utility) thế hệ mới, thay thế cho `yum` trên các hệ điều hành thuộc họ Red Hat (như Fedora, RHEL 8+, CentOS 8+). 

Khác với công cụ cấp thấp `rpm` chỉ biết xử lý từng file đơn lẻ, `dnf` biết cách giao tiếp với các kho lưu trữ trực tuyến (Repositories), tự động tải file và quan trọng nhất là **tự động giải quyết mọi rủi ro về phụ thuộc (Dependency Resolution)**.

Trong bài lab này, chúng ta sẽ thực hành trên một máy chủ Fedora (tương tự như RHEL/CentOS) để thấy sự thông minh của nó. Chúng ta sẽ làm việc với gói `lbzip2` (phiên bản chạy đa luồng - parallel - của công cụ nén `bzip2`, giúp nén nhanh hơn nhiều lần nhờ chia nhỏ công việc ra nhiều CPU Cores).

---

## 2. Tìm kiếm Gói Phần mềm (Search / List)

Để tìm xem có những gói phần mềm nào liên quan đến từ khóa `bzip2` trên kho lưu trữ, ta dùng lệnh `list`:
```bash
dnf list "*bzip2*"
# Hoặc lệnh tìm kiếm phổ biến hơn:
dnf search bzip2
```
*Kết quả sẽ hiển thị một danh sách dài, được chia làm 2 phần chính:*
- **Installed Packages:** Những gói đã có sẵn trên máy (như `bzip2`, `bzip2-devel`, `bzip2-libs`).
- **Available Packages:** Những gói có sẵn trên máy chủ của kho lưu trữ mạng nhưng chưa được cài, trong đó có gói `lbzip2` và `lbzip2-utils` mà chúng ta đang quan tâm.

---

## 3. Cài đặt Gói và Tự động hóa Phụ thuộc (Install)

Thay vì phải đi tải từng file `.rpm` như trước đây, ta chỉ cần ra một lệnh duy nhất.
*(Lưu ý: Mọi thao tác thay đổi hệ thống đều cần quyền `sudo`).*

```bash
sudo dnf install lbzip2-utils
```

**Sự thông minh của `dnf` thể hiện ở đây:**
Hệ thống sẽ tiến hành kiểm tra và nhận ra rằng gói `lbzip2-utils` không thể hoạt động một mình. Nó **bắt buộc phải có** gói lõi là `lbzip2`. 

Ngay lập tức, `dnf` sẽ hiển thị một bảng tóm tắt báo cáo:
- Sẽ cài đặt `lbzip2-utils`.
- **Cài đặt thêm (Installing for dependencies):** Gói `lbzip2`.
- Tổng dung lượng cần tải về.

Và nó dừng lại hỏi bạn: `Is this ok [y/N]:` (Bạn có đồng ý không?). 
Chỉ cần nhấn `y` và Enter, hệ thống sẽ tự động tải cả 2 gói về và cài đặt theo đúng thứ tự. 

Sau khi cài xong, bạn có thể kiểm tra xem lệnh đã có sẵn chưa:
```bash
which lbzip2
```

---

## 4. Gỡ bỏ Gói an toàn (Remove)

Giờ chúng ta thử dọn dẹp bằng cách xóa gói lõi `lbzip2` đi:

```bash
sudo dnf remove lbzip2
```

**Sự thông minh của `dnf` tiếp tục được thể hiện:**
Nó nhận ra rằng nếu xóa `lbzip2`, thì gói `lbzip2-utils` lúc nãy sẽ trở thành đồ vô dụng (unsatisfied dependency). Vì vậy, nó đề xuất **xóa luôn cả hai gói cùng lúc** để đảm bảo hệ thống luôn sạch sẽ và ổn định. Nó sẽ hiện bảng hỏi xác nhận, bạn chỉ việc nhấn `y`.

---

## 5. Tổng kết (Takeaway)

- Lệnh `dnf` (hay `yum`, `apt`) giải quyết hoàn toàn nỗi đau **Dependency Hell** mà các công cụ cấp thấp (`rpm`, `dpkg`) để lại.
- Dù các công cụ đồ họa (GUI) có thể dễ dùng, nhưng trên các máy chủ Server hiện đại (đặc biệt là Red Hat 8 hay CentOS 8 trở lên) thường không đi kèm công cụ quản lý gói bằng GUI nào đáng tin cậy. Do đó, việc thành thạo công cụ dòng lệnh cấp cao như `dnf` hay `apt` là **kỹ năng bắt buộc** đối với các System Administrators và Kỹ sư DevOps.
# Sử dụng rpm quản lý gói phần mềm (Demo)

## Document Information
- **Doc ID:** 02-029
- **Summary:** Bài lab hướng dẫn thực hành cách sử dụng lệnh `rpm` (công cụ cấp thấp) để truy vấn thông tin, liệt kê file và kiểm tra khả năng gỡ bỏ của một gói phần mềm (Ví dụ: `bzip2`), đồng thời nhấn mạnh khái niệm Dependency.
- **Version:** 0.0.1
- **Date:** 2026-05-13
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

`rpm` (Red Hat Package Manager) là một công cụ dòng lệnh cấp thấp (low-level utility) được sử dụng mặc định trên họ hệ điều hành Red Hat (RHEL, CentOS, Fedora). 

Trong bài Lab này, chúng ta sẽ không dùng `rpm` để cài đặt (vì dễ vướng lỗi Dependency Hell như đã học ở bài trước), mà sẽ sử dụng nó để **Truy vấn (Query)** và **Kiểm thử gỡ bỏ (Erase Test)** để hiểu sâu hơn cách hệ điều hành tổ chức các file phần mềm.

*Phần mềm được lấy làm ví dụ trong bài là `bzip2` (một công cụ nén dữ liệu rất phổ biến trên Linux).*

---

## 2. Truy vấn Thông tin Gói (Querying Packages)

Cú pháp lệnh `rpm` luôn bắt đầu bằng các cờ chức năng chính. Chữ **`q`** đại diện cho **Query** (Truy vấn).

### 2.1. Liệt kê tất cả các gói đang cài trên hệ thống
```bash
rpm -qa
```
- `-a` (all): Hiển thị tất cả. Lệnh này sẽ in ra hàng ngàn dòng. 
- Để tìm đúng gói mình cần, ta kết hợp (pipe) với lệnh `grep`:
  ```bash
  rpm -qa | grep bzip2
  ```
  *(Kết quả thường sẽ trả về hai gói: `bzip2` là chương trình nén, và `bzip2-libs` là các thư viện nền tảng mà chương trình đó cần dùng).*

### 2.2. Xem thông tin chi tiết (Information) của một gói
```bash
rpm -qi bzip2
```
- `-i` (information): Hiển thị thông tin mô tả.
- Kết quả sẽ cho bạn biết: Tên gói, Phiên bản (Version 1.0.x), Ngày phát hành, Kiến trúc (Architecture), Website (URL), và một đoạn văn bản mô tả chức năng của phần mềm (Ví dụ: "high quality data compressor").

### 2.3. Liệt kê các file nằm bên trong gói (List Files)
Khi bạn cài một phần mềm, hệ thống sẽ rải các file của nó đi khắp nơi (vào `/usr/bin`, `/usr/share/man`...). Để biết chính xác gói đó đã thả những file nào vào hệ thống:
```bash
rpm -ql bzip2
```
- `-l` (list files).

Nếu danh sách quá dài, bạn nên kết hợp với `less` để xem từng trang:
```bash
rpm -ql bzip2 | less
```

### Mẹo nâng cao: Xem chi tiết các file đó
Nếu bạn muốn dùng lệnh `ls -l` để xem kích thước, quyền hạn của chính những file mà lệnh trên liệt kê ra, bạn có thể gộp lệnh lại như sau (Sử dụng command substitution `$()`):
```bash
ls -lF $(rpm -ql bzip2) | less
```
> **Phát hiện thú vị:** Nếu nhìn vào kết quả, bạn sẽ thấy file lệnh nén `bzip2` và lệnh giải nén `bunzip2` thực ra chỉ là chung một file vật lý (liên kết Symbolic link với nhau). Hệ thống đủ thông minh để biết lúc nào thì nén, lúc nào thì giải nén dựa vào tên lệnh bạn gõ.

---

## 3. Kiểm thử Gỡ bỏ Gói (Erase Test) và Phân tích Dependency

Tiếp tục bài Lab, chúng ta sẽ thử dùng lệnh xóa gói `bzip2`. 
- Cờ **`-e`** (erase) dùng để xóa. (Yêu cầu quyền `sudo`).
- Cờ **`--test`** cực kỳ quan trọng: Nó ra lệnh cho `rpm` **chỉ chạy thử mô phỏng (dry run)** để xem có lỗi gì xảy ra không, chứ tuyệt đối không xóa thật.

### Chạy thử mô phỏng xóa gói
```bash
sudo rpm -e --test bzip2
```

**Kết quả:** Hệ thống sẽ "gào thét" (báo lỗi Failed) và từ chối việc xóa này! 
Lý do? Lệnh báo rằng có hàng loạt các phần mềm khác (ví dụ như `file-roller` - trình quản lý nén file giao diện đồ họa) đang **cần** `bzip2` để hoạt động. Nếu bạn xóa `bzip2`, những phần mềm kia sẽ bị hỏng theo.

### Tra cứu nguồn gốc và phụ thuộc (What provides / What requires)

Lệnh `rpm` cung cấp 2 công cụ trinh thám cực mạnh để tìm ra quan hệ phụ thuộc:

**1. "Gói phần mềm nào đã cung cấp cái lệnh/file này?"**
Ví dụ bạn thấy có lệnh tên `bzip2` trên hệ thống và tự hỏi nó từ đâu ra:
```bash
rpm -q --whatprovides bzip2
```

**2. "Những gói phần mềm nào đang sống bám (phụ thuộc) vào gói này?"**
Để xác nhận lại danh sách các phần mềm bị ảnh hưởng nếu ta xóa `bzip2`:
```bash
rpm -q --whatrequires bzip2
```
*Kết quả sẽ liệt kê danh sách các phần mềm (`file-roller`, `dpkg`...) cần `bzip2` để chạy.*

---

## 4. Tổng kết (Takeaway)

Qua bài thực hành này, ta rút ra 2 điều:
1. `rpm` là một công cụ cực tốt để **truy vấn, điều tra và khám phá** xem các phần mềm cài cắm gì vào hệ thống.
2. `rpm` **quá yếu kém** để dùng cho việc thao tác (cài đặt/gỡ bỏ) vì nó bắt bạn phải tự tay xử lý các rủi ro phụ thuộc (Dependency Hell). 

Để cài đặt và gỡ bỏ phần mềm một cách an toàn và tự động xử lý rủi ro này, chúng ta sẽ phải dùng tới các công cụ Cấp cao (High-level tools) như `yum`, `dnf` hoặc `apt` ở các bài học ngay tiếp theo.
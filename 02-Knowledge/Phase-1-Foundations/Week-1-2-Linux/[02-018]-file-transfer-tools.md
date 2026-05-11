# Công cụ Truyền tải File (File Transfer Tools)

## Document Information
- **Doc ID:** 02-018
- **Summary:** Tổng hợp và phân loại các công cụ truyền tải file trong Linux, bao gồm các công cụ cũ (FTP), công cụ tải dữ liệu (curl, wget) và các công cụ bảo mật hiện đại (scp, sftp, rsync).
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về công cụ truyền tải file?**
Dù sớm hay muộn, bạn sẽ cần phải chuyển file giữa các máy tính trong mạng nội bộ (Local Network) hoặc đẩy dữ liệu từ server ra Internet (và ngược lại). Ví dụ: tải mã nguồn từ GitHub, kéo một file backup database từ máy chủ này sang máy chủ khác, hay tải một script cài đặt từ web. Việc chọn đúng công cụ không chỉ giúp tối ưu hóa tốc độ truyền tải mà còn đảm bảo tính **bảo mật** cực kỳ quan trọng (tránh việc dữ liệu nhạy cảm bị đánh cắp trên đường truyền).

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Local Linux VM hoặc AWS EC2 instance.
- **Yêu cầu:** Các công cụ cơ bản như `curl`, `wget`, `scp` thường được cài đặt mặc định trên mọi bản phân phối Linux. Các lệnh trong bài có thể thực thi bằng user thường.

## 3. Phân loại Các Công cụ Truyền tải File

### 3.1. Giao thức FTP (File Transfer Protocol)
- **Lịch sử:** FTP là giao thức truyền tải file lâu đời nhất, đã tồn tại từ những ngày đầu của Internet.
- **Cách dùng:** Có thể dùng lệnh `ftp` qua terminal, dùng các công cụ nâng cao như `lftp`, `ncftp`, hoặc dùng giao diện đồ họa như **FileZilla**. Thậm chí các trình duyệt web cũng tích hợp sẵn khả năng download qua FTP.
- **Server:** `vsftpd` là FTP server phổ biến nhất trên Linux, dễ cấu hình và mạnh mẽ.
- **Lưu ý bảo mật (CỰC KỲ QUAN TRỌNG):** FTP gửi dữ liệu dưới dạng **văn bản thô (clear text)**, kể cả mật khẩu đăng nhập. Hiện nay, FTP bị coi là **không an toàn (deprecated/insecure)**. Nhiều dự án lớn như Linux Kernel đã ngừng cho phép tải mã nguồn qua FTP. Trừ khi bạn bắt buộc phải dùng cho hệ thống cũ, hãy tránh sử dụng FTP thuần túy.

### 3.2. Công cụ Đa năng: `curl` và `wget`
Đây là bộ đôi công cụ đa năng dùng để tải dữ liệu từ web hoặc API.
- **Hỗ trợ giao thức:** Rất nhiều giao thức như HTTP, HTTPS, FTP...
- **`wget`:** Chuyên dùng để **tải file**. Nó hỗ trợ tải tiếp khi bị đứt mạng (resume) và có thể tải toàn bộ một trang web (recursive download).
- **`curl`:** Linh hoạt hơn, thường được dùng để gửi request tới API (REST API), truyền tải header, cookie, và đọc trực tiếp dữ liệu trả về trên terminal mà không cần lưu thành file.

### 3.3. Các Công cụ Bảo mật (Dựa trên SSH)
Đây là tiêu chuẩn vàng (Best Practice) cho DevOps hiện đại. Vì dựa trên OpenSSH, toàn bộ dữ liệu truyền tải đều được **mã hóa (encrypted)**, chống lại việc bị nghe lén (sniffing).
- **`scp` (Secure Copy):** Dùng để copy file giữa 2 máy chủ giống như lệnh `cp` thông thường, nhưng thực hiện qua mạng. Rất dễ dùng cho các file lẻ.
- **`sftp` (Secure FTP):** Hoạt động giống như FTP nhưng dữ liệu được mã hóa an toàn. Đây là sự thay thế hoàn hảo cho FTP.

### 3.4. Công cụ Đồng bộ hóa: `rsync`
- `rsync` là một công cụ cực kỳ mạnh mẽ dùng để **chuyển và đồng bộ hóa (synchronization)** file giữa các máy tính.
- Nó cũng chạy trên nền SSH (mã hóa an toàn).
- **Sức mạnh cốt lõi:** Nó sử dụng thuật toán thông minh để **chỉ truyền đi phần dữ liệu bị thay đổi** (delta transfer) thay vì copy lại toàn bộ file. Việc này giúp tiết kiệm băng thông và tăng tốc độ đồng bộ lên gấp hàng trăm lần đối với các thư mục lớn.

## 4. Cảnh báo Bảo mật (Tuyệt đối không sử dụng)
Bạn có thể thấy một số công cụ cũ kĩ như **`rsh`** (Remote Shell) hay **`rcp`** (Remote Copy) vẫn còn tồn tại trên một số bản phân phối. **TUYỆT ĐỐI KHÔNG SỬ DỤNG CHÚNG**. Chúng không có bất kỳ cơ chế mã hóa nào và cực kỳ thiếu an toàn. Hãy luôn dùng `ssh` và `scp` để thay thế.

---

## 5. Thực hành (Hands-on Lab)

**Mục tiêu:** Thử nghiệm việc tải một file từ Internet và chuyển một file giữa máy cá nhân và Server bằng công cụ bảo mật.

### Bước 1: Dùng `wget` hoặc `curl` để tải file
Giả sử bạn muốn tải một file text mẫu từ Internet:

```bash
# Dùng wget (lưu trực tiếp thành file)
wget https://raw.githubusercontent.com/torvalds/linux/master/README

# Hoặc dùng curl (phải dùng tham số -o để lưu thành file, nếu không nó sẽ in ra màn hình)
curl -o README.txt https://raw.githubusercontent.com/torvalds/linux/master/README
```
*Lệnh trên sẽ tải file README của dự án Linux Kernel từ GitHub về máy chủ của bạn.*

### Bước 2: Dùng `scp` để truyền tải file an toàn
*Lưu ý: Lệnh này bạn cần mở một tab Terminal mới trên máy tính cá nhân (Mac/Windows), KHÔNG gõ trong SSH session của EC2.*

Chúng ta sẽ copy file `aws-lab-key.pem` từ máy cá nhân lên thư mục `/home/ubuntu` của máy chủ AWS EC2.

```bash
# Cú pháp: scp [Tùy_chọn] [Đường_dẫn_File_Nguồn] [User@IP_Đích]:[Đường_dẫn_Đích]

# Thay địa chỉ IP_CỦA_EC2 bằng IP Public thật của bạn
scp -i '/Users/tung/OnlyTun/DevOps/05-Document/key/tungqt-macbook.pem' \
    '/Users/tung/OnlyTun/DevOps/05-Document/key/aws-lab-key.pem' \
    ubuntu@<IP_CỦA_EC2>:/home/ubuntu/
```
*Lệnh này dùng giao thức mã hóa SSH (chứng thực bằng file `.pem`) để copy file an toàn lên máy chủ. Bạn có thể SSH vào EC2 và gõ `ls` để kiểm tra file đã xuất hiện chưa.*
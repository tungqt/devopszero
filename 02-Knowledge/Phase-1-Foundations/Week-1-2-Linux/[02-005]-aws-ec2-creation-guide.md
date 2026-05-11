# Hướng dẫn tạo và quản lý AWS EC2 Instance

## Document Information
- **Doc ID:** 02-005
- **Summary:** Hướng dẫn chi tiết từng bước tạo một máy chủ ảo (EC2) trên AWS, cách kết nối SSH, quản lý vòng đời (Stop/Terminate) và lưu ý về chi phí.
- **Version:** 0.0.1
- **Date:** 2026-05-05
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

### 1.1. EC2 là gì?
**Amazon EC2 (Elastic Compute Cloud)** là dịch vụ cốt lõi nhất của AWS. Nó cho phép bạn thuê các máy chủ ảo (virtual servers) trên đám mây. Bạn có toàn quyền quản trị (root access) trên các máy chủ này giống hệt như một máy tính vật lý đặt trước mặt bạn.

### 1.2. Tại sao DevOps phải thành thạo EC2?
- **Nền tảng của mọi thứ:** Dù hiện nay có Docker, Kubernetes, Serverless... nhưng bản chất bên dưới chúng vẫn là các máy chủ EC2 đang chạy.
- **Môi trường thực hành chuẩn:** EC2 cung cấp môi trường Linux chuẩn xác nhất để thực hành các bài Lab (như phân vùng ổ đĩa, cài đặt web server, setup CI/CD runner).
- **Kỹ năng Troubleshooting:** Khi hệ thống sập, kỹ năng SSH vào EC2, đọc log, kiểm tra RAM/CPU là kỹ năng sinh tồn của DevOps.

### 1.3. Môi trường thực hành (Environment Setup)
- **Tài khoản AWS:** Đã hoàn thành setup bảo vệ chi phí (Billing Protection) theo tài liệu `[02-004]`.
- **Region (Khu vực):** Khuyến nghị chọn `us-east-1` (N. Virginia) hoặc `ap-southeast-1` (Singapore) vì có hỗ trợ Free Tier đầy đủ.
- **Công cụ:** Trình duyệt web (để thao tác trên AWS Console) và Terminal (để SSH).

---

## 2. Hướng dẫn tạo EC2 Instance (Click-by-click)

Đây là quy trình chuẩn để tạo một server Linux an toàn và miễn phí (nằm trong Free Tier).

### Bước 2.1: Truy cập giao diện tạo EC2
1. Đăng nhập vào [AWS Management Console](https://console.aws.amazon.com/).
2. Đảm bảo bạn đang chọn đúng Region ở góc trên cùng bên phải (ví dụ: N. Virginia).
3. Gõ `EC2` vào thanh tìm kiếm trên cùng và chọn dịch vụ **EC2**.
4. Click vào nút màu cam **Launch instance** (Khởi chạy phiên bản).

### Bước 2.2: Cấu hình chi tiết (Launch an instance)

Điền theo đúng các thông số sau:

#### 1. Name and tags
- **Name:** Gõ tên gợi nhớ, ví dụ: `devops-linux-lab`. (Tên này giúp bạn dễ tìm và xóa máy sau khi học xong).

#### 2. Application and OS Images (Amazon Machine Image - AMI)
- Chọn tab **Ubuntu**.
- Ở dropdown *Amazon Machine Image (AMI)*, chọn **Ubuntu 24.04 LTS** hoặc **Ubuntu 26.04 LTS** (bản mới nhất).
- ⚠️ **QUAN TRỌNG:** Đảm bảo bên dưới tên hệ điều hành có dòng chữ màu xanh lá: **Free tier eligible**.

#### 3. Instance type (Cấu hình phần cứng)
- Chọn **t2.micro** (hoặc **t3.micro** tùy region). Đây là cấu hình có 1 vCPU và 1GB RAM.
- ⚠️ **QUAN TRỌNG:** Đảm bảo có chữ **Free tier eligible** bên cạnh. Chọn sai loại (ví dụ t3.medium) sẽ bị tính tiền ngay lập tức.

#### 4. Key pair (login) - Chìa khóa vào nhà
Đây là bước bắt buộc để bạn có thể SSH vào server.
- Click **Create new key pair**.
- **Key pair name:** Gõ `aws-lab-key`.
- **Key pair type:** `RSA`.
- **Private key file format:** `.pem` (dành cho Mac/Linux/Windows 10+).
- Click **Create key pair**.
- Trình duyệt sẽ tải file `aws-lab-key.pem` về máy (thường vào thư mục `Downloads`). **Lưu trữ file này cẩn thận, mất nó là mất quyền truy cập server.**

#### 5. Network settings (Tường lửa - Security Group)
Click nút **Edit** (ở góc phải) để mở rộng cấu hình chi tiết:
- **VPC & Subnet:** Để mặc định.
- **Auto-assign public IP:** **Enable** (Bắt buộc phải bật để có IP kết nối từ Internet).
- **Firewall (security groups):** Chọn **Create security group**.
  - **Security group name:** Gõ `linux-lab-sg`.
  - Cuộn xuống phần **Inbound security groups rules**:
    - AWS đã tạo sẵn 1 rule cho SSH (Port 22).
    - Ở cột **Source type**, bấm dropdown chọn **`My IP`**.
    - *(AWS sẽ tự điền IP mạng nhà bạn vào. Điều này cực kỳ quan trọng để chặn hacker dò mật khẩu từ các IP khác).*

#### 6. Configure storage (Ổ cứng - EBS)
- Mặc định AWS cấp 1 ổ đĩa Root: `8 GiB` | `gp3` (hoặc gp2). Cứ để nguyên.
- *(Nếu bài Lab yêu cầu thêm ổ đĩa trống, ví dụ Lab phân vùng, bạn mới click **Add new volume** và thêm ổ 1GB).*
- Tổng dung lượng dưới 30GB là an toàn trong Free Tier.

#### 7. Launch
- Nhìn sang cột **Summary** bên phải kiểm tra lại (đặc biệt là phần Free tier).
- Click nút cam **Launch instance**.
- Đợi thông báo Success, click nút **View all instances** ở góc dưới.

---

## 3. Kết nối vào EC2 (SSH)

### Bước 3.1: Lấy địa chỉ IP
1. Ở màn hình Instances, đợi trạng thái (Instance state) chuyển sang màu xanh lá **`Running`**.
2. Click vào tên instance.
3. Nhìn xuống tab **Details**, tìm mục **Public IPv4 address** và copy địa chỉ IP (ví dụ: `18.138.236.20`).

### Bước 3.2: Thực hiện kết nối từ Terminal (Mac/Linux)
1. Mở Terminal.
2. Di chuyển file key `.pem` vào một thư mục an toàn (ví dụ `~/Keys/`) hoặc để nguyên ở `Downloads`.
3. **Cấp quyền bảo mật cho file key (Bắt buộc làm 1 lần đầu tiên):**
   ```bash
   chmod 400 ~/Downloads/aws-lab-key.pem
   ```
   *(Nếu không làm bước này, SSH sẽ báo lỗi "bad permissions" và từ chối kết nối).*
4. **Gõ lệnh SSH kết nối:**
   ```bash
   ssh -i ~/Downloads/aws-lab-key.pem ubuntu@<ĐỊA_CHỈ_IP_VỪA_COPY>
   ```
5. Ở lần kết nối đầu tiên, Terminal sẽ hỏi: `Are you sure you want to continue connecting (yes/no/[fingerprint])?`
   👉 Gõ chữ **`yes`** và nhấn Enter.

✅ **Thành công:** Dấu nhắc lệnh của bạn sẽ đổi thành `ubuntu@ip-172-31-x-x:~$`. Bạn đang đứng bên trong server AWS!

---

## 4. Quản lý Vòng đời (Lifecycle) và Chi phí

Đây là phần quan trọng để không bị mất tiền oan. Trên giao diện Instances, khi bạn chọn 1 máy và click nút **Instance state**, sẽ có các tùy chọn sau:

### 4.1. Stop instance (Tạm dừng)
- **Tác dụng:** Tắt máy giống như bạn Shutdown máy tính ở nhà.
- **Về dữ liệu:** Mọi dữ liệu trên ổ cứng (EBS) **được giữ nguyên**.
- **Về IP Public:** Khi bật lại (Start), **địa chỉ IP Public sẽ bị đổi thành số mới**. Bạn phải copy lại IP mới để SSH.
- **Về chi phí:**
  - 🛑 **Ngừng tính tiền giờ chạy máy (Compute).**
  - ⚠️ **VẪN TÍNH TIỀN ổ cứng (EBS).** (Nhưng nếu tổng < 30GB thì vẫn free).
  - ⚠️ **VẪN TÍNH TIỀN IP Public.** (Từ 02/2024, AWS thu phí ~$3.6/tháng cho IP Public dù máy tắt hay bật).
- **Khi nào dùng:** Khi bạn đang làm dở bài Lab, muốn đi ngủ và ngày mai làm tiếp.

### 4.2. Terminate instance (Xóa vĩnh viễn)
- **Tác dụng:** Tiêu hủy hoàn toàn máy chủ. **Không thể khôi phục.**
- **Về dữ liệu:** Ổ cứng Root (8GB) mặc định sẽ **bị xóa sạch** cùng với máy. *(Lưu ý: Nếu bạn có gắn thêm ổ đĩa phụ, ổ phụ đó KHÔNG tự xóa, bạn phải vào mục Volumes xóa bằng tay).*
- **Về chi phí:**
  - ✅ **Ngừng tính mọi loại tiền** (Compute, IP Public).
- **Khi nào dùng:** Khi đã học xong bài Lab và không cần dùng máy đó nữa. **Nên tạo thói quen Terminate ngay sau khi học xong.**

### 4.3. Reboot instance (Khởi động lại)
- Tương đương lệnh `sudo reboot` bên trong Linux. IP Public không bị đổi.

---

## 5. Xử lý lỗi thường gặp (Troubleshooting)

| Lỗi / Hiện tượng | Nguyên nhân | Cách khắc phục |
|---|---|---|
| Lệnh SSH cứ treo (đơ) không hiện gì, sau đó báo `Connection timed out` | Sai cấu hình Security Group (Tường lửa) | Vào AWS Console → Security Groups → Sửa Inbound rule SSH (port 22) thành `My IP` (vì IP nhà bạn có thể đã thay đổi do reset modem). |
| `Permission denied (publickey)` | Sai đường dẫn file key `.pem`, hoặc sai tên user | Kiểm tra lại đường dẫn file key. Đảm bảo user là `ubuntu` (không phải `root` hay `ec2-user`). |
| `WARNING: UNPROTECTED PRIVATE KEY FILE!` | File key `.pem` đang mở quyền quá rộng (ai cũng đọc được) | Chạy lệnh: `chmod 400 /đường/dẫn/tới/file.pem` |
| `No such file or directory` | Gõ sai đường dẫn tới file `.pem` trong lệnh SSH | Kéo thả file `.pem` từ Finder vào Terminal để nó tự sinh ra đường dẫn chuẩn xác nhất. |

---

## 6. Bài tập thực hành (Lab 5)

1. Khởi tạo 1 EC2 instance Ubuntu 24.04/26.04 t2.micro ở region us-east-1.
2. Cấu hình Security Group chỉ cho phép SSH từ `My IP`.
3. Tải file key `.pem` và thực hiện SSH thành công vào server từ máy Mac.
4. Chạy lệnh `uptime` và `free -m` bên trong server để kiểm tra thời gian chạy và RAM.
5. Gõ `exit` để thoát khỏi server.
6. Lên AWS Console thực hiện **Terminate instance** để dọn dẹp. Đảm bảo trạng thái chuyển sang `Terminated`.

# AWS Account Setup & Billing Protection cho Người Mới

## Document Information
- **Doc ID:** 02-004
- **Summary:** Hướng dẫn toàn diện cách setup AWS account an toàn (MFA, Billing Alerts, Budgets, Cost Anomaly Detection) trước khi thực hành các Lab DevOps. Cập nhật theo UI AWS Console mới nhất 2026.
- **Version:** 0.0.1
- **Date:** 2026-05-03
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

### 1.1. Tại sao DevOps phải biết "khoá" Billing AWS trước khi thực hành?
Đây là **bài học bắt buộc** trước khi bạn chạm vào bất kỳ dịch vụ AWS nào. Lý do:

- **Câu chuyện thực tế ám ảnh cộng đồng:** Có người mới học AWS đã bị **bill $30,000+ trong 1 đêm** vì để lộ Access Key trên GitHub → bị hacker dùng để đào coin. Có người bị **bill $1,000+** chỉ vì quên xoá 1 RDS instance đã chạy 1 tháng. Đây không phải chuyện hiếm — search Google cụm "AWS bill shock" sẽ thấy hàng nghìn câu chuyện.
- **AWS không tự động ngắt khi vượt Free Tier:** Khác với gói cước điện thoại, AWS **KHÔNG TỰ TẮT** dịch vụ khi bạn vượt giới hạn miễn phí. Họ cứ tính tiền tiếp và gửi bill cuối tháng.
- **Hậu quả thực tế:** Nếu không setup chặn billing trước, một lỗi nhỏ (quên tắt EC2, để lộ key, click nhầm tạo RDS Aurora) có thể tốn hàng nghìn đô — và việc xin AWS hoàn tiền (refund) **phụ thuộc thiện chí của AWS**, không phải quyền của bạn.
- **Kỹ năng nền tảng cho DevOps thật:** Trong công việc, **mỗi DevOps Engineer đều phải biết FinOps** — tối ưu chi phí cloud. Không có kỹ năng này thì chỉ là "click-ops" chứ không phải DevOps thực thụ.

> 📌 **Triết lý cốt lõi:** Trong cloud, **mỗi giây trôi qua đều có thể tính tiền**. Hãy xây dựng "lá chắn nhiều lớp" trước khi bắt tay vào học.

### 1.2. Môi trường thực hành (Environment Setup)
Tài liệu này KHÔNG chạy lệnh CLI nào. Toàn bộ thao tác diễn ra trên **AWS Management Console** (giao diện Web).

**Yêu cầu trước khi bắt đầu:**
- ✅ Đã đăng ký tài khoản AWS (cần email + thẻ Visa/Master để verify).
- ✅ Đã đăng nhập được vào [AWS Console](https://console.aws.amazon.com/) bằng email Root.
- ✅ Có 1 email cá nhân **kiểm tra hàng ngày** để nhận alert.
- ✅ Có điện thoại smartphone để cài app **Authenticator** (Google Authenticator hoặc Microsoft Authenticator).

**Khu vực (Region) khuyến nghị:**
- ✅ **us-east-1 (N. Virginia)**: Rẻ nhất, đầy đủ Free Tier, **bắt buộc** cho CloudWatch Billing Metrics.
- ✅ **ap-southeast-1 (Singapore)**: Gần Việt Nam (latency thấp), có Free Tier.

> ⚠️ **Lưu ý:** Một số region (Hong Kong, Bahrain, Cape Town) **không có Free Tier** — tránh tạo resource ở đó.

---

## 2. Hiểu đúng về AWS Free Tier (2026)

### 2.1. 3 loại Free Tier của AWS
AWS có **3 loại** miễn phí khác nhau, **không phải tất cả đều miễn phí mãi**:

| Loại | Thời hạn | Ví dụ |
|---|---|---|
| **12-Months Free** | 12 tháng kể từ ngày đăng ký | EC2 t2.micro, EBS 30GB, S3 5GB |
| **Always Free** | Vĩnh viễn | Lambda 1M requests/tháng, DynamoDB 25GB |
| **Trials** | Thường 30-60 ngày | Amazon Inspector, GuardDuty |

### 2.2. Free Tier cho Lab cơ bản (EC2 + EBS)

| Dịch vụ | Quota Free | Giới hạn cần biết |
|---|---|---|
| **EC2 t2.micro / t3.micro** | **750 giờ/tháng** | = 31 ngày × 24h. Đủ chạy **1 instance liên tục cả tháng**. **Chạy 2 instance = vượt quota → tính tiền $0.0116/giờ.** |
| **EBS gp2/gp3 Storage** | **30 GB/tháng** | Lab cơ bản dùng ~9GB → an toàn |
| **EBS Snapshots** | **1 GB/tháng** | Đừng tạo snapshot không cần |
| **Data Transfer Out (Internet)** | **100 GB/tháng** (toàn AWS) | Lab text-only gần như không transfer |
| **Public IPv4** | ⚠️ **TÍNH TIỀN từ 01/02/2024** | $0.005/giờ ≈ **$3.6/tháng/IP** kể cả Free Tier |
| **Elastic IP (chưa attach)** | ⚠️ Tính tiền | $0.005/giờ — luôn release sau khi xong |

> 🚨 **CẢNH BÁO QUAN TRỌNG (mới):** Từ **01/02/2024**, AWS bắt đầu tính tiền **mọi Public IPv4** (kể cả gắn vào EC2 Free Tier). Đây là khoản phí **duy nhất** bạn có thể bị charge khi làm Lab Linux Partition cơ bản — khoảng **$3.6/tháng nếu để instance chạy 24/7**, hoặc gần **$0** nếu tắt instance sau mỗi buổi học.

### 2.3. Worst Case Cost Analysis cho Lab `[02-003]`
Nếu bạn **lỡ quên không tắt** EC2 sau **1 tuần** (us-east-1):

| Tài nguyên | Cost | Ghi chú |
|---|---|---|
| t2.micro 24/7 × 7 ngày | **$0** | Trong 750h/tháng Free Tier |
| EBS 9GB × 7 ngày | **$0** | Trong 30GB Free Tier |
| Public IPv4 24/7 × 7 ngày | **~$0.84** | 168h × $0.005 |
| **Tổng worst case** | **~$1** | Budget alert sẽ trigger trước khi hết tiền |

→ Kể cả tệ nhất, bạn chỉ mất **~$1**. Không sợ "cháy thẻ" hàng nghìn đô như tin đồn — **NẾU** đã setup đầy đủ Mục 3 dưới đây.

---

## 3. Setup Billing Protection - "Lá chắn 8 lớp" (KHÔNG được bỏ qua)

Đây là phần **quan trọng nhất** của tài liệu. Làm theo đúng thứ tự, không skip bước nào.

### 3.1. Lớp 1 — Bật MFA cho Root Account

**Vì sao quan trọng nhất?** Tài khoản Root bị hack → kẻ tấn công có thể tạo 100 EC2 đào coin → bill $10,000+ qua đêm.

**Thao tác:**
1. Đăng nhập [AWS Console](https://console.aws.amazon.com/) bằng email root.
2. Click **avatar góc phải** → **Security credentials**.
3. Mục **Multi-factor authentication (MFA)** → **Assign MFA device**.
4. Chọn **Authenticator app** → quét QR code bằng app Authenticator trên điện thoại.
5. Nhập **2 mã liên tiếp** (mã hiện tại + mã mới sau 30s) → xác nhận.

✅ **Verify:** Vào lại Security credentials, mục MFA hiện status `Active`.

### 3.2. Lớp 2-4 — Billing Preferences (UI mới 2024+)

> 📌 **Lưu ý UI mới:** AWS đã redesign trang Billing Preferences thành **2 sections riêng biệt**, mỗi section phải click **Edit** riêng (khác với UI cũ "1 trang 3 checkbox").

**Truy cập:** [Billing Preferences](https://console.aws.amazon.com/billing/home#/preferences)

#### Section 1: Invoice delivery preferences
1. Click nút **Edit** bên cạnh **Invoice delivery preferences**.
2. Tick ☑️ **PDF invoices delivery by email**.
3. Click **Update**.
4. ✅ Verify: Trạng thái đổi từ `Deactivated` → `Activated`.

#### Section 2: Alert preferences
1. Click nút **Edit** bên cạnh **Alert preferences**.
2. Tick ☑️ **AWS Free Tier alerts** → nhập **email cá nhân** vào ô bên dưới.
3. Tick ☑️ **CloudWatch billing alerts**.
4. Click **Update**.
5. ✅ Verify: 2 dòng đổi từ `Not delivered` → `Delivered to your-email@gmail.com`.

> 💡 **Vai trò từng alert:**
> - **AWS Free Tier alerts:** Email khi bạn dùng đến **85% quota Free Tier** của bất kỳ dịch vụ nào.
> - **CloudWatch billing alerts:** Cho phép tạo CloudWatch Alarm dựa trên billing metric. **Bắt buộc bật** thì Budget Alerts ở Lớp 5-6 mới hoạt động đúng.

> ⚠️ **Cạm bẫy region:** Nếu thấy thông báo *"To receive billing alerts, you must be in the US East (N. Virginia) region"* → switch sang **us-east-1** ở góc trên bên phải Console TRƯỚC khi setup.

### 3.3. Lớp 5 — Zero Spend Budget (Template-based, UI mới)

**Truy cập:** [AWS Budgets](https://console.aws.amazon.com/billing/home#/budgets) → click **Create a budget**.

**Thao tác:**
1. **Budget setup**: chọn ✅ **Use a template (simplified)**.
2. **Templates**: chọn ✅ **Zero spend budget**.
3. **Budget name**: giữ `My Zero-Spend Budget` (hoặc đổi `Zero-Spend-Alert`).
4. **Email recipients**: nhập **email cá nhân** (BẮT BUỘC, nếu trống sẽ báo đỏ "Enter a valid email address").
5. Click **Create budget**.

**Đặc điểm Zero Spend Budget:**
- Cảnh báo khi spend **vượt $0.01** (chạm đồng đầu tiên).
- Check **1 lần/ngày** (delay 8-24h sau khi phát sinh charge).
- **Không tự stop EC2** — chỉ gửi email.

### 3.4. Lớp 6 — Monthly $1 Budget (Forecast-based)

**Thao tác:**
1. Quay lại trang Budgets → **Create a budget** lần nữa.
2. Vẫn chọn **Use a template (simplified)**.
3. **Templates**: chọn ✅ **Monthly cost budget**.
4. Điền:

| Field | Giá trị |
|---|---|
| Budget name | `My-Monthly-1USD-Budget` |
| Enter your budgeted amount ($) | `1.00` |
| Email recipients | Email cá nhân |

5. Click **Create budget**.

**Đặc điểm Monthly Cost Budget:**
- Tự động tạo sẵn **2 alerts** (80% Actual và 100% Forecasted).
- **Forecasted alert** dùng AI dự đoán tháng này sẽ vượt $1 → cảnh báo sớm hơn cả Actual.

### 3.5. Lớp 7-8 — Cost Anomaly Detection (Default + Strict)

**Truy cập:** [Cost Anomaly Detection](https://console.aws.amazon.com/cost-management/home#/anomaly-detection)

#### Lớp 7: Default Monitor (đã có sẵn)

> 💡 Từ 2023+, AWS **tự động tạo sẵn** 1 monitor `Default-Services-Monitor` cho mọi account mới. Bạn **KHÔNG cần tạo monitor mới** (và cũng không tạo được — vì giới hạn 1 monitor "AWS Services"/account).

**Verify:** Vào tab **Cost monitors** → phải thấy 1 dòng:
```text
Default-Services-Monitor | AWS services | Managed by AWS | Active
```

#### Lớp 8: Tạo Strict Subscription mới ($1, Daily summaries)

**Vì sao tạo mới (không edit Default-Services-Subscription)?**
- AWS tạo sẵn 1 subscription mặc định với threshold **$100 AND 40%** — **QUÁ CAO** cho account học tập (charge $99 vẫn không alert).
- Subscription mặc định **bị lock không cho edit** (AWS-managed).
- Tạo subscription mới giúp có **2 lớp song song**: strict ($1) bắt anomaly nhỏ + default ($100) bắt anomaly lớn.

**Thao tác:**
1. Trang Anomaly Detection → tab **Alert subscriptions** → **Create subscription**.
2. Điền:

| Field | Giá trị | Giải thích |
|---|---|---|
| **Subscription name** | `My-Strict-1USD-Alert` | Tên gọi |
| **Linked monitors** | ☑️ `Default-Services-Monitor` | Bắt buộc tick |
| **Threshold type** | **Dollar amount** (KHÔNG chọn "Dollar amount AND percentage") | Chỉ 1 điều kiện cho dễ trigger |
| **Dollar amount** | `1` | Cảnh báo từ $1 |
| **Alerting frequency** | ⚠️ **Daily summaries** (xem lưu ý) | Email trực tiếp |
| **Email recipients** | Email cá nhân | Có thể nhập nhiều, cách bằng dấu phẩy |

3. Click **Subscribe**.

> ⚠️ **Lưu ý UI mới (quan trọng):** AWS đã **bỏ tùy chọn email trực tiếp** cho `Individual alerts` — giờ Individual chỉ hỗ trợ **SNS Topic ARN**. Có 2 đường:
> - **Đơn giản (khuyến nghị):** Chọn `Daily summaries` → dùng email trực tiếp (Lab này đủ dùng).
> - **Nâng cao:** Tạo SNS Topic riêng, subscribe email vào topic, dán ARN vào → giữ Individual alerts (xem Mục 8 cuối tài liệu).

### 3.6. Lớp 9 (Optional) — Budget Action: Auto-stop EC2

Nếu muốn "ngắt cầu dao" tự động khi vượt budget:

1. Trong AWS Budgets, edit `My-Monthly-1USD-Budget` → **Add an action**.
2. Action type: **Stop EC2 instances**.
3. Trigger: **Actual > 100% ($1.00)**.
4. Chọn EC2 instance → Approval: **Automatic**.

> 📌 Action này tự tạo IAM Role, an toàn. Vẫn miễn phí.

### 3.7. Verify Checklist - "Lá chắn 8 lớp"

Sau khi xong, verify từng lớp:

| # | Lớp bảo vệ | Verify ở đâu | Status mong muốn |
|---|---|---|---|
| 1 | MFA Root | Security credentials → MFA | ✅ Active |
| 2 | PDF Invoice | Billing Preferences → Section 1 | ✅ Activated |
| 3 | Free Tier alerts | Billing Preferences → Section 2 | ✅ Delivered to email |
| 4 | CloudWatch billing alerts | Billing Preferences → Section 2 | ✅ Activated |
| 5 | Zero Spend Budget | AWS Budgets | ✅ Active, $0.01 |
| 6 | Monthly $1 Budget | AWS Budgets | ✅ Active, $1.00 |
| 7 | Default Anomaly Monitor | Cost Anomaly → Monitors | ✅ Active (auto) |
| 8 | Strict Anomaly Subscription | Cost Anomaly → Subscriptions | ✅ $1.00, Daily |

→ **8/8 ✅** = Sẵn sàng tạo EC2 an toàn. **Tổng phí setup: $0/tháng.**

### 3.8. Bảng phí THỰC TẾ của các công cụ Billing Protection

Nhiều người mới hiểu lầm rằng các công cụ này tốn tiền. **Tất cả đều FREE:**

| Công cụ | Phí AWS thu | Ghi chú |
|---|---|---|
| MFA cho Root | ✅ Free | |
| Billing Preferences (3 alerts) | ✅ Free | |
| AWS Budgets | ✅ Free **2 budgets đầu** | Budget #3+ tốn $0.02/ngày |
| Cost Anomaly Detection | ✅ Free hoàn toàn | Bao gồm AI/ML engine |
| Budget Actions (auto-stop EC2) | ✅ Free | |
| Cost Explorer (xem dashboard) | ✅ Free | API call tốn $0.01/req |
| SNS Topic (cho Individual alerts) | ✅ Free 1M emails/tháng | Account nhỏ = $0 |

> 💡 **Quan trọng:** Threshold "$1" trong Anomaly Detection là **NGƯỠNG cảnh báo**, KHÔNG phải phí. Giống bạn cài app ngân hàng và set "thông báo khi giao dịch ≥100k" — app không trừ 100k của bạn.

---

## 4. Tạo EC2 + EBS cho Lab Linux Partition

Sau khi xong Mục 3, bạn có thể an tâm tạo EC2. Đây là setting **bắt buộc** để giữ trong Free Tier.

### 4.1. Quy tắc vàng khi launch EC2

Vào [EC2 Console](https://console.aws.amazon.com/ec2/) → **Launch instance**:

| Mục | Setting an toàn | Lý do |
|---|---|---|
| **Region** | `us-east-1` (N. Virginia) | Rẻ nhất, đầy đủ Free Tier |
| **Name** | `linux-partition-lab` | Dễ nhận diện khi cleanup |
| **AMI** | **Ubuntu Server 22.04 LTS** (có nhãn 🟢 *Free tier eligible*) | Dễ học, nhiều tài liệu |
| **Instance type** | **t2.micro** hoặc **t3.micro** (CHỈ 2 loại có Free Tier) | Tránh chọn nhầm `t3.medium` |
| **Key pair** | **Create new key pair** → tải file `.pem` về và lưu cẩn thận | Mất là không SSH vào được |
| **Network → Auto-assign public IP** | **Enable** (chỉ trong khi học), nhớ tắt instance khi xong | Cần Public IP để SSH |
| **Allow SSH traffic from** | **My IP** (KHÔNG chọn `Anywhere 0.0.0.0/0`) | Chỉ IP của bạn SSH được, tránh hack |
| **Storage (Root)** | **8 GB gp3** (mặc định) | Trong 30GB Free Tier |
| **Add new volume** (cho Lab) | **1 GB gp3** | Đây là EBS bạn sẽ phân vùng |

→ Click **Launch instance**. Đợi ~30s status đổi thành `Running`.

### 4.2. SSH vào instance

```bash
# Trên MacOS Terminal
chmod 400 ~/Downloads/your-key.pem
ssh -i ~/Downloads/your-key.pem ubuntu@<PUBLIC_IP>
```

> 💡 Lấy `PUBLIC_IP` từ EC2 Console → click vào instance → tab Details → **Public IPv4 address**.

### 4.3. Verify EBS Volume thứ 2 đã attach

```bash
lsblk
```

Output mong muốn:
```text
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
xvda         202:0    0    8G  0 disk          ← Root disk
└─xvda1      202:1    0    8G  0 part /
xvdb         202:16   0    1G  0 disk          ← EBS Volume cho Lab (CHƯA có partition)
```

→ Nếu thấy `xvdb` (hoặc `nvme1n1` nếu instance đời mới) → có thể bắt đầu Lab `[02-003]` Phương án B.

---

## 5. Checklist DỌN DẸP sau khi xong Lab (BẮT BUỘC)

Đây là phần hay bị quên nhất. **Sau mỗi buổi học, làm theo thứ tự:**

### 5.1. Cleanup nhanh (sau mỗi buổi học)
```text
☐ 1. Terminate EC2 Instance     → EC2 Console → Instances → Actions → Terminate instance
☐ 2. Delete EBS Volumes phụ     → EC2 → Volumes → Actions → Delete volume (cả root + data)
☐ 3. Release Elastic IP (nếu có)→ EC2 → Elastic IPs → Actions → Release Elastic IP
```

### 5.2. Cleanup sâu (cuối tuần)
```text
☐ 4. Delete Snapshots           → EC2 → Snapshots → Actions → Delete
☐ 5. Delete Security Groups     → EC2 → Security Groups → xoá custom (giữ "default")
☐ 6. Delete Key Pairs (optional)→ EC2 → Key Pairs → Actions → Delete
☐ 7. Switch sang region khác    → Lặp lại check (đề phòng tạo nhầm region)
☐ 8. Vào Billing Dashboard      → xem Forecast cho tháng → đảm bảo $0
```

> 🚨 **Cạm bẫy phổ biến:** Khi `Terminate` EC2, EBS root volume thường tự xoá theo. Nhưng EBS volume **bạn tự attach thêm** (volume cho Lab) thì **KHÔNG TỰ XOÁ** — vẫn tính tiền $0.10/GB/tháng. **Phải xoá thủ công ở bước 2.**

### 5.3. Lệnh CLI verify nhanh (nếu đã cài AWS CLI)

```bash
# Kiểm tra EC2 còn chạy không (toàn region phổ biến)
for region in us-east-1 us-west-2 ap-southeast-1; do
  echo "=== $region ==="
  aws ec2 describe-instances --region $region \
    --filters "Name=instance-state-name,Values=running,stopped" \
    --query "Reservations[].Instances[].[InstanceId,State.Name,InstanceType]" \
    --output table
done

# Kiểm tra EBS volume còn không
for region in us-east-1 us-west-2 ap-southeast-1; do
  echo "=== $region ==="
  aws ec2 describe-volumes --region $region \
    --query "Volumes[].[VolumeId,Size,State,Tags]" --output table
done
```

---

## 6. Phương án THAY THẾ - Học mà không cần AWS

Nếu bạn vẫn lo về billing, đây là **3 phương án 100% miễn phí, 100% an toàn**:

### 6.1. Multipass (KHUYẾN NGHỊ NHẤT cho MacOS)
Tool của Canonical (Ubuntu) — tạo Ubuntu VM trên Mac chỉ bằng 1 lệnh:
```bash
brew install --cask multipass
multipass launch --name devops-lab --disk 10G
multipass shell devops-lab
```
→ Vào Ubuntu, có thể thực hành Loop Device (Phương án C trong `[02-003]`).

### 6.2. UTM (Apple Silicon)
GUI virtualization free trên Mac App Store. Tạo VM Ubuntu, attach virtual disk thoải mái.

### 6.3. GitHub Codespaces (60h free/tháng)
- Vào [github.com/codespaces](https://github.com/codespaces) → tạo codespace với Ubuntu image.
- Sudo full quyền, có thể làm Loop Device.

---

## 7. So sánh 4 phương án thực hành Lab Linux

| Tiêu chí | Multipass (Mac) | AWS EC2 | UTM (Mac) | Codespaces |
|---|---|---|---|---|
| **Setup time** | 2 phút | 10 phút (lần đầu) | 15 phút | 5 phút |
| **Chi phí** | $0 | ~$1/tuần (worst case) | $0 | $0 (60h/tháng) |
| **Học AWS Console** | ❌ | ✅ | ❌ | ❌ |
| **Có ổ đĩa thật** | Loop device | ✅ EBS thật | ✅ Virtual disk | Loop device |
| **Yêu cầu Internet** | ❌ | ✅ | ❌ | ✅ |
| **Phù hợp cho** | Học nhanh, không lo bill | Học AWS + Linux song song | Người thích GUI | Học từ tablet/iPad |

> 💡 **Khuyến nghị cá nhân:** Học **Multipass trước** để hoàn thành Lab Linux nhanh. Sau khi quen, dành 1 buổi học **riêng về AWS Console** (tạo EC2 lần đầu). Tách 2 mục tiêu ra, đỡ overload và đỡ rủi ro bill.

---

## 8. Phụ lục - Tạo SNS Topic cho Individual Alerts (Nâng cao)

Nếu bạn muốn Anomaly Detection email NGAY (Individual alerts thay vì Daily summaries), cần tạo SNS Topic.

### 8.1. Tạo SNS Topic
1. Mở [Amazon SNS Console](https://console.aws.amazon.com/sns/v3/home).
2. **Create topic** → Type: **Standard** → Name: `cost-anomaly-alerts` → **Create**.
3. Copy ARN (dạng: `arn:aws:sns:us-east-1:123456789012:cost-anomaly-alerts`).

### 8.2. Subscribe Email
1. Tab **Subscriptions** → **Create subscription**.
2. Protocol: **Email** → Endpoint: email của bạn.
3. **Quan trọng:** Vào inbox email → click link **Confirm subscription**.

### 8.3. Cấp quyền cho Cost Anomaly publish
Trang detail SNS topic → tab **Access policy** → **Edit** → paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AWSAnomalyDetectionSNSPublishingPermissions",
      "Effect": "Allow",
      "Principal": {
        "Service": "costalerts.amazonaws.com"
      },
      "Action": "SNS:Publish",
      "Resource": "arn:aws:sns:us-east-1:YOUR_ACCOUNT_ID:cost-anomaly-alerts"
    }
  ]
}
```

### 8.4. Cập nhật Subscription
Quay lại Cost Anomaly → edit subscription → đổi frequency thành **Individual alerts** → paste SNS ARN.

> 💡 **Phí SNS:** Free 1 triệu emails/tháng — account học tập gần như chắc chắn = $0.

---

## 9. Bài tập thực hành (Lab 4)

### Lab 4.1 - Setup Billing Protection toàn diện (làm 1 lần)
1. Bật MFA cho Root account (cài Google Authenticator nếu chưa có).
2. Setup 2 sections trong Billing Preferences (PDF + Free Tier alert + CloudWatch billing alert).
3. Tạo `Zero Spend Budget` ($0.01).
4. Tạo `Monthly $1 Budget`.
5. Tạo `My-Strict-1USD-Alert` subscription cho Default Anomaly Monitor.
6. **Verify checklist 8/8** ở Mục 3.7.
7. Chụp screenshot trang Billing Dashboard, lưu vào `05-Document/aws-billing-protection-proof.png`.

### Lab 4.2 - Tạo & Cleanup EC2 (chu trình hoàn chỉnh)
1. Launch 1 EC2 `t3.micro` Ubuntu 22.04 ở `us-east-1` với 1 EBS Volume phụ 1GB.
2. SSH vào instance, chạy `lsblk` để verify thấy 2 disk.
3. Logout SSH.
4. **DỌN DẸP NGAY:** Terminate instance + Delete EBS volume phụ.
5. Vào Billing Dashboard → Tab **Bills** → confirm `Forecast` tháng = $0 hoặc < $1.

### Lab 4.3 - Test cảnh báo (optional, advanced)
1. Tạo EC2 `t3.medium` (KHÔNG phải Free Tier) — sẽ tốn ~$0.04/giờ.
2. Để chạy đúng **1 giờ** rồi tắt ngay.
3. Đợi 24-48h → check email inbox → phải nhận được:
   - Email từ `Zero Spend Budget` (vì vượt $0.01).
   - Có thể nhận từ `Cost Anomaly Detection` (nếu ML phát hiện).
4. Nếu nhận đủ email → setup billing đã hoạt động đúng.

---

## 10. Tài nguyên tham khảo

**AWS Official:**
- [AWS Free Tier Details](https://aws.amazon.com/free/)
- [AWS Cost Anomaly Detection Pricing](https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/) — confirm $0
- [AWS Budgets Best Practices](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-best-practices.html)

**Câu chuyện cảnh báo (đáng đọc):**
- Search Google: `"AWS bill shock" reddit` — hàng nghìn case study thực tế.
- Search Google: `AWS forgotten EC2 cost` — case study bill $5000+ vì quên EC2.

**Tools/Skills nâng cao (học sau):**
- **AWS CLI** — quản lý từ terminal thay vì click console.
- **AWS Cost Explorer** — phân tích chi phí theo service/tag/time.
- **AWS Trusted Advisor** — auto-detect resources lãng phí (cần Business Support).
- **FinOps Foundation** — methodology tối ưu cloud cost ở scale doanh nghiệp.

# Lộ trình học DevOps (1 Năm - Từ Zero đến Production)

## Document Information
- **Doc ID:** 01-002
- **Summary:** Kế hoạch học tập DevOps chi tiết theo từng tuần trong vòng 1 năm, đi từ xương sống đến mở rộng.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 📌 Nguyên tắc của lộ trình (The Backbone Strategy)
Lộ trình này được thiết kế dựa trên nguyên tắc **"Xương sống trước, đắp thịt sau"**. Thay vì học dàn trải mọi công cụ (như trong Roadmap.sh), chúng ta sẽ:
1. Xây dựng một luồng CI/CD hoàn chỉnh từ code đến production càng sớm càng tốt bằng các công cụ cơ bản nhất.
2. Sau khi có "xương sống", mới bắt đầu mở rộng ra các công cụ phức tạp hơn (K8s, Terraform, Prometheus) để giải quyết các bài toán về scale và vận hành.

---

## 🗓️ Lộ trình 52 Tuần (1 Năm)

### Phase 1: Nền tảng sống còn (Tuần 1 - Tuần 8)
*Mục tiêu: Giao tiếp được với máy chủ, hiểu mạng cơ bản và quản lý được mã nguồn.*

- **Tuần 1-2: Linux & Terminal (Sống trong Terminal)**
  - Các lệnh cơ bản (ls, cd, grep, find, tar).
  - Quản lý user, phân quyền (chmod, chown).
  - Quản lý process (top, ps, kill) và service (systemd).
  - Vim/Nano cơ bản.
- **Tuần 3-4: Bash Scripting (Tự động hóa thủ công)**
  - Biến, vòng lặp, câu điều kiện.
  - Viết script backup file, monitor dung lượng ổ cứng.
- **Tuần 5-6: Computer Networking (Mạng cơ bản)**
  - Mô hình OSI & TCP/IP.
  - DNS hoạt động như thế nào?
  - HTTP/HTTPS, SSL/TLS, SSH.
  - Các lệnh mạng: ping, curl, netstat, nmap.
- **Tuần 7-8: Git & GitHub (Quản lý mã nguồn)**
  - Git cơ bản (add, commit, push, pull).
  - Branching, Merge, Rebase, Resolve conflicts.
  - Git Flow cơ bản.

### Phase 2: Đóng gói Ứng dụng (Tuần 9 - Tuần 14)
*Mục tiêu: Ứng dụng chạy được trên máy tôi thì cũng phải chạy được trên máy bạn.*

- **Tuần 9: Ngôn ngữ lập trình (Python/Go/NodeJS - Tùy chọn 1)**
  - Học cú pháp cơ bản để đọc hiểu code của Dev.
  - Viết một Web API đơn giản (Hello World).
- **Tuần 10-12: Docker (Containerization)**
  - Kiến trúc Docker (Images, Containers, Volumes, Networks).
  - Viết Dockerfile tối ưu (Multi-stage build).
  - Đóng gói Web API ở Tuần 9 thành Docker Image.
- **Tuần 13-14: Docker Compose**
  - Chạy nhiều container cùng lúc (Web + Database).
  - Cấu hình network và volume cho Compose.

### Phase 3: Xương sống CI/CD (Tuần 15 - Tuần 22)
*Mục tiêu: Tự động hóa quá trình Test -> Build -> Deploy.*

- **Tuần 15-16: GitHub Actions (CI cơ bản)**
  - Khái niệm Workflow, Job, Step.
  - Tự động chạy test khi có code mới push lên GitHub.
  - Tự động build Docker Image và push lên Docker Hub.
- **Tuần 17-19: Jenkins (Công cụ CI/CD công nghiệp)**
  - Cài đặt Jenkins.
  - Viết Jenkinsfile (Declarative Pipeline).
  - Tích hợp Webhook với GitHub.
- **Tuần 20-22: Web Servers & Reverse Proxy**
  - Nginx cơ bản (Cấu hình Reverse Proxy, Load Balancer).
  - Triển khai ứng dụng lên 1 VPS Linux (EC2/DigitalOcean) dùng Nginx + Docker.
  - **[Project 1]**: Pipeline hoàn chỉnh: Push code -> Jenkins build Docker -> Deploy lên VPS qua SSH -> Nginx trỏ domain vào.

### Phase 4: Hạ tầng Đám mây & IaC (Tuần 23 - Tuần 32)
*Mục tiêu: Không click tay tạo server nữa, mọi thứ phải là Code.*

- **Tuần 23-26: Cloud Provider (AWS là lựa chọn tốt nhất)**
  - IAM (Quản lý quyền).
  - VPC (Mạng ảo, Subnet, Security Group).
  - EC2 (Máy chủ ảo), S3 (Lưu trữ file).
  - RDS (Database managed).
- **Tuần 27-30: Terraform (Infrastructure Provisioning)**
  - Khái niệm State, Providers, Resources.
  - Viết code tạo VPC, EC2, Security Group trên AWS.
  - Quản lý Remote State với S3.
- **Tuần 31-32: Ansible (Configuration Management)**
  - Khái niệm Playbook, Inventory, Roles.
  - Dùng Ansible để tự động cài Docker, Nginx lên 10 con EC2 cùng lúc.
  - **[Project 2]**: Dùng Terraform tạo hạ tầng AWS -> Dùng Ansible cấu hình server -> Dùng Jenkins deploy app.

### Phase 5: Container Orchestration (Tuần 33 - Tuần 42)
*Mục tiêu: Quản lý hàng trăm container, tự động scale, tự động phục hồi.*

- **Tuần 33-38: Kubernetes (K8s - Trùm cuối)**
  - Kiến trúc K8s (Control Plane, Worker Nodes).
  - Các object cốt lõi: Pod, ReplicaSet, Deployment, Service, Ingress.
  - ConfigMap, Secret, Persistent Volume.
  - Triển khai cụm K8s thực tế (Dùng EKS trên AWS hoặc Minikube ở local).
- **Tuần 39-40: Helm (Package Manager cho K8s)**
  - Viết Helm Chart để đóng gói ứng dụng K8s.
- **Tuần 41-42: GitOps với ArgoCD**
  - Khái niệm GitOps (Kéo code thay vì Đẩy code).
  - Dùng ArgoCD để tự động deploy app lên K8s khi có thay đổi trong Git.
  - **[Project 3]**: Chuyển toàn bộ hệ thống từ EC2/Docker sang chạy trên EKS (K8s) + ArgoCD.

### Phase 6: Vận hành, Giám sát & Bảo mật (Tuần 43 - Tuần 52)
*Mục tiêu: Hệ thống chết phải biết ngay lập tức, bảo mật từ trong trứng nước.*

- **Tuần 43-45: Monitoring & Alerting**
  - Prometheus (Thu thập metrics).
  - Grafana (Vẽ biểu đồ Dashboard).
  - Cài đặt Alertmanager bắn cảnh báo về Slack/Telegram khi CPU > 80%.
- **Tuần 46-48: Logging Management**
  - ELK Stack (Elasticsearch, Logstash, Kibana) hoặc EFK (Fluentd).
  - Gom log từ tất cả các container/server về 1 chỗ để dễ search.
- **Tuần 49-50: DevSecOps (Bảo mật)**
  - Quét lỗ hổng code (SonarQube).
  - Quét lỗ hổng Docker Image (Trivy).
  - Tích hợp các bước quét này vào CI/CD Pipeline.
- **Tuần 51-52: Final Project & Review**
  - **[Project 4 - Masterpiece]**: Xây dựng hệ thống Production-ready từ A-Z: 
    - Code nằm trên GitHub.
    - Terraform tạo EKS, RDS, VPC.
    - GitHub Actions build Image + quét Trivy.
    - ArgoCD deploy lên EKS.
    - Prometheus/Grafana giám sát toàn cụm.
    - ELK gom log.

---
*Ghi chú: Lộ trình này mang tính định hướng. Tùy vào tốc độ tiếp thu, bạn có thể rút ngắn hoặc kéo dài thời gian ở từng Phase.*
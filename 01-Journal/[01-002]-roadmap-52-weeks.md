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
2. Nắm vững Database (Stateful) song song với App (Stateless).
3. Sau khi có "xương sống", mới bắt đầu mở rộng ra các công cụ phức tạp hơn (K8s, Terraform, Observability) để giải quyết các bài toán về scale và vận hành.

---

## 🗓️ Lộ trình 52 Tuần (1 Năm)

### Phase 1: Nền tảng sống còn & Lập trình (Tuần 1 - Tuần 10)
*Mục tiêu: Giao tiếp được với máy chủ, hiểu mạng cơ bản, quản lý mã nguồn và biết code tự động hóa.*

- **Tuần 1-2: Linux & Terminal (Sống trong Terminal)**
  - Các lệnh cơ bản (ls, cd, grep, awk, sed, tar).
  - Quản lý user, phân quyền (chmod, chown).
  - Quản lý process (top, htop, ps, kill) và service (systemd).
  - Vim/Nano cơ bản.
- **Tuần 3-4: Bash Scripting (Tự động hóa thủ công)**
  - Biến, vòng lặp, câu điều kiện.
  - Viết script backup file, monitor dung lượng ổ cứng.
- **Tuần 5-6: Ngôn ngữ lập trình (Python hoặc Go)**
  - Học cú pháp cơ bản để đọc hiểu code của Dev.
  - Thao tác với JSON/YAML, gọi REST API.
  - Viết một Web API đơn giản (Hello World) để làm mồi cho các Phase sau.
- **Tuần 7-8: Computer Networking (Mạng cơ bản)**
  - Mô hình OSI & TCP/IP.
  - DNS hoạt động như thế nào?
  - HTTP/HTTPS, SSL/TLS, SSH.
  - Các lệnh mạng: ping, curl, netstat, nmap.
- **Tuần 9-10: Git & GitHub (Quản lý mã nguồn)**
  - Git cơ bản (add, commit, push, pull).
  - Branching, Merge, Rebase, Resolve conflicts.
  - Git Flow cơ bản.

### Phase 2: Đóng gói Ứng dụng & Quản lý Dữ liệu (Tuần 11 - Tuần 16)
*Mục tiêu: Ứng dụng chạy được trên máy tôi thì cũng phải chạy được trên máy bạn. Quản lý được Database.*

- **Tuần 11-13: Docker (Containerization)**
  - Kiến trúc Docker (Images, Containers, Networks).
  - Viết Dockerfile tối ưu (Multi-stage build, giảm dung lượng image).
  - Đóng gói Web API ở Tuần 5 thành Docker Image.
- **Tuần 14: Docker Volumes & Database Basics**
  - Hiểu về Stateless vs Stateful.
  - Cách mount volume để giữ lại data cho Database (MySQL/PostgreSQL) khi container chết.
  - Các lệnh SQL cơ bản, cách backup/restore database thủ công.
- **Tuần 15-16: Docker Compose**
  - Chạy nhiều container cùng lúc (Web API + Database + Redis).
  - Cấu hình network nội bộ cho các container nói chuyện với nhau.

### Phase 3: Xương sống CI/CD (Tuần 17 - Tuần 24)
*Mục tiêu: Tự động hóa quá trình Test -> Build -> Deploy.*

- **Tuần 17-18: GitHub Actions (CI cơ bản)**
  - Khái niệm Workflow, Job, Step.
  - Tự động chạy test khi có code mới push lên GitHub.
  - Tự động build Docker Image và push lên Docker Hub.
- **Tuần 19-21: Jenkins (Công cụ CI/CD công nghiệp)**
  - Cài đặt Jenkins bằng Docker.
  - Viết Jenkinsfile (Declarative Pipeline).
  - Quản lý Secrets/Credentials trong Jenkins.
- **Tuần 22-24: Web Servers & Reverse Proxy**
  - Nginx cơ bản (Cấu hình Reverse Proxy, Load Balancer, SSL/TLS với Certbot).
  - **[Project 1]**: Pipeline hoàn chỉnh: Push code -> Jenkins build Docker -> Deploy lên VPS (EC2) qua SSH -> Nginx trỏ domain vào.

### Phase 4: Hạ tầng Đám mây & IaC (Tuần 25 - Tuần 34)
*Mục tiêu: Không click tay tạo server nữa, mọi thứ phải là Code.*

- **Tuần 25-28: Cloud Provider (AWS là lựa chọn tốt nhất)**
  - IAM (Quản lý quyền, Role, Policy).
  - VPC (Mạng ảo, Public/Private Subnet, Security Group, NAT Gateway).
  - EC2 (Máy chủ ảo), S3 (Lưu trữ file).
  - RDS (Managed Database - Cách AWS quản lý backup/HA cho DB).
- **Tuần 29-32: Terraform (Infrastructure Provisioning)**
  - Khái niệm State, Providers, Resources, Modules.
  - Viết code tạo VPC, EC2, RDS trên AWS.
  - Quản lý Remote State với S3 và DynamoDB (State locking).
- **Tuần 33-34: Ansible (Configuration Management)**
  - Khái niệm Playbook, Inventory, Roles.
  - Dùng Ansible để tự động cài Docker, Nginx lên nhiều EC2 cùng lúc.
  - **[Project 2]**: Dùng Terraform tạo hạ tầng AWS (VPC, EC2, RDS) -> Dùng Ansible cấu hình server -> Dùng GitHub Actions deploy app.

### Phase 5: Container Orchestration (Tuần 35 - Tuần 44)
*Mục tiêu: Quản lý hàng trăm container, tự động scale, tự động phục hồi.*

- **Tuần 35-40: Kubernetes (K8s - Trùm cuối)**
  - Kiến trúc K8s (Control Plane, Worker Nodes).
  - Các object cốt lõi: Pod, ReplicaSet, Deployment, Service, Ingress.
  - ConfigMap, Secret, Persistent Volume (PV/PVC).
  - Triển khai cụm K8s thực tế (Dùng EKS trên AWS hoặc Minikube ở local).
- **Tuần 41-42: Helm (Package Manager cho K8s)**
  - Viết Helm Chart để đóng gói ứng dụng K8s.
- **Tuần 43-44: GitOps với ArgoCD**
  - Khái niệm GitOps (Kéo code thay vì Đẩy code).
  - Dùng ArgoCD để tự động deploy app lên K8s khi có thay đổi trong Git.
  - **[Project 3]**: Chuyển toàn bộ hệ thống từ EC2/Docker sang chạy trên EKS (K8s) + ArgoCD.

### Phase 6: Observability, Logging & Security (Tuần 45 - Tuần 52)
*Mục tiêu: Hệ thống chết phải biết ngay lập tức, biết chính xác lỗi ở đâu và bảo mật từ trong trứng nước.*

- **Tuần 45-47: Monitoring & Alerting**
  - Prometheus (Thu thập metrics).
  - Grafana (Vẽ biểu đồ Dashboard).
  - Cài đặt Alertmanager bắn cảnh báo về Slack/Telegram khi CPU > 80%.
- **Tuần 48-49: Logging & Distributed Tracing (Observability)**
  - ELK Stack (Elasticsearch, Logstash, Kibana) hoặc Promtail/Loki.
  - Gom log từ tất cả các container/server về 1 chỗ.
  - Khái niệm Distributed Tracing (Jaeger/OpenTelemetry) để dò vết request qua nhiều microservices.
- **Tuần 50-51: DevSecOps (Bảo mật)**
  - Quét lỗ hổng code (SonarQube).
  - Quét lỗ hổng Docker Image (Trivy).
  - Tích hợp các bước quét này vào CI/CD Pipeline.
- **Tuần 52: Final Project & Review**
  - **[Project 4 - Masterpiece]**: Xây dựng hệ thống Production-ready từ A-Z: 
    - Code nằm trên GitHub.
    - Terraform tạo EKS, RDS, VPC.
    - GitHub Actions build Image + quét Trivy.
    - ArgoCD deploy lên EKS.
    - Prometheus/Grafana/Loki giám sát toàn cụm.

---
*Ghi chú: Lộ trình này mang tính định hướng. Tùy vào tốc độ tiếp thu, bạn có thể rút ngắn hoặc kéo dài thời gian ở từng Phase.*
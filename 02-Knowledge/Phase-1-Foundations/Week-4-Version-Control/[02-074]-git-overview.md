# Tổng quan Git (Git Overview)

## Document Information
- **Doc ID:** 02-074
- **Summary:** Version control (nội dung, team, agility); nội dung nào nên quản lý bằng Git; DVCS; repository và commit; CLI vs Sourcetree — video Git Overview, Module 1.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Version control** quản lý **project** — tập file thay đổi liên tục — với **toàn bộ lịch sử** luôn truy cập được. **Git** là DVCS mã nguồn mở, phổ biến cho code và mọi tài sản “cần cải tiến liên tục” (config, test, docs). *(Mối quan hệ DevOps–commit–branch: [02-073].)*

---

## Block 1 — Mô hình (Structure)

### Ba góc nhìn về version control

```text
                    ┌─────────────────────────────────────┐
                    │         PROJECT (tập file)           │
                    └─────────────────────────────────────┘
                           │           │           │
              Content      │   Teams   │  Agility  │
                           ▼           ▼           ▼
              Lịch sử đầy đủ   Workflow đa dạng   Thay đổi nhỏ,
              mọi lúc          Collaboration,      test / fix / undo
                               review, chất lượng  nhanh khi môi trường đổi
```

| Góc nhìn | Git hỗ trợ |
|----------|------------|
| **Content** | Theo dõi mọi phiên bản file trong project |
| **Teams** | Nhiều workflow; đồng bộ, review, giao tiếp |
| **Agility** | Batch nhỏ; dễ thử ý tưởng, sửa hoặc hoàn tác |

### Nội dung nào nên đưa vào Git?

Hầu hết VCS **không giới hạn loại file** — miễn là nội dung **có giá trị** và **cải tiến liên tục**.

| Loại nội dung | Ghi chú |
|---------------|---------|
| **Source code** | Phổ biến nhất; Git rất mạnh với **text** |
| **Test code** | Cùng vòng đời với app; luôn được cải thiện |
| **Cấu hình hạ tầng (IT)** | Rebuild / audit infra; IaC (Terraform, Ansible, …) |
| **Tài liệu, sách, website** | Nội dung thay đổi thường xuyên |
| *Không chỉ “dành cho dev”* | Ops, technical writer, bất kỳ ai quản lý tài sản sống |

### Distributed Version Control System (DVCS)

```text
        Remote repository (source of truth — cloud / data center)
              ▲                    ▲
              │ push               │ pull
              │                    │
    ┌─────────┴─────────┐  ┌──────┴──────────┐
    │  User A — local    │  │  User B — local  │
    │  repo (full history)│  │  repo (full history)│
    └───────────────────┘  └──────────────────┘
              Offline OK — đồng bộ sau bằng pull / push
```

| Đặc điểm DVCS | Ý nghĩa |
|---------------|---------|
| Mỗi user có **bản sao đầy đủ** lịch sử (local **repository**) | Làm việc **offline**; commit local trước khi sync |
| Có **một remote** làm **source of truth** | Team thống nhất trạng thái “chính thức” |
| Đồng bộ qua **pull** / **push** | Lấy thay đổi từ remote / đẩy commit lên remote |

### Git: repository và commit (snapshot)

```text
Commit 1          Commit 2                    Commit 3
┌──────────┐      ┌──────────┐               ┌──────────┐
│ file.txt │  →   │ file.txt │               │ file.txt │  (đã sửa — “v2”)
│  (mới)   │      │ img/     │               │ img/     │
└──────────┘      │ image.png│               │ image.png│
                  └──────────┘               └──────────┘
```

- **Repository** = chuỗi **commit** theo thời gian.
- Mỗi **commit** = **snapshot toàn bộ** thư mục + file tại thời điểm đó (kể cả file không đổi vẫn nằm trong “ảnh” project).
- Có thể **xem lại** project ở commit cũ bất cứ lúc nào.

**Git thêm:** mã nguồn mở, không một công ty sở hữu; ecosystem lớn (GitHub, Bitbucket, CI, …); scale từ project nhỏ tới rất lớn (ví dụ phát triển Linux).

### CLI vs GUI (khóa học)

| | **Command line** | **Sourcetree** (GUI) |
|---|------------------|----------------------|
| **Ưu** | Kỹ năng chuẩn cho dev/IT; **tự động hóa** (script/CI); Git được thiết kế/document cho CLI; thường nhanh | Dễ hơn nếu **chưa quen terminal**; một số tác vụ trực quan (history, interactive rebase) |
| **Nhược** | Học terminal + Git cùng lúc có thể khó | Ít phù hợp automation thuần CLI |
| **Gợi ý khóa** | Bạn đã học Week 1–3 terminal → **path Command line** | Nếu chưa chắc → Sourcetree trước, quay lại CLI sau; hoặc làm **cả hai path** để củng cố |

Khái niệm học được **giống nhau**; lab/video được gắn nhãn **Command Line** hoặc **Sourcetree** — chọn một path cho phần hands-on.

---

## Block 2 — Thuật ngữ & quyết định (Application)

### Thuật ngữ

| Thuật ngữ | Định nghĩa ngắn |
|-----------|-----------------|
| **Project** | Tập file được version control quản lý |
| **Repository (repo)** | Kho chứa toàn bộ lịch sử project |
| **Commit** | Snapshot project tại một thời điểm |
| **DVCS** | Mỗi máy có full history; sync qua remote |
| **Remote** | Repo “chính thức” trên server (Bitbucket, GitHub, …) |
| **Pull / Push** | Kéo thay đổi từ remote / đẩy commit lên remote |

### Checklist: path học trong khóa

| Câu hỏi | Gợi ý |
|---------|--------|
| Đã quen `cd`, `ls`, shell cơ bản? | → **Command line** |
| Mới terminal, muốn tập trung Git trước? | → **Sourcetree**, sau đó học lại path CLI |
| Muốn tối đa cho DevOps/CI? | → Ưu tiên **CLI** (pipeline không dùng GUI) |
| Dùng Git thưa, dễ quên lệnh? | GUI hỗ trợ nhớ thao tác; vẫn nên hiểu khái niệm CLI |

*Lệnh cài đặt, `init`, `add`, `commit`: bài hands-on tiếp theo Module 1.*

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Một repo cho code + test + infra config

**Bối cảnh:** Team DevOps quản lý microservice, pipeline test và file Terraform trên cùng org.

| Thành phần trong Git | Lợi ích |
|----------------------|---------|
| `src/` — application code | Review PR, trace bug theo commit |
| `tests/` — automation | Test “sống” cùng version code; CI chạy trên từng push |
| `infra/` — Terraform/Ansible | Rebuild môi trường; audit ai đổi rule firewall |
| `docs/runbook.md` | Runbook cập nhật theo thay đổi hệ thống |

**DVCS trong thực tế:** Kỹ sư trên máy bay / site không có mạng vẫn **commit local**; về văn phòng **push** lên remote → CI chạy → đồng nghiệp **pull**.

**Automation:** Script deploy gọi `git pull` trên server; pipeline Jenkins/GitHub Actions clone repo — đều cần **CLI**, không phụ thuộc Sourcetree.

| Bước tiếp (trong khóa) | Hành động |
|------------------------|-----------|
| 1 | Chọn path **Command Line** hoặc **Sourcetree** |
| 2 | Lab: cài Git → tạo local repo → commit đầu tiên |
| 3 | Remote (Bitbucket) → push |

---

*Nguồn học: video **Git Overview**, Module 1 — Version Control with Git (Atlassian / Coursera).*

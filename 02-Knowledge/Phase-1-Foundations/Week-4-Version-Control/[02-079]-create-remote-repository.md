# Tạo remote repository (Create a Remote Repository)

## Document Information
- **Doc ID:** 02-079
- **Summary:** Remote bare repo, source of truth; Bitbucket/GitHub; tạo repo trên host; import repo cũ — Module 1 (chưa liên kết local).
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Remote repository** là bản Git trên server (cloud / data center), thường là **source of truth** của team — khác với repo **local** trên máy bạn ([02-076], [02-077]). Bài này: **tạo repo trống** (hoặc **import**) trên hosting; bước **push** local lên remote ở video/lab tiếp theo.

---

## Block 1 — Mô hình (Structure)

### Local vs remote

```text
  MÁY BẠN (local)                    HOSTING (remote)
 ┌────────────────────────┐          ┌─────────────────────────┐
 │ project/               │          │ repoa.git  (BARE)        │
 │  working tree          │  push    │  • chỉ objects + refs    │
 │  .git/ (staging+hist)  │ ───────► │  • KHÔNG working tree    │
 └────────────────────────┘  pull    │  • KHÔNG staging area    │
                                     │  • source of truth       │
                                     └───────────┬─────────────┘
                                                 │
                                     issue tracker, CI/CD pipeline
```

| Đặc điểm | Remote (thường gặp) | Local |
|----------|---------------------|--------|
| Vị trí | Cloud / DC, quản lý bởi provider | Máy developer |
| Working tree / staging | **Không** (bare repo) | Có |
| Cấu trúc gốc | Giống nội dung thư mục **`.git/`** | Project dir + `.git/` |
| Vai trò | Trạng thái “chính thức”, chia sẻ team | Làm việc, commit, thử nghiệm |

### Loại hosting

| Loại | Ví dụ |
|------|--------|
| **Hosted (SaaS)** | [Bitbucket](https://bitbucket.org), [GitHub](https://github.com) |
| **On-premise / self-hosted** | Bitbucket Server, GitHub Enterprise, GitLab self-hosted, … |
| **Triển khai** | Data center hoặc cloud của tổ chức |

Khóa dùng **Bitbucket**; thao tác tương tự trên GitHub/GitLab.

### URL remote — quy ước `.git`

```text
https://bitbucket.org/<workspace>/repoa.git
                                              └── tên repo + hậu tố .git
```

Provider thường **tự thêm** `.git` vào URL khi bạn đặt tên repository (vd. `repoa`).

---

## Block 2 — Thao tác trên hosting (Application)

**Prerequisite:** Tài khoản Bitbucket (hoặc GitHub); đã có local repo + commit ([02-077], [02-078]) — chưa cần cho bước *tạo* remote.

### Tạo remote repository trống (Bitbucket)

| Bước | Thao tác |
|------|----------|
| 1 | Đăng nhập Bitbucket |
| 2 | **+** (menu trái) hoặc **Create repository** |
| 3 | Nhập **Repository name** (vd. `repoa`) |
| 4 | **Create repository** |
| 5 | Sao chép **remote URL** (HTTPS hoặc SSH) — dùng ở bài **Push** |

**Lưu ý khi vừa có local commit:**

- Tạo repo **không** tick “Initialize with README” nếu bạn sẽ push history local lên (tránh unrelated histories).
- Repo trống trên server = sẵn sàng nhận `git push` từ máy bạn.

### Import repository có sẵn (thay vì tạo mới)

| Bước | Thao tác |
|------|----------|
| 1 | Chọn **Import repository** (thay vì Create) |
| 2 | Chọn **loại** nguồn (Git, Subversion, …) |
| 3 | Dán **URL repo cũ** |
| 4 | **Import repository** → remote mới trên Bitbucket chứa lịch sử đã import |

Dùng khi **di chuyển** từ host khác hoặc từ SVN — không phải luồng “local mới → push lần đầu”.

### Thuật ngữ

| Thuật ngữ | Ý nghĩa |
|-----------|---------|
| **Bare repository** | Chỉ lưu Git metadata/commits; không checkout làm việc trực tiếp trên server |
| **Source of truth** | Trạng thái chính thức team thống nhất |
| **`.git` suffix** | Quy ước URL remote |

*Liên kết local ↔ remote: `git remote add`, `git push` — bài tiếp theo.*

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Repo trung tâm cho service + pipeline

**Bối cảnh:** Team tạo `payment-api` trên Bitbucket làm remote chính; developer đã commit trên laptop.

| Bước | Hành động |
|------|-----------|
| 1 | Admin tạo repo **private** `payment-api` trên Bitbucket |
| 2 | Bật tích hợp **Jira** / issue tracker (nếu org dùng Atlassian) |
| 3 | Gắn **pipeline** (Bitbucket Pipelines / Jenkins webhook) — trigger trên push |
| 4 | Developer: `git remote add origin <url>` → `git push -u origin main` |
| 5 | Mọi merge vào `main` trên remote → CI build/test → deploy |

**Bare repo trên server:** Máy CI chỉ **clone/fetch** — không ai SSH vào server để sửa file trong “working tree” của remote.

**Import:** Khi công ty **migrate** từ GitHub công ty cũ sang Bitbucket — dùng Import thay vì tạo repo trống rồi push tay từng nhánh.

### Lab khóa

1. Tạo remote repository trên Bitbucket (tên tùy chọn)
2. Ghi lại HTTPS/SSH URL
3. *(Lab push riêng)* Đưa local repository lên remote

---

*Nguồn học: video **Create a Remote Repository**, Module 1 — Version Control with Git (Atlassian / Coursera).*

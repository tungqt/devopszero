# Vị trí trong Git (Git Locations)

## Document Information
- **Doc ID:** 02-076
- **Summary:** Working tree, staging area (index), local repo, project directory, `.git/`, remote — nền tảng trước `add`/`commit`/`push`.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Mỗi thao tác Git diễn ra ở **một trong các “vị trí”** sau. Nắm được sơ đồ này thì `status`, `add`, `commit`, `push` sẽ có ý nghĩa rõ — video chỉ giới thiệu; chi tiết lệnh bổ sung dần trong Module 1. *(Cài đặt & config: [02-075].)*

---

## Block 1 — Mô hình (Structure)

### Bốn vùng + project directory

```text
  MÁY LOCAL                              REMOTE (cloud / DC)
 ┌─────────────────────────────────┐    ┌──────────────────────┐
 │  PROJECT DIRECTORY               │    │  Remote repository    │
 │  (thư mục làm việc của bạn)      │    │  (source of truth)    │
 │                                  │    │  • toàn bộ commit     │
 │  ┌────────────────────────────┐  │    │  • đồng bộ = cùng     │
 │  │ WORKING TREE               │  │    │    commit với local   │
 │  │ • file/thư mục 1 commit    │  │◄──►│    (khi đã sync)      │
 │  │ • xem / sửa → chuẩn bị     │  │ push│                      │
 │  │   commit tiếp theo         │  │ pull│                      │
 │  └───────────┬────────────────┘  │    └──────────────────────┘
 │              │ git add            │
 │              ▼                    │
 │  ┌────────────────────────────┐  │
 │  │ .git/  (ẩn)               │  │
 │  │  • STAGING AREA (index)    │  │
 │  │    danh sách file cho      │  │
 │  │    commit KẾ TIẾP          │  │
 │  │  • LOCAL REPOSITORY        │  │
 │  │    mọi commit + lịch sử    │  │
 │  └────────────────────────────┘  │
 └─────────────────────────────────┘
```

| Vị trí | Chứa gì | Ghi chú |
|--------|---------|---------|
| **Working tree** | File/thư mục của **một snapshot** (một commit đang “checkout”) | Nơi bạn **sửa** nội dung trước commit mới |
| **Staging area** (*index*) | **Danh sách** file sẽ vào commit **tiếp theo** | Chuẩn bị snapshot có ý nghĩa — không nhất thiết stage mọi thay đổi |
| **Local repository** | **Tất cả commit** đã tạo trên máy | Lịch sử phiên bản project |
| **Project directory** | Working tree + thư mục **`.git/`** | Một folder trên disk = một project Git local |
| **Remote repository** | Commit trên server | Trạng thái “chính thức” của team khi đã đồng bộ |

### Luồng khái niệm (sẽ thực hành sau)

```text
checkout commit  →  working tree = bản copy file của commit đó
sửa file         →  working tree thay đổi
git add          →  đưa file vào staging area
git commit       →  ghi snapshot từ staging → local repository (commit mới)
git push         →  đưa commit lên remote (khi đã có remote)
```

**Checkout** (học sau): đặt nội dung một commit vào working tree.

### Cảnh báo quan trọng

```text
Xóa PROJECT DIRECTORY  =  xóa working tree + .git/
                         =  mất staging area + LOCAL REPOSITORY
                         (trừ khi đã push lên remote và clone lại được)
```

---

## Block 2 — Lệnh & quan sát (Application)

**Prerequisite:** Đã `git init` hoặc `git clone` trong một project directory ([02-075]).

### Nhìn thấy cấu trúc trên disk

```bash
cd ~/repos/my-project          # project directory

ls -la                         # file working tree + thư mục .git/
ls -la .git/                   # metadata Git (không sửa tay)

git status                     # file: untracked / modified / staged
```

| Trạng thái (khái niệm) | Vùng liên quan |
|------------------------|----------------|
| File mới / sửa chưa `add` | Working tree |
| “Changes to be committed” | Staging area |
| Commit đã tạo | Local repository (trong `.git/`) |
| `git push` thành công | Remote có cùng commit |

### Lệnh gắn với từng vùng (preview)

```bash
# Working tree → staging
git add README.md
git add .

# Staging → local repository
git commit -m "Describe the snapshot"

# Local → remote (sau khi có origin)
git push origin main
```

| Lệnh | Tác động chính |
|------|----------------|
| `git add <file>` | Đưa file (mới/sửa) vào **staging area** |
| `git commit` | Tạo **commit** trong **local repository** từ staging |
| `git push` | Đồng bộ commit lên **remote** |

### Thuật ngữ đồng nghĩa

| Thuật ngữ khóa | Cùng nghĩa |
|----------------|------------|
| Staging area | **Index** |
| Working tree | Working directory (ngữ cảnh file trên disk) |

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Sửa config trên server clone — biết file đang ở vùng nào

**Bối cảnh:** Trên EC2 có clone repo deploy; bạn sửa `config.yml` để test nhanh.

| Hành động | Vùng | Rủi ro / thực hành tốt |
|-----------|------|-------------------------|
| Sửa `config.yml` trực tiếp | **Working tree** | `git status` thấy *modified* — thay đổi **chưa** an toàn cho team |
| Quên `add` + `commit` | Chỉ local, không có lịch sử | Mất track; restart có thể ghi đè |
| `git add` + `git commit` | Local **repository** | Có history; có thể `push` hoặc cherry-pick |
| Chỉ cần đồng bộ với team | **Remote** phải nhận `push` | Production thường **không** sửa tay trên server — dùng pipeline từ remote |

**Trên máy dev:** Giữ mọi project trong `~/repos/`; **không** xóa nhầm folder project (mất `.git/`). Backup = push lên remote thường xuyên.

**Đồng bộ:** Khi local và remote **đã sync**, hai bên chứa **cùng tập commit** — `git pull` / `git push` để duy trì trạng thái đó.

---

*Nguồn học: video **Git Locations**, Module 1 — Version Control with Git (Atlassian / Coursera).*

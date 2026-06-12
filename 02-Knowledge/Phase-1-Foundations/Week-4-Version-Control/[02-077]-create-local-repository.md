# Tạo local repository (Create a Local Repository)

## Document Information
- **Doc ID:** 02-077
- **Summary:** Khởi tạo repo local rỗng — Sourcetree hoặc `git init`; project directory, `.git/`, trạng thái ban đầu trống.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

`git init` (hoặc Sourcetree **Create Local Repository**) tạo **project directory** có working tree + `.git/` (staging + local repo). Ban đầu **chưa có commit**, working tree và staging **trống**. *(Các vùng: [02-076]; cài đặt: [02-075].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Trạng thái ngay sau khi tạo repo

```text
PROJECT DIRECTORY (vd. ~/repos/myproj/ hoặc repoa/)
│
├── (working tree)     ← trống — chưa file hoặc file chưa được Git track
│
└── .git/              ← ẩn; tạo bởi git init / Sourcetree
     ├── staging area  ← trống
     └── local repo    ← 0 commit
```

| Thành phần | Ngay sau `git init` |
|------------|---------------------|
| Working tree | Rỗng (hoặc có file nhưng **untracked**) |
| Staging area | Rỗng |
| Local repository | **Không** có commit nào |
| Remote | **Chưa** có — tạo/push ở bài sau |

### Luồng tạo repo (CLI — khóa dùng ví dụ)

```text
~/home
  └── mkdir repos          # một chỗ gom mọi project (khuyến nghị)
        └── mkdir myproj   # project directory
              └── cd myproj && git init
                    → Git tạo .git/ trong myproj/
```

**Sourcetree:** **New** → **Create Local Repository** → chọn **destination path** (trong `repos/`) + **tên** (thường trùng tên thư mục, vd. `repoa`).

---

## Block 2 — Lệnh & thao tác (Application)

**Prerequisite:** Đã cấu hình Git / Sourcetree ([02-075]). Hiểu working tree, staging, `.git/` ([02-076]).

---

### Path A — Command line

```bash
cd ~                           # hoặc thư mục home của bạn
mkdir -p repos
cd repos

mkdir myproj                   # tên project directory
cd myproj

git init
# Initialized empty Git repository in .../myproj/.git/

ls -la                         # thấy .git/ (flag -a = hidden)
```

| Lệnh / thao tác | Kết quả |
|-----------------|---------|
| `mkdir repos` | Thư mục gốc cho mọi local repo |
| `mkdir <project>` + `cd` | **Project directory** |
| `git init` | Tạo `.git/`; message *empty Git repository* |
| `ls -la` | Xác nhận `.git/` tồn tại |

**Sau `git init`:** Có thể thêm file vào working tree (vd. `sampleprojectfile.txt`) — file đó **chưa** được Git quản lý cho đến khi `git add` (bài Commit).

```bash
echo "hello" > sampleprojectfile.txt
git status                     # untracked
```

---

### Path B — Sourcetree

| Bước | Thao tác |
|------|----------|
| 1 | **New** → **Create Local Repository** |
| 2 | **Destination path** — trong thư mục `repos` đã cấu hình mặc định ([02-075]) |
| 3 | **Name** — thường khớp tên thư mục (gõ path có thể auto-fill name), vd. `repoa` |
| 4 | **Create** |

**Kiểm tra trên disk:** Mở `~/repos/repoa/` → bật hiển thị file ẩn → thấy `.git/`. Có thể thêm file mẫu vào working tree để chuẩn bị lab commit.

*Tương đương CLI: `mkdir ~/repos/repoa && cd ~/repos/repoa && git init`*

---

### Lab khóa (cả hai path)

1. Tạo local repository (tên tùy chọn: `myproj`, `repoa`, …) trong `repos/`
2. Xác nhận thư mục **`.git/`** tồn tại
3. *(Tùy chọn)* Thêm một file mẫu vào working tree — chưa commit

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Repo đầu tiên cho script vận hành

**Bối cảnh:** Trước khi push lên Bitbucket/GitHub, cần repo local cho thư mục `backup-scripts/`.

| Bước | CLI |
|------|-----|
| 1 | `cd ~/repos && mkdir backup-scripts && cd backup-scripts` |
| 2 | `git init` |
| 3 | Tạo `backup.sh`, `.gitignore` (`.env`, `*.log`) |
| 4 | `git status` — file **untracked**; chưa commit → chưa có lịch sử để push |

**DevOps:** Mỗi service/tool (Terraform module, Ansible role, Helm chart) thường = **một project directory** + một `.git/`. Giữ dưới `~/repos/` tránh lẫn với repo khác.

**Không nhầm:** `git init` trong thư mục **đã có** `.git/` → Git cảnh báo re-init; `git init` trong thư mục cha nhầm → nested repo khó quản lý.

---

*Nguồn học: **(Sourcetree) Create a Local Repository** và **(Command Line) Create a Local Repository**, Module 1 — Version Control with Git.*

# Cài đặt Git — Sourcetree & Command line (Installation and Getting Started)

## Document Information
- **Doc ID:** 02-075
- **Summary:** Cài Git CLI hoặc Sourcetree; cú pháp lệnh, help, `git config` (system/global/local); editor; thư mục repos — Module 1.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Khóa có **hai path** cài đặt ban đầu — chọn **một** phần hands-on, đọc phần còn lại để biết tương đương. **Command line** khuyến nghị cho DevOps (CI, SSH server). *(Chọn path: [02-074].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Block 2 — CLI (bỏ qua § Sourcetree) |
| **Sourcetree** | § Block 2 — Sourcetree (bỏ qua § CLI) |

---

## Block 1 — Mô hình (Structure)

### Hai path, cùng mục tiêu ban đầu

```text
Module 1 — Installation and Getting Started
    │
    ├── CLI          git --version → git config (identity, editor) → ~/repos/
    │
    └── Sourcetree   cài app → Accounts (name, email) → default repos folder
```

| Thành phần | Vai trò chung |
|------------|----------------|
| **Git binary** (CLI) hoặc **Sourcetree** | Công cụ thao tác repository |
| **user.name / user.email** | Metadata **mọi commit** — phải đúng |
| **~/repos/** (hoặc tương đương) | Gom local repository trên máy cá nhân |

**Directory = folder** — khóa dùng hai từ thay nhau.

### Cấu trúc một lệnh Git (CLI)

```text
git  <command>  [-flags|--flags]  [arguments...]
 │       │              │                │
 │       │              │                └── ví dụ tên file (git add README.md)
 │       │              └── -s, --short, -h, …
 │       └── status, init, add, commit, config, …
 └── luôn bắt đầu bằng "git"
```

Ví dụ: `git status -s` — lệnh `status`, flag ngắn `-s` (short / critical status only).

### Thứ tự ưu tiên `git config`

```text
local (trong repo hiện tại)  →  thắng
        ↓ nếu không set
global (user hiện tại)       →  thắng
        ↓ nếu không set
system (mọi user trên máy)   →  thắng
```

Dùng **global** cho name/email mặc định; **local** khi một repo cần email khác (ví dụ cá nhân vs công ty).

### Đọc cú pháp trong `git help` (CLI)

| Ký hiệu | Ý nghĩa |
|---------|---------|
| `-f` hoặc `--flag` | `\|` = hoặc — chọn một dạng flag |
| `[ ... ]` | Tùy chọn |
| `<placeholder>` | Thay bằng giá trị thật |
| `[<placeholder>]` | Placeholder tùy chọn |
| `( ... )` | Nhóm / làm rõ |
| `--` | Tách flag khỏi path (path có thể nhầm với option) |
| `...` | Có thể lặp nhiều lần (vd. nhiều path) |

*Flags còn gọi là **options** hoặc **switches**.*

---

## Block 2 — Lệnh & thao tác (Application)

**Prerequisite CLI:** Terminal đã quen cơ bản (Week 1–3). Cài Git: [git-scm.com/downloads](https://git-scm.com/downloads) nếu chưa có.

**Prerequisite Sourcetree:** [sourcetreeapp.com](https://www.sourcetreeapp.com/). GUI khác: [git-scm.com/downloads/guis](https://git-scm.com/downloads/guis).

---

### Path A — Command line (chính cho DevOps)

#### Kiểm tra / cài đặt

```bash
git --version
# Có dòng version → đã cài. Không có → cài theo git-scm.com rồi chạy lại.
```

CLI = command line interface = terminal (dùng lẫn trong khóa).

#### Help

```bash
git help                  # trợ giúp tổng quan
git help <command>        # chi tiết lệnh (vd. git help status)
git <command> -h          # help ngắn (vd. git init -h)
```

Tài liệu online: [git-scm.com/docs](https://git-scm.com/docs) — cùng nội dung với `git help`.

#### Cấu hình danh tính & editor

```bash
# Global — áp dụng mọi repo của user này (thường dùng lần đầu)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Đọc giá trị (trong repo: local ưu tiên hơn global)
git config user.name
git config user.email
git config --global --list

# Local — chỉ repo hiện tại (cd vào repo trước)
git config user.email "personal@example.com"

# Editor khi Git mở editor (vd. commit không -m)
git config --global core.editor "nano"
# macOS có thể: git config --global core.editor "vim"
```

| Flag `--` | Phạm vi |
|-----------|---------|
| `--system` | Mọi user trên máy |
| `--global` | Mọi repo của **bạn** |
| `--local` hoặc không flag (trong repo) | Chỉ **repo hiện tại** |

#### Thư mục làm việc

```bash
mkdir -p ~/repos && cd ~/repos
# Git không có "default folder" built-in — tự quy ước ~/repos trước init/clone
```

#### Lab khóa (CLI path)

1. Cài Git (nếu cần) → `git --version`
2. `git help`, `git init -h` — làm quen help và ký hiệu cú pháp
3. `git config --global user.name` / `user.email` / `core.editor`
4. Tạo `~/repos` làm nơi chứa repo sau này

---

### Path B — Sourcetree (GUI — tùy chọn)

*Bỏ qua nếu chỉ học CLI.*

| Bước | Sourcetree |
|------|------------|
| Cài đặt | Tải installer Windows/macOS → cài theo wizard |
| Danh tính | ⚙️ **Accounts** → **General** → name + email |
| Thư mục repos | Tạo `~/repos` → **General** → **Miscellaneous** → default folder |

*Tương đương CLI: xem các lệnh `git config` và `mkdir ~/repos` ở Path A.*

#### Lab khóa (Sourcetree path)

1. Cài Sourcetree
2. Cấu hình name + email trong Accounts
3. Chỉ định default folder `repos`

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Chuẩn bị máy dev và đồng bộ với CI

**Bối cảnh:** Laptop cá nhân + runner CI đều cần Git; commit phải trace được tác giả.

| Bước | CLI (máy dev) | CI / server |
|------|----------------|-------------|
| 1 | `git --version` xác nhận Git | Image Docker/`ubuntu-latest` thường đã có `git` |
| 2 | `git config --global user.email` = **email công ty** | `git config user.email` trong job hoặc env `GIT_AUTHOR_EMAIL` |
| 3 | Làm việc trong `~/repos/project-x/` | `git clone` vào workspace pipeline |
| 4 | `git help commit` khi quên flag | Script chỉ gọi lệnh đã test — không cần GUI |

**Editor:** Trên server headless thường set `GIT_EDITOR=true` hoặc luôn `git commit -m "..."` để tránh mở `nano`/`vim` trong CI.

**Sourcetree:** Hữu ích xem graph local; **không** cài trên Jenkins/GitHub Actions — pipeline luôn **CLI**.

---

*Nguồn học: **(Command Line) Installation and Getting Started** và **(Sourcetree) Installation and Getting Started**, Module 1 — Version Control with Git (Atlassian / Coursera).*

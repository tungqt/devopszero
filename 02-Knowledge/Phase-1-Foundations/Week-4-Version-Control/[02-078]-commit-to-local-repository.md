# Commit vào local repository (Commit to a Local Repository)

## Document Information
- **Doc ID:** 02-078
- **Summary:** `status` → `add` → `commit` → `log`; trạng thái file (untracked, staged, modified); Sourcetree File status / History — Module 1.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Luồng cốt lõi: xem trạng thái → **stage** → **commit** (snapshot + message) → xem **history**. Hai path cùng mục đích — khác công cụ. *(Vùng Git: [02-076]; `git init`: [02-077]; config: [02-075].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Vòng đời thay đổi → commit

```text
Working tree          Staging area           Local repository
     │                     │                        │
     │  untracked/new     │                        │
     ├──── git add ───────►│  staged                │
     │                     │                        │
     │  sửa sau khi add    │  (bản cũ vẫn staged)   │
     │  = modified         │                        │
     ├──── git add lại ───►│  staged (bản mới)      │
     │                     │                        │
     │                     ├── git commit ─────────►│  commit mới
     │                     │   (+ snapshot cũ)    │  (full project)
     ▼                     ▼                        ▼
  clean sau commit    clean                  git log xem history
```

### Trạng thái file (CLI `git status -s`)

| Ký hiệu (short) | Ý nghĩa |
|-----------------|---------|
| `??` | **Untracked** — chỉ trong working tree, Git chưa quản lý |
| `A` (cột staged) | **Added to stage** — sẽ vào commit tiếp theo |
| `M` (working tree) | **Modified** — đã stage/commit trước đó, rồi sửa thêm |

**Hai trạng thái cùng lúc:** Stage bản cũ (`A`) + sửa file trên disk (`M`) → cần `git add` lại để đưa bản mới vào stage.

```text
fileA.txt:  add → stage v1
            sửa trên disk →  staged: v1  |  working: v2 (modified)
            add lại       →  staged: v2 only
```

### Commit là gì?

- **Snapshot toàn bộ project** tại thời điểm commit (file mới trên stage **cộng** với nội dung từ commit trước).
- File đã từng commit thường **vẫn nằm trong staging** và trong các commit sau, trừ khi bạn **gỡ** khỏi project (học sau).
- **Commit message** bắt buộc, có ý nghĩa — là một phần **lịch sử** (audit, blame).
- Không có gì để commit → `git commit` **thất bại** (working tree + staging “clean” hoặc chưa stage).

### Sourcetree ↔ vùng Git

| Sourcetree | Tương đương |
|------------|-------------|
| **File status** tab | Working tree + staging |
| `?` untracked | Untracked |
| `+` staged | Staged |
| `...` modified | Modified (sau khi đã stage/commit) |
| **Commit** + message | `git commit` |
| **History** tab | `git log` |

---

## Block 2 — Lệnh & thao tác (Application)

**Prerequisite:** Local repo đã `git init` ([02-077]).

---

### Path A — Command line

#### Xem trạng thái

```bash
git status
# Dòng 1: On branch master  (nhánh mặc định — nhiều repo mới dùng main)
# Clean: nothing to commit, working tree clean

git status -s              # short format: ??  A  M  ...
```

Tạo `fileA.txt` → `git status` → **untracked**; Git gợi ý `git add`.

*Nội dung có sẵn trước `git init` cũng hiện **untracked** như file mới tạo.*

#### Stage (`git add`)

```bash
git add fileA.txt          # một file
git add dirA/              # cả thư mục + file bên trong
git add .                  # mọi untracked/modified trong thư mục hiện tại — cẩn thận
```

| Lệnh | Tác dụng |
|------|----------|
| `git add <file>` | Đưa file vào staging |
| `git add <dir>/` | Stage cả cây thư mục |
| `git add .` | Stage hàng loạt — dễ **commit nhầm** file (.env, build artifact) |

**Ví dụ `git status -s`:**

```bash
# Untracked
?? fileA.txt

# Sau git add
A  fileA.txt

# Sửa file sau khi add — staged v1 + modified v2
AM fileA.txt    # hoặc hiển thị tách: A ở cột staged, M ở working

git add fileA.txt   # stage bản mới nhất
```

#### Commit

```bash
git commit -m "Initial commit"

# Không -m → mở editor (core.editor, vd. nano) — tiện message nhiều dòng
git commit
```

Sau commit thành công: `git status` → **working tree clean**.

#### Lịch sử

```bash
git log
git log --oneline
git log -2                 # 2 commit gần nhất
git log --oneline -3
```

| Lệnh | Vai trò |
|------|---------|
| `git status` | Working tree + staging |
| `git add` | Untracked/modified → staging |
| `git commit` | Staging → commit (snapshot project) |
| `git log` | Lịch sử local repository |

#### Cheat sheet một commit đầu tiên

```bash
cd ~/repos/myproj
echo "content" > fileA.txt
git status
git add fileA.txt
git status
git commit -m "Add fileA.txt"
git log --oneline
```

---

### Path B — Sourcetree

| Bước | Thao tác |
|------|----------|
| Xem trạng thái | Tab **File status** — unstaged / staged panes |
| Untracked | Icon `?`; file mới trong working tree |
| Stage một file | Chọn file → `...` → **Stage file**; icon `+` |
| Stage tất cả | Checkbox **unstaged files** (cả thư mục) |
| Sửa file đã stage | Icon modified (`...`) → **stage lại** nếu muốn vào commit |
| Commit | **Commit** → nhập **message** có ý nghĩa → **Commit** |
| History | Tab **History** — commit, message, chi tiết khung dưới |

*Tương đương CLI: bảng lệnh Path A.*

---

### Lab khóa

1. Tạo file → xem untracked  
2. Stage → commit với message rõ (vd. `Initial commit`)  
3. `git log` / History — xác nhận 1 commit  
4. *(Nâng cao)* Stage → sửa file → `status -s` → `add` lại → commit thứ hai  

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Commit script deploy — tránh `git add .` bừa

**Bối cảnh:** Repo `deploy-scripts/` có `deploy.sh`, `.env.example`, thư mục `tmp/`.

| Bước | Thực hành tốt |
|------|----------------|
| 1 | `.gitignore` có `.env`, `tmp/`, `*.log` **trước** khi add hàng loạt |
| 2 | `git add deploy.sh .gitignore` — **chọn file**, không `git add .` trên root lúc đầu |
| 3 | `git commit -m "Add deploy script and gitignore"` |
| 4 | CI sau này đọc message/log — message rõ giúp trace incident |

**Sau commit:** Có thể thử sửa script; mọi thay đổi an toàn vì đã có snapshot để quay lại (`git log`, reset/revert — học sau).

**Sourcetree vs CI:** Pipeline chỉ chạy `git` CLI — thói quen stage/commit rõ ràng trên terminal giúp debug khi job fail vì “nothing to commit” hoặc “untracked files”.

---

*Nguồn học: **(Sourcetree) Commit to a Local Repository** và **(Command Line) Commit to a Local Repository**, Module 1 — Version Control with Git.*

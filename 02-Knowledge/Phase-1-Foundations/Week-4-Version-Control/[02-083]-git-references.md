# Git references (nhãn trỏ tới commit)

## Document Information
- **Doc ID:** 02-083
- **Summary:** Cách Git đặt tên dễ nhớ cho commit qua nhánh, HEAD và tag; tham chiếu commit cũ bằng ký hiệu ~ và ^; đẩy tag lên remote — Module 2.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Reference** là tên thân thiện trỏ tới **Git ID** (SHA-1) hoặc reference khác (**symbolic ref**). Dùng `master`, `HEAD`, tag thay vì gõ hash — cùng một commit có thể gọi bằng nhiều tên. *(Git ID: [02-082]; graph: [02-081].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Reference và symbolic reference

```text
  SHA-1 (commit C)  ◄──  master (branch label — tip)
         ▲                    ▲
         │                    │
         └──── HEAD ──────────┘

  HEAD → master → commit C   (symbolic rồi trỏ SHA-1)
```

| Khái niệm | Định nghĩa |
|-----------|------------|
| **Reference** | Tên dễ đọc → SHA-1 **hoặc** → ref khác |
| **Symbolic reference** | Ref trỏ ref khác (HEAD file: `ref: refs/heads/master`) |
| **Tip** | Commit mới nhất trên nhánh — branch label trỏ đây |

Cùng commit — `git show` nhận: full hash, prefix hash, `master`, hoặc `HEAD`.

### Branch vs branch label

```text
  A ───── B ───── C
                  ▲
                  └── master (label chỉ ở tip)

  A, B, C đều thuộc nhánh master; label chỉ tại C
```

`git status` → *On branch master* — commit mới sẽ vào nhánh này.

### Lưu trữ ref trong `.git/` (học — không sửa tay)

```text
.git/
├── HEAD                 # symbolic: ref: refs/heads/master
└── refs/
    ├── heads/
    │   └── master       # file text: 1 dòng SHA-1 (tip)
    └── tags/
        └── v0.1         # lightweight tag
```

Git có thể **pack** refs — vị trí đổi; **không** chỉnh `.git/` trực tiếp khi vận hành.

### HEAD

Một HEAD / local repo; thường trỏ branch label đang checkout.

### Tham chiếu commit trước: `~` và `^`

```text
  Linear:     HEAD~1  = cha    HEAD~3  = 3 commit trước
              HEAD~   = HEAD~1

  Merge:      HEAD^   = parent 1 (như ~ trên nhánh thường)
              HEAD^2  = parent 2 (chỉ merge commit)
              HEAD^^  = ông (parent của parent) — khác HEAD^2
```

| Cú pháp | Ý nghĩa |
|---------|---------|
| `HEAD~` / `HEAD~1` | Cha trực tiếp |
| `master~3` | 3 commit trước tip `master` |
| `483d~~~` | 3 commit trước commit `483d...` |
| `HEAD^` | Parent thứ nhất |
| `HEAD^2` | Parent thứ hai *(lỗi nếu không phải merge)* |
| `HEAD^^` | `HEAD~2` trên đường thẳng |
| `HEAD~^2` | Cha, rồi parent thứ 2 của cha — graph phức tạp |

Có thể **kết hợp** tùy ý để trỏ commit bất kỳ trong lịch sử.

### Tag vs branch label

| | Branch label | Tag |
|---|--------------|-----|
| Di chuyển | Có — theo commit mới | Không — cố định một commit |
| Lightweight | Giống ref file đơn giản | `git tag v1.0` |
| Annotated | — | Git object + metadata, GPG, message — **khuyến nghị** |

---

## Block 2 — Thao tác (Application)

**Prerequisite:** Repo có vài commit ([02-078]).

---

### Path A — Command line

#### Xem ref và dùng thay SHA-1

```bash
git status                          # On branch master
git log --oneline -1                # HEAD, master trên commit hiện tại

git show HEAD
git show master
git show 483d                       # prefix — nếu unique
# Cùng commit nếu trỏ cùng SHA-1
```

#### `~` và `^`

```bash
git log --oneline --graph

git show HEAD                       # commit hiện tại (vd. e0...)
git show HEAD~                     # cha (vd. 1e...)
git show master~3                 # 3 commit trước tip master (vd. 14...)
git show 483d~~~                  # tương tự với partial SHA

git show master^                  # parent 1 — giống ~ trên nhánh tuyến tính
git show HEAD^2                   # lỗi nếu không phải merge commit
git show HEAD^^                   # = HEAD~2 (vd. e8...)
```

#### Tag

```bash
git tag                             # liệt kê

# Lightweight
git tag v1.0                        # tag commit HEAD trỏ tới
git tag v0.1 HEAD^                 # tag commit cha

# Annotated (khuyến nghị)
git tag -a v2.0 -m "includes feature 2"
git tag -a v2.0 -F message.txt      # message từ file
git tag -a v2.0                     # mở editor nhập message

git show v0.1                       # lightweight → thông tin commit
git show v2.0                       # annotated → tag object + commit
```

| Lệnh | Vai trò |
|------|---------|
| `git tag <name>` | Lightweight tag (HEAD mặc định) |
| `git tag <name> <commit>` | Tag commit chỉ định |
| `git tag -a -m "..."` | Annotated tag |
| `git push origin <tag>` | Push **một** tag |
| `git push origin --tags` | Push **mọi** tag |

`git push` thường **không** đẩy tag — phải push tag riêng. Kiểm tra trên Bitbucket sau push.

#### Xem ref trong `.git/` *(chỉ học)*

```bash
cat .git/HEAD                       # ref: refs/heads/master
cat .git/refs/heads/master          # SHA-1 tip
ls .git/refs/heads/
ls .git/refs/tags/
```

---

### Path B — Sourcetree

#### Đọc HEAD và master

- **History** — commit mới nhất: **HEAD**, **master**.
- Chi tiết commit: HEAD → master → SHA-1.

#### Tạo tag

| Bước | Thao tác |
|------|----------|
| 1 | Right-click commit → **Tag** |
| 2 | **Commit** hoặc **Working copy parent** |
| 3 | Tên (vd. `v0.1`) → **Add** |

**Advanced:** Move existing tag; **annotated** (mặc định, khuyến nghị); lightweight (không khuyến nghị); GPG sign; tag message; **Push tag** khi tạo.

#### Push tag

**Push** thường không gửi tag → bật **Push tag** lúc tạo, hoặc **Tags** → right-click → **Push to** → `origin`.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Release tag + trace commit cha

**Bối cảnh:** Hotfix cần so sánh release `v1.0` với commit cha trên `master`.

```bash
git show v1.0
git show v1.0~1                   # commit trước release
git tag -a v1.1 -m "Hotfix CVE-xxx"
git push origin v1.1
```

**Rollback deploy:** Checkout tag cũ — tag không di chuyển khi `master` tiến thêm.

**Pipeline:** Trigger trên `git push origin v1.1` — đảm bảo tag đã push, không chỉ push branch.

### Câu hỏi tự kiểm

1. Nội dung file `.git/refs/heads/master`?
2. `HEAD^2` trên commit thường (không merge) — kết quả?
3. `git push` có đẩy tag `v2.0` không?

---

*Nguồn học: **(Command Line) Git References** và **(Sourcetree) Git References**, Module 2 — Version Control with Git.*

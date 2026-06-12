# Git ID (tên định danh object)

## Document Information
- **Doc ID:** 02-082
- **Summary:** Mỗi commit có mã hash duy nhất từ nội dung; bốn loại object Git lưu bên trong; xem và rút gọn ID trên terminal hoặc Sourcetree — Module 2.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Mỗi commit (và object khác) có **tên** là chuỗi hex 40 ký tự — **Git ID** / **hash**. ID gắn chặt **nội dung**: đổi một ký tự → hash đổi hẳn. *(Graph commit: [02-081].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Bốn loại Git object (object store trong `.git/`)

```text
                    COMMIT object (file text)
                         │
         ┌───────────────┼───────────────┐
    user, message    parent(s)      root TREE
                         │               │
                         │               ▼
                         │         TREE → tên file/thư mục
                         │               ▼
                         │         BLOB → nội dung file
         └───────────────┴──► Git ID (SHA-1) = tên mỗi object
```

| Object | Lưu gì | Tương tác thường ngày |
|--------|--------|------------------------|
| **Commit** | Author, message, cha, con trỏ **tree** gốc | Có — mỗi commit |
| **Annotated tag** | Tham chiếu **một commit** | Release, đánh dấu version |
| **Tree** | Danh sách file/thư mục trong một directory | Git tự quản |
| **Blob** | Nội dung file được track | Git tự quản |

Commit + tree → Git **tái dựng** full snapshot. **Object store** — bạn hiếm khi đụng trực tiếp.

### Git ID = SHA-1 (40 ký tự hex)

| Tên khác | Cùng nghĩa |
|----------|------------|
| Object ID, SHA-1, hash, checksum | Git ID |

```text
  Nội dung  ──►  SHA-1  ──►  40 ký tự (0-9, a-f)
                │
                ├── Cùng nội dung → cùng ID (luôn)
                └── Đổi nhỏ → ID đổi mạnh (avalanche)
```

| Ví dụ (khóa) | SHA-1 bắt đầu |
|--------------|---------------|
| `hi` | `45b9...` |
| `hi` + space cuối | `0b5d...` (khác hẳn) |

### Rút gọn ID

| Hiển thị | Khi nào |
|----------|---------|
| **40 ký tự** | `git log` (full) |
| **~7 ký tự** | `git log --oneline`, Sourcetree list |
| **≥4 ký tự** | Gõ trong lệnh (`git show abcd`) — Git tìm object **duy nhất** khớp prefix; nếu trùng → gõ thêm |

---

## Block 2 — Xem & thử Git ID (Application)

**Prerequisite:** Repo có ít nhất một commit ([02-078]).

---

### Path A — Command line

#### Xem ID commit

```bash
git log                      # hash 40 ký tự + chi tiết
git log --oneline            # ~7 ký tự đầu + message

git rev-parse HEAD           # ID đầy đủ commit hiện tại
git rev-parse --short HEAD   # ID rút
```

7 ký tự đầu của `--oneline` **khớp** 7 ký tự đầu của hash đầy đủ cùng commit.

#### Tham chiếu ID rút trong lệnh

```bash
git show a1b2                # ≥4 ký tự — nếu unique, Git hiểu đúng object
git show a1b2c3d4e5f6...     # full ID khi cần tránh nhầm
```

#### Demo SHA-1: `git hash-object` (plumbing)

Lệnh **tầng thấp** — Git dùng nội bộ; hữu ích hiểu cơ chế, ít dùng hàng ngày.

```bash
echo -n hi > fileA.txt
git hash-object fileA.txt
# → hash blob của "hi" (luôn cùng giá trị nếu nội dung giống)

git hash-object fileA.txt    # chạy lại → cùng hash

echo -n "hi " > fileA.txt    # thêm space cuối
git hash-object fileA.txt
# → hash khác hẳn (avalanche)
```

| Loại lệnh | Ví dụ | Dùng khi |
|-----------|-------|----------|
| **Porcelain** (cao) | `git log`, `git commit`, `git status` | Làm việc hàng ngày |
| **Plumbing** (thấp) | `git hash-object`, `cat-file` | Script, debug, hiểu Git |

---

### Path B — Sourcetree

| Nơi | Nội dung |
|-----|----------|
| **History** → click commit | Chi tiết: **40 ký tự** Git ID |
| Danh sách commit | **7**, **10** hoặc **40** ký tự tùy view |

Chuỗi đó = tên commit object trong object store — copy khi cần, không cần nhớ thuộc lòng.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Pipeline in SHA — trace và `git show`

**Bối cảnh:** CI log `Building commit 45b9a1c`; cần xem commit đó chứa gì.

```bash
git show 45b9a1c              # prefix đủ unique
git log --oneline | grep 45b9
```

**Deploy cố định:** Tag/release trỏ tới **một Git ID** — cùng ID trên mọi clone = cùng nội dung.

**Lưu ý:** Sửa message/nội dung đã commit → **ID mới** (object mới). Đừng rewrite history đã push (Module 3).

### Câu hỏi tự kiểm

1. Commit object cần **tree** để làm gì?
2. `git hash-object` trên cùng file hai lần — hash có đổi không?
3. Khi nào cần gõ >4 ký tự của ID?

---

*Nguồn học: **(Command Line) Git IDs** và **(Sourcetree) Git IDs**, Module 2 — Version Control with Git.*

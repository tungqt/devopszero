# Git merge (gộp nhánh)

## Document Information
- **Doc ID:** 02-085
- **Summary:** Cách gộp nhánh feature vào nhánh gốc; fast-forward khi nhánh gốc không đổi, merge commit khi có commit song song; tùy chọn luôn tạo merge commit — Module 2.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Merge** gộp công việc từ hai nhánh độc lập — thường **topic branch** (vd. `feature1`) vào **base branch** (vd. `master`/`main`). *(Nhánh: [02-084]; graph: [02-081].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

**Thuật ngữ:** **tip** = **đỉnh nhánh** / commit mới nhất trên nhánh.

---

## Block 1 — Mô hình (Structure)

### Merge là gì?

```text
  Trước merge:
    master:   A ─── E          (không biết feature1)
    feature1: A ─ B ─ C

  Sau merge commit M:
    master:   A ─── E ─── M
              \         /
    feature1:  B ─ C ──┘
    (M có 2 cha: C và E)
```

| Khái niệm | Ý nghĩa |
|-----------|---------|
| **Base branch** | Nhánh dài hạn — `master`, `main`, `develop` |
| **Topic branch** | Nhánh ngắn — feature, bugfix |
| **Merge commit** | Commit **M** gộp hai nhánh; thường **≥2 parent** |

Một commit (vd. **B**) có thể **thuộc nhiều nhánh** — khi trace từ **M** về gốc vẫn đi qua **B**.

### Bốn kiểu merge (khóa)

| Kiểu | Video này | Học sau |
|------|-----------|---------|
| **Fast-forward (FF)** | ✓ | |
| **Merge commit** | ✓ | |
| **Squash merge** | | Module sau |
| **Rebase** | | Module sau |

### Fast-forward merge

```text
  Trước:
    master:   A
    feature1: A ─ B ─ C

  Sau FF (chỉ DI CHUYỂN label master):
    master:   A ─ B ─ C   ← label master nhảy tới C
    feature1: A ─ B ─ C   (cùng commit, không đổi nội dung commit)
```

| Điều kiện FF | Giải thích |
|--------------|------------|
| **Được phép** | **Không** có commit mới trên base (ví dụ `master`) kể từ khi tách nhánh (branch) để phát triển feature. |
| **Không FF** | Nếu cả nhánh đang phát triển (ví dụ `feature1`) và base (`master`) đều có commit mới kể từ lúc tách nhánh, sẽ không thể fast-forward vì như vậy sẽ “nhảy cóc” qua phần việc của người khác. |

- Lịch sử sau FF: **tuyến tính** — không có commit nhiều parent.
- Xong FF: có thể **xóa label** topic branch.

### Merge commit (không fast-forward)

```text
  master:   A ─ D ─ E
  feature1: A ─ B ─ C
              \     \
               └── M ─┘   M = merge commit (cha: C và E)
```

| | Fast-forward | Merge commit |
|---|--------------|--------------|
| Graph | Thẳng | **Nhánh hình chữ Y** — dễ thấy branch/merge |
| Parents của commit mới | Không tạo commit mới* | **2+ parents** |
| Khi dùng | Base đứng yên từ lúc tách topic | Base đã có commit mới song song |

*\*FF chỉ di chuyển label, không tạo commit merge.*

**Merge conflict:** hai nhánh sửa cùng chỗ khác nhau — xử lý ở bài sau; giả sử merge thành công ở đây.

### No fast-forward (ép merge commit)

Dù merge **có thể** FF, team vẫn muốn **luôn có merge commit** để graph thấy rõ feature branch.

- File project ở **M** có thể **giống hệt** đỉnh nhánh feature (nếu base không đổi) — khác biệt là **lịch sử / graph**, không phải nội dung file.
- CLI: `git merge --no-ff`; Sourcetree: **Create a commit even if merge resolved via fast-forward**.

### Merge nhánh dài hạn

`develop` → `master` khi release: thường **dễ** (thường FF hoặc merge commit đơn giản) vì `master` đứng yên trong khi `develop` tiến.

---

## Block 2 — Thao tác (Application)

**Prerequisite:** Có `master` và `feature1` với commit trên cả hai ([02-084]).

---

### Path A — Command line

#### Quy trình chung

```bash
git checkout master              # 1. đứng trên base branch
git merge feature2               # 2. gộp topic (Git ưu tiên FF)
git branch -d feature2           # 3. xóa label topic (nếu policy cho phép)
```

| Lệnh | Vai trò |
|------|---------|
| `git merge <topic>` | Gộp `<topic>` vào nhánh hiện tại |
| `git merge --no-ff <topic>` | **Luôn** tạo merge commit |
| `git branch -d <topic>` | Xóa nhánh đã merge an toàn |

Git **mặc định thử fast-forward** trước; không FF được thì tự tạo **merge commit**.

#### Fast-forward merge (lab khóa: `feature2`)

```bash
git log --oneline --graph
# master ở commit cũ; feature2 ở commit mới nhất

git checkout master
git merge feature2
# Updating ...  Fast-forward

git log --oneline --graph
# commit mới nhất có cả master và feature2

git branch -d feature2
```

| Dấu hiệu FF | Ý nghĩa |
|-------------|---------|
| Message `Fast-forward` | Chỉ **dời label** base — không commit merge mới |
| Graph thẳng | Không commit nhiều parent |

#### Merge commit (không FF)

Khi base có commit mới song song (vd. **D**, **E** trong lúc làm `featureX`):

```bash
git checkout master
git merge featureX
# Mở editor — message mặc định: "Merge branch 'featureX'"
# Lưu & đóng editor → tạo merge commit M
```

| | |
|---|---|
| **Merge commit M** | Có **≥2 parent** (vd. đỉnh `featureX` + đỉnh `master`) |
| **Graph** | Nhánh chữ Y — dễ thấy lịch sử merge |
| **Conflict** | Có thể xảy ra — bài sau |

Git thường dùng chiến lược **recursive** (hiển thị khi merge).

#### No fast-forward (`--no-ff`)

Dù **có thể** FF, vẫn ép merge commit (policy team):

```bash
git checkout master
git merge --no-ff feature2
# Editor: "Merge branch 'feature2'" — chấp nhận hoặc sửa message

git log --oneline --graph
# Commit merge có 2 parent; graph không tuyến tính
```

| FF vs `--no-ff` (base không đổi) | |
|----------------------------------|---|
| **File trong project** | Có thể **giống nhau** |
| **Lịch sử / graph** | FF thẳng; no-ff có **điểm merge** rõ |

#### Xem kết quả & dọn nhánh

```bash
git log --oneline --graph --decorate
git branch -d feature2           # từ chối nếu chưa merge — dùng -D chỉ khi chắc bỏ work
```

Giữ “dấu vết” feature: message commit hoặc **tag** thay vì giữ label nhánh.

*Tương đương Sourcetree: checkout base → Merge → chọn đỉnh topic → OK.*

---

### Path B — Sourcetree

#### Quy trình chung

| Bước | Thao tác |
|------|----------|
| 1 | **Checkout base** — double-click `master` (hoặc `main`) |
| 2 | **Merge** |
| 3 | Chọn commit cần gộp — thường **đỉnh nhánh** topic (tip `feature1`) |
| 4 | **OK** |
| 5 | *(Tùy policy)* **Branches** → chọn topic → **Delete Branches** |

#### Fast-forward (ví dụ khóa)

- Ban đầu: `master` có 1 commit; `feature1` có 2 commit thêm; đang checkout feature.
- Checkout `master` → **Merge** → chọn tip `feature1` → **OK**.
- Label `master` nhảy tới commit mới nhất của feature — graph thẳng.
- Xóa label `feature1` nếu không cần giữ tên nhánh.

#### Merge commit (base đã có commit mới)

- Trong lúc làm `feature1`, `master` thêm `fileB.txt`, `feature2`, …
- Checkout `master` → **Merge** → tip `feature1` → **OK**.
- Git tự **merge commit** vì không FF.
- Graph **không tuyến tính** — thấy nhánh và điểm gộp.
- Right-click nhánh dưới **Branches** → **Delete** (hoặc tab Branches).

#### No fast-forward (policy team)

Trong hộp thoại **Merge**, bật:

**Create a commit even if merge resolved via fast-forward**

→ Luôn có merge commit; graph nhánh rõ dù base không đổi.

#### Sau merge — có nên xóa topic branch?

| Lựa chọn | Lý do |
|----------|--------|
| **Xóa label** | Tránh quá nhiều nhánh đã merge — đỡ rối |
| **Giữ thông tin** | Ghi trong commit message hoặc **tag** đánh dấu feature |

---

## Block 3 — DevOps scenario (Use case)

### Scenario A: Feature vào `main` — FF

**Bối cảnh:** Chỉ bạn làm trên `feature/login`; `main` không commit thêm.

| Bước | Hành động |
|------|-----------|
| 1 | `git checkout main` |
| 2 | `git merge feature/login` → thấy `Fast-forward` |
| 3 | `git branch -d feature/login` |
| 4 | Push `main` — CI chạy trên history tuyến tính |

### Scenario B: Hotfix song song — merge commit

**Bối cảnh:** `main` nhận hotfix trong lúc feature đang dev.

| Bước | Hành động |
|------|-----------|
| 1 | Hoàn thành `feature/payment` |
| 2 | `git checkout main` → `git merge feature/payment` |
| 3 | Sửa merge message trong editor → merge commit (2 parents) |
| 4 | Review graph / PR — thấy rõ nhánh feature |

### Scenario C: Policy “luôn merge commit”

Team bật `git merge --no-ff` mọi feature → graph PR luôn có điểm gộp dù `main` không đổi — dễ audit; đổi lại history không thẳng.

### Câu hỏi tự kiểm

1. Vì sao có commit **E** trên `master` thì không FF được?
2. Merge commit **M** có mấy parent trong ví dụ feature + master?
3. No-ff khác FF về **file** hay chủ yếu về **lịch sử**?

---

*Nguồn học: **(Command Line) Merging** và **(Sourcetree) Merging**, Module 2 — Version Control with Git.*

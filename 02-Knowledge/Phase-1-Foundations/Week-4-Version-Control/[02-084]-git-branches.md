# Git branches (nhánh)

## Document Information
- **Doc ID:** 02-084
- **Summary:** Nhánh giúp tách luồng công việc để thử nghiệm và làm song song an toàn; bài này hướng dẫn tạo nhánh, checkout, detached HEAD, xóa nhánh và khôi phục bằng reflog.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Branch** là tập commit tính từ **tip** của nhánh quay ngược về commit đầu tiên. Nhiều nhánh có thể chia sẻ các commit nền giống nhau. Tạo nhánh rất nhẹ vì thực chất chỉ tạo thêm một reference nhỏ. *(References: [02-083]; graph: [02-081].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Branch là gì?

```text
        C  ← tip featureX
       /
  A ─ B ─ D  ← tip master
      ↑
  A, B thuộc CẢ master và featureX
  C chỉ featureX; D chỉ master
```

| Định nghĩa | Chi tiết |
|------------|----------|
| **Branch** | Tập commit từ tip đi ngược về commit đầu project |
| **Mặc định** | Chưa tạo nhánh thì commit nằm trên `master` |
| **Branch label** | Con trỏ tới tip nhánh (reference nhỏ trong `.git`) |

### Lợi ích nhánh

| Lợi ích | Giải thích |
|---------|------------|
| **Nhanh / nhẹ** | Tạo nhánh = thêm một file reference nhỏ |
| **Thử nghiệm an toàn** | Làm trên `featureX` không chạm `master` cho tới khi merge |
| **Làm việc song song** | Nhiều người, nhiều nhánh, gộp sau |
| **Hỗ trợ nhiều version** | Có thể hotfix cho các line release khác nhau |

### Topic vs long-running

| Loại | Ví dụ | Mục đích |
|------|-------|----------|
| **Topic / feature** | `featureX`, `bugfix/login`, `hotfix` | Ngắn hạn, xong thì merge và thường xóa label |
| **Long-running** | `master`, `develop` | Duy trì lâu dài, có thể suốt đời dự án |

### Checkout làm hai việc

```text
checkout featureX:
  1) HEAD đổi sang featureX
  2) Working tree cập nhật theo commit tip của featureX
```

| Sau khi tạo `featureX` tại tip `master` | HEAD | Commit tiếp theo vào |
|-----------------------------------------|------|----------------------|
| Có checkout nhánh mới | `featureX` | `featureX` |
| Không checkout | vẫn `master` | `master` (dễ commit nhầm) |

### Detached HEAD

```text
Bình thường:  HEAD -> master -> commit
Detached:     HEAD -> commit SHA-1 (không qua branch label)
```

| Trường hợp | Ý nghĩa |
|-----------|---------|
| Checkout commit trực tiếp | Vào detached HEAD |
| Chỉ xem code cũ | An toàn |
| Muốn commit tiếp | Nên tạo branch mới từ commit đó trước |

### Xóa nhánh

- Xóa nhánh thực chất là xóa **branch label**.
- Commit không bị xóa ngay.
- Nếu commit chưa merge chỉ còn nằm trên nhánh đó, Git chặn `-d`.
- Ép `-D` có thể tạo **dangling commit**; dangling commit có thể bị garbage collect sau.

---

## Block 2 — Lệnh & thao tác (Application)

**Prerequisite:** Repo có vài commit ([02-078]) và đã nắm HEAD ([02-083]).

### Path A — Command line

#### Xem nhánh

```bash
git branch
# dấu * là nhánh hiện tại
```

#### Tạo nhánh

```bash
git branch featureX        # tạo label, chưa checkout
git branch                 # vẫn thấy * master
```

#### Checkout nhánh/commit

```bash
git checkout featureX      # sang nhánh featureX
git checkout master        # quay lại master

git checkout <commit-sha>  # detached HEAD
```

#### Tạo + checkout trong một lệnh

```bash
git checkout -b featureX
```

#### Xóa nhánh

```bash
git branch -d featureX     # chỉ xóa khi đã merge an toàn
git branch -D featureX     # force delete (có thể làm mất work chưa merge)
```

Nếu nhánh chưa merge, `-d` sẽ báo *not fully merged*.

#### Khôi phục khi lỡ xóa nhánh (reflog)

`git reflog` lưu lịch sử local của HEAD (chỉ local, không nằm trong lịch sử repo chia sẻ).

```bash
git reflog
# tìm SHA của commit "mất" (dangling)

git checkout -b featureX <dangling-sha>
# tạo lại branch label tại commit đó
```

#### Cú pháp mới (Git mới)

```bash
git switch featureX
git switch -c featureX
```

### Path B — Sourcetree

#### Xem nhánh

| Tab | Nội dung |
|-----|----------|
| **Branches** | Nhánh local |
| **Remotes** | Nhánh remote |

#### Tạo nhánh

| Bước | Thao tác |
|------|----------|
| 1 | **Branch** -> **New Branch** |
| 2 | Nhập tên nhánh |
| 3 | Giữ **Checkout new branch** nếu muốn commit ngay trên nhánh mới |
| 4 | **Create Branch** |

#### Đổi nhánh

- Click tên nhánh để checkout.
- Nếu có thay đổi chưa commit, Sourcetree cảnh báo giữ hoặc discard thay đổi.

#### Detached HEAD

- Double-click một commit (không phải branch) sẽ vào detached HEAD.
- Muốn commit tiếp: tạo branch mới tại commit đó.

#### Xóa nhánh

- Right-click nhánh -> **Delete branch**.
- Nếu có unmerged commit, Sourcetree/Git chặn trừ khi force.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Làm feature song song, tránh phá production

**Bối cảnh:** `master/main` đang chạy production, bạn cần làm `feature/payment`.

| Bước | Lệnh/Thao tác |
|------|---------------|
| 1 | `git checkout -b feature/payment` |
| 2 | Commit trên feature branch |
| 3 | Trong lúc đó team vẫn có thể commit hotfix trên `master` |
| 4 | Khi feature xong -> PR/merge vào `master` |
| 5 | Sau merge, xóa nhánh topic (`git branch -d feature/payment`) |

**Lỗi hay gặp:** tạo nhánh nhưng quên checkout -> commit lọt vào `master`.

**Khôi phục nhanh:** lỡ `-D` nhánh có commit chưa merge -> `git reflog` tìm SHA -> `git checkout -b` tạo lại branch.

### Câu hỏi tự kiểm

1. Vì sao commit B có thể thuộc cả `master` và `featureX`?
2. Khác nhau giữa `git branch featureX` và `git checkout -b featureX`?
3. Khi nào cần dùng `git branch -D` thay vì `-d`?
4. `git reflog` khác `git log` ở điểm nào?

---

*Nguồn học: **(Command Line) Branches** và **(Sourcetree) Branches**, Module 2 — Version Control with Git.*

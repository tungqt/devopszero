# Mô hình đồ thị của Git (Git's Graph Model)

## Document Information
- **Doc ID:** 02-081
- **Summary:** Lịch sử commit là đồ thị có hướng không vòng; mỗi commit trỏ về cha; nhánh và merge hiển thị thế nào trên graph — Module 2.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Git lưu lịch sử project dưới dạng **đồ thị**: mỗi **commit** là một điểm, mũi tên trỏ về **commit cha**. Hiểu graph giúp đọc nhánh, merge và màn hình History trong Sourcetree hoặc terminal. *(Khái niệm commit/branch sơ bộ: [02-073].)*

---

## Block 1 — Mô hình (Structure)

### Từ đồ thị thường → DAG

| Thuật ngữ | Ý nghĩa |
|-----------|---------|
| **Graph (đồ thị)** | Mô hình các “thứ” có **liên kết** (gia phả, thư mục, …) |
| **Node (nút)** | Đối tượng được mô hình |
| **Edge (cạnh)** | Đường nối giữa các nút |
| **Directed (có hướng)** | Liên kết có **mũi tên** — hướng phụ thuộc cách bạn **định nghĩa** quan hệ (cha → con hay con → cha) |
| **Acyclic (không chu trình)** | **Không** có đường đi từ một nút quay lại chính nó |
| **DAG** | Directed Acyclic Graph — có hướng + không vòng |

```text
  Acyclic (OK)              Cyclic (KHÔNG dùng cho lịch sử Git)
      C                         A ──► B
      │                         ▲     │
      ▼                         └─────┘
      B ──► A                   (quay vòng A)
```

### Git graph: commit = nút, mũi tên = cha

```text
        D  ← merge (2 cha: B và C)
       / \
      B   C  ← branch (A có 2 con)
       \ /
        A

Mũi tên trong lý thuyết: trỏ TỚI parent(s).
Commit D "nhìn ngược" về B và C.
```

| Trong Git graph | Định nghĩa |
|-----------------|------------|
| **Mỗi node** | Một **commit** |
| **Cạnh / mũi tên** | Trỏ tới **commit cha** (parent) |
| **Toàn graph** | **Lịch sử** project |
| **Branch (nhánh)** | Một commit có **hơn một con** (vd. A → B và A → C) |
| **Merge** | Một commit có **hơn một cha** (vd. D có cha B và C) |

### Cách hiển thị trên công cụ

```text
  Sourcetree / GUI          Command line
  ─────────────────         ─────────────────────────
  Commit mới ở TRÊN         git log --graph
  (thời gian ngược)         (tương tự, có thể ngang/dọc)

  Thường KHÔNG vẽ mũi tên   Mũi tên / nhánh ngụ ý qua
  — thứ tự dọc = hướng      thứ tự và ký tự │ / \
```

Graph phức tạp (nhiều nhánh, merge) — GUI thường dễ nhìn; CLI vẫn dùng được.

---

## Block 2 — Xem đồ thị commit (Application)

**Prerequisite:** Repo đã có vài commit ([02-078]); đứng trong project directory.

### Command line

```bash
git log --graph

git log --oneline --graph

git log --oneline --graph --decorate   # thêm tên nhánh (master, origin/master)

# Nhiều nhánh — xem toàn bộ graph
git log --oneline --graph --all
```

| Tùy chọn | Tác dụng |
|----------|----------|
| `--graph` | Vẽ nhánh merge bằng ký tự `*`, `\|`, `/`, `\` |
| `--oneline` | Mỗi commit một dòng (hash rút + message) |
| `--all` | Mọi nhánh, không chỉ nhánh hiện tại |

**Đọc output:** Dòng trên = commit **mới hơn**; chỗ graph **tách** = branch; chỗ **hội** = merge.

### Sourcetree

Tab **History** — graph dọc, merge/branch thể hiện bằng đường nối; commit mới nhất trên cùng.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Đọc lịch sử sau khi merge feature

**Bối cảnh:** Team merge nhánh `feature/login` vào `master`. Bạn cần hiểu commit nào là merge, không chỉ đọc danh sách message.

| Bước | Hành động |
|------|-----------|
| 1 | `git log --oneline --graph -15` — thấy điểm graph **chia đôi** rồi **gộp** |
| 2 | Commit merge = **một nút, hai cha** — đó là điểm tích hợp feature |
| 3 | Nếu graph rối → mở Sourcetree History hoặc GitHub/GitLab **network graph** |
| 4 | Khi debug CI: biết merge commit nào đưa thay đổi lên `main` |

**Vì sao quan trọng với DevOps:** Pipeline thường chạy trên `main`; graph cho thấy thay đổi đến từ nhánh nào, merge khi nào — hữu ích khi rollback hoặc bisect (học sau).

### Câu hỏi tự kiểm

1. DAG “acyclic” nghĩa là gì với lịch sử commit?
2. Commit **D** có cha B và C — đó là **branch** hay **merge**?
3. Commit **A** có con B và C — đó là **branch** hay **merge**?

---

*Nguồn học: video **Git's Graph Model**, Module 2 — Version Control with Git (Atlassian / Coursera).*

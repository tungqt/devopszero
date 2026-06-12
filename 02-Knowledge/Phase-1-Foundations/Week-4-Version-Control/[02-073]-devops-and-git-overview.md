# DevOps và Git — Tổng quan (DevOps and Git in a Nutshell)

## Document Information
- **Doc ID:** 02-073
- **Summary:** DevOps vs waterfall; cải tiến liên tục với batch nhỏ; commit, lưu trữ hiệu quả, branch, merge, pull request — video mở đầu khóa Version Control with Git.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**DevOps** là tập thực hành phát triển phần mềm hiện đại (plan → build → release theo vòng lặp ngắn). **Git** là công cụ quản lý **nhiều phiên bản** của cùng một dự án khi team liên tục ship thay đổi nhỏ — nền tảng cho review, CI và triển khai an toàn.

---

## Block 1 — Mô hình (Structure)

### Waterfall vs cải tiến liên tục (small batch)

| Cách làm | Đặc điểm | Rủi ro |
|----------|----------|--------|
| **Waterfall** | Nhiều tính năng plan → dev → **release một lần** | Khó rollback; feedback muộn |
| **DevOps / continuous** | **Plan – build – release** từng thay đổi **nhỏ** | Phản hồi sớm; mỗi bản là một **version** rõ ràng |

Mỗi thay đổi nhỏ (sửa 1 bug, thêm 1 tính năng như icon đa màu) vẫn tạo **phiên bản sản phẩm khác** — Git ghi lại từng bước đó.

### Commit = phiên bản dự án

```text
Project (ví dụ: app 50 file)
    │
    ├── Commit A  — bản có bug / chỉ icon xanh
    │
    ├── Commit B  — chỉ khác A ở 1 file (đã sửa bug)
    │
    └── Commit C  — thêm tính năng (ví dụ icon đa màu)
```

- Mỗi **commit** = snapshot toàn bộ dự án tại một thời điểm (trong thực tế Git **không** nhân đôi mọi file — xem dưới).
- **Lịch sử** = tập hợp commit → xem lại, quay về bản cũ, hoặc commit mới **hoàn tác** thay đổi của commit trước (không bắt buộc xóa lịch sử).

### Lưu trữ: mỗi file unique chỉ lưu một lần

```text
Sau commit A:  Git lưu 50 file (unique)
Sau commit B:  Git thêm 1 object file đã đổi → tổng 51 object file, không phải 100 bản copy
```

→ Nhiều commit nhỏ vẫn **hiệu quả**; phù hợp mô hình DevOps “ship thường xuyên”.

### Branch = dòng phát triển độc lập

```text
                    master (mặc định; khóa gọi là master — nhiều repo hiện dùng main)
                    ─── A ─── B ─── (stable / production)
                         \
                          featureX ─── C   (master chưa biết C; coi B là mới nhất)

Song song:  featureX | bugY | featureZ  — không ảnh hưởng commit B trên master
```

| Nhánh | Vai trò thường gặp |
|-------|-------------------|
| **master / main** | Ổn định, đã test, có thể đang **production** |
| **featureX, bugY, …** | Phát triển / sửa lỗi **tách** khỏi bản ổn định |

### Merge và Pull Request

```text
Trước merge:
  master:     ... ─── B
  featureX:   ... ─── B ─── C

Pull Request:  yêu cầu gộp C (featureX) vào master
  → review, thảo luận, CI test pass

Sau merge (được chấp nhận):
  master:     ... ─── B ─── ... ─── M   (M chứa code featureX)
```

| Khái niệm | Ý nghĩa |
|-----------|---------|
| **Merge** | Gộp công việc từ nhánh này sang nhánh khác (nhiều cách merge — học sau) |
| **Pull request (PR)** | **Yêu cầu** merge + cổng chất lượng: review, approve, **automated tests** trước khi merge |

---

## Block 2 — Thuật ngữ & pattern (Application)

*Bài này là tổng quan khái niệm; lệnh CLI (`init`, `commit`, `branch`, …) ở các bài tiếp theo Module 1.*

### Thuật ngữ cốt lõi

| Thuật ngữ | Định nghĩa ngắn |
|-----------|-----------------|
| **DevOps** | Thực hành dev hiện đại: vòng lặp ngắn, tự động hóa, chất lượng trước khi release |
| **Commit** | Một phiên bản đã ghi trong lịch sử Git |
| **Branch** | Dòng phát triển độc lập; mọi commit thuộc một nhánh |
| **master / main** | Nhánh mặc định, thường là bản ổn định |
| **Merge** | Đưa thay đổi từ nhánh nguồn vào nhánh đích |
| **Pull request** | Quy trình “xin merge” kèm review + kiểm thử tự động |

### Pattern DevOps gắn với Git

| Pattern | Mô tả |
|---------|--------|
| **Small batch** | 1 bug fix / 1 feature nhỏ → 1 (hoặc vài) commit |
| **Stable + parallel work** | Production trên `master`; feature/bugfix trên nhánh riêng |
| **Quality gate** | PR bắt buộc review + CI pass trước merge vào `master` |
| **Rollback / undo** | Quay về commit cũ **hoặc** commit mới revert thay đổi không mong muốn |

### Câu hỏi tự kiểm (từ video)

1. Vì sao “50 file, sửa 1 file” giữa hai commit không làm Git lưu trùng 50 file hai lần?
2. Production đang ở commit **B** trên `master` — team có thể làm **featureX** và **bugY** song song không? `master` có bị ảnh hưởng trước merge không?
3. PR khác **merge** ở điểm nào? *(PR = quy trình phê duyệt; merge = thao tác gộp code sau khi chấp nhận.)*

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Production ổn định, phát triển tính năng song song

**Bối cảnh:** App 50 file đang chạy production tại commit **B** trên `master`. Product muốn thêm icon đa màu; đồng thời có bug báo từ khách.

| Bước | Hành động (khái niệm) |
|------|------------------------|
| 1 | Giữ `master` tại **B** — không deploy thử nghiệm trực tiếp lên production |
| 2 | Tạo nhánh `feature-multicolor-icons` → commit **C** (chỉ thêm/sửa file liên quan icon) |
| 3 | Tạo nhánh `bugfix-login-timeout` → commit độc lập, không chặn feature |
| 4 | Mở **PR** từ `feature-multicolor-icons` → `master`: review code + pipeline chạy test |
| 5 | Nếu khách không thích tính năng sau release: **revert** commit merge hoặc quay lại trạng thái trước feature (commit mới hoàn tác, không nhất thiết xóa lịch sử) |
| 6 | Chỉ khi PR approved + CI xanh → **merge** → `master` mới là candidate deploy |

**Liên hệ pipeline:** PR + CI là mắt xích giữa Git và DevOps (build/test trước khi thay đổi chạm production). Chi tiết lệnh branch/merge/PR: các bài Module 1–4 tiếp theo.

---

*Nguồn học: video **DevOps and Git in a Nutshell**, Module 1 — Version Control with Git (Atlassian / Coursera).*

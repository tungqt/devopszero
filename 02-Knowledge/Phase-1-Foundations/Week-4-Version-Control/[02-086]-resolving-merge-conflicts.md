# Git Resolving Merge Conflicts

## Document Information
- **Doc ID:** 02-086
- **Summary:** Tổng quan về merge conflict và cách xử lý xung đột khi gộp nhánh bằng Command line và Sourcetree — Module 3.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Xung đột (conflict) xảy ra khi **nhiều nhánh cùng sửa đổi chung một phần của một file** theo cách khác nhau, khiến Git không thể tự động quyết định phải giữ lại phần nào.

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Khi nào xảy ra Merge Conflict?

| Tình huống | Kết quả | Giải thích |
|------------|---------|------------|
| Sửa chung 1 đoạn trong 1 file | ❌ **Conflict** | Git cần con người quyết định nội dung cuối cùng. |
| Sửa các file khác nhau | ✅ Tự động | Git tự gộp tất cả các file vào merge commit. |
| Sửa các phần khác nhau trong cùng 1 file | ✅ Tự động | Git ghép nối các "hunk" (đoạn file) thay đổi lại với nhau. |

**Best Practices để tránh conflict:**
- Merge **thường xuyên, chia nhỏ** thay vì để dồn một cục lớn sau thời gian dài. "Tốt hơn là có nhiều vấn đề merge nhỏ hơn là một vấn đề merge khổng lồ".
- Code **module hóa**, decoupled tốt sẽ ít bị xung đột hơn.

### 3 commit tham gia vào việc giải quyết Conflict

1. **Ours / Mine:** Đỉnh của nhánh hiện tại (base branch - ví dụ: `master`).
2. **Theirs:** Đỉnh của nhánh được đem gộp (topic branch - ví dụ: `feature2`).
3. **Merge base:** Commit tổ tiên chung gần nhất của hai nhánh.

### Cơ chế đánh dấu Conflict của Git

Khi có conflict, Git sẽ tạm dừng merge và thay đổi file trên working tree với các **conflict markers** (dấu phân cách xung đột):

```text
Phần text tự động gộp (không có conflict)
<<<<<<< HEAD
(Ours / Mine: nội dung từ nhánh hiện tại)
=======
(Theirs: nội dung từ nhánh đang được gộp)
>>>>>>> feature2
Phần text tự động gộp
```

Con người cần sửa nội dung giữa các dấu này thành kết quả cuối cùng mong muốn.

---

## Block 2 — Thao tác (Application)

### Path A — Command line

#### Quy trình giải quyết conflict

| Bước | Thao tác |
|------|----------|
| 1 | Đảm bảo đang checkout base branch: `git checkout master` |
| 2 | Gộp nhánh: `git merge feature2` |
| 3 | Git báo lỗi conflict (`Automatic merge failed; fix conflicts and then commit the result.`). File xung đột được đánh dấu là `unmerged paths`. |
| 4 | Kiểm tra trạng thái: `git status` để xem các file cần sửa. |
| 5 | Mở file bị conflict bằng Text Editor. Chỉnh sửa file thành nội dung mong muốn, **xóa bỏ** các conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`). Lưu lại. |
| 6 | Đưa file đã sửa vào Staging area: `git add fileA.txt` |
| 7 | Commit kết quả merge: `git commit`. Editor hiện ra với message mặc định, bạn có thể sửa lại (bỏ `#` để hiện danh sách file bị conflict trong message). Lưu và đóng editor. |
| 8 | Xem lại lịch sử graph để xác nhận: `git log --oneline --graph` |
| 9 | *(Tùy chọn)* Xóa label của nhánh topic: `git branch -d feature2` |

#### Hủy bỏ Merge (Abort Merge)

Nếu không muốn tiếp tục giải quyết conflict và muốn quay lại trạng thái ban đầu:
```bash
git merge --abort
```
Lệnh này hủy toàn bộ quá trình merge và phục hồi lại working tree về trạng thái trước khi gõ lệnh merge.

---

### Path B — Sourcetree

#### Quy trình giải quyết conflict

| Bước | Thao tác |
|------|----------|
| 1 | Đảm bảo đang **checkout base branch** (vd: `master`). |
| 2 | Chọn **Merge** → Chọn nhánh cần gộp (vd: `feature2`) → **OK**. |
| 3 | Git báo lỗi conflict. Bấm **OK**. File xung đột được đưa vào **working tree** (tab File status). |
| 4 | Mở file bị conflict bằng **Text Editor** (hoặc merge tool). Tìm các conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`). |
| 5 | Chỉnh sửa file thành nội dung mong muốn, **xóa bỏ** các dấu marker. Lưu lại. |
| 6 | Quay lại Sourcetree, đưa file đã sửa vào **Staging area** (Stage). |
| 7 | Bấm **Commit**. Màn hình sẽ hiện sẵn thông báo commit mặc định (có thể bỏ dấu `#` để hiển thị file từng bị conflict trong message). Bấm **Commit**. |
| 8 | *(Tùy chọn)* Xóa label của nhánh topic. |

#### Hủy bỏ Merge (Abort Merge)

Nếu không muốn tiếp tục giải quyết conflict và muốn quay lại trạng thái ban đầu:
- **Sourcetree:** Repository menu → **Reset** → **Reset All**.
- Thao tác này hủy tất cả thay đổi local (bao gồm cả file Git đã chèn conflict markers).

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Hai lập trình viên sửa chung file config

**Bối cảnh:** Hai bạn dev làm 2 nhánh khác nhau. Cả hai cùng sửa file `config.yaml` ở dòng số 10. Nhánh `featureA` merge vào `main` trước. Bạn merge nhánh `featureB` vào `main` sau và bị conflict.

| Bước | Hành động |
|------|-----------|
| 1 | Bạn đang ở `main`, tiến hành merge `featureB`. Git báo lỗi. |
| 2 | Mở `config.yaml`, bạn thấy dòng config của `featureA` (`HEAD`) và `featureB`. |
| 3 | Bạn nhận ra cần giữ lại cả 2 thiết lập cấu hình. Bạn gộp mã lại cho hợp lệ, xóa các marker `<`, `=`, `>`. |
| 4 | Stage file `config.yaml` và Commit để kết thúc quá trình merge. |

---

*Nguồn học: **(Command Line) Resolving Merge Conflicts** và **(Sourcetree) Resolving Merge Conflicts**, Module 3 — Version Control with Git.*
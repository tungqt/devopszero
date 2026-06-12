# Git Rewriting History

## Document Information
- **Doc ID:** 02-090
- **Summary:** Cách viết lại lịch sử commit: `git commit --amend`, `git rebase -i` (sửa, xóa, gộp - squash), và Squash Merges bằng CLI và Sourcetree — Module 3.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

⚠️ **CẢNH BÁO QUAN TRỌNG:** Các thao tác trong bài này đều **viết lại lịch sử commit (Rewrite History)**, tạo ra các mã SHA-1 mới. 
**Quy tắc chung:** KHÔNG thực hiện trên các commit đã được push (chia sẻ) lên remote repository để tránh ảnh hưởng đến người khác. Chỉ dùng cho các commit ở local chưa push.

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### 1. Amending a Commit (Sửa commit gần nhất)
Sử dụng khi bạn vừa commit xong nhưng phát hiện gõ sai message, hoặc quên add một file nào đó.
- Thay vì tạo thêm một commit mới rác như "fix typo", bạn có thể sửa trực tiếp vào commit cuối cùng.
- Kết quả: Tạo ra một commit có SHA-1 mới thay thế cho commit vừa rồi.

### 2. Interactive Rebase (Rebase tương tác)
Cho phép bạn duyệt qua và chỉnh sửa lại một loạt các commit cũ trên nhánh. 
Bạn chọn một commit làm mốc, các thao tác sẽ được áp dụng cho các commit **con (children)** của nó (các commit mới hơn nó).

Các lệnh trong Interactive Rebase:
- **pick**: Giữ nguyên commit.
- **reword**: Sử dụng commit nhưng dừng lại để sửa commit message.
- **edit**: Sử dụng commit nhưng dừng lại (pause) để bạn sửa nội dung file (`amend`).
- **squash**: Gộp commit hiện tại vào commit ngay trước nó (giữ cả 2 message).
- **fixup**: Giống `squash` nhưng vứt bỏ message của commit hiện tại.
- **drop** (hoặc xóa dòng): Xóa bỏ hoàn toàn commit này (mất cả nội dung file đã đổi).
- **exec**: Chạy một lệnh shell trong quá trình rebase.
- *Tip*: Bạn có thể đảo thứ tự các dòng trong editor để đảo ngược thứ tự các commit.

### 3. Khác biệt: Squash vs Delete (Drop)
- **Squash**: Gộp 2 commit thành 1. Toàn bộ thay đổi file (work) của cả 2 commit **đều được giữ lại**.
- **Delete**: Xóa hoàn toàn commit. Mọi thay đổi về file trong commit đó sẽ bị **vứt bỏ**. Điều này dễ gây ra merge conflict do mất đi các file hoặc logic trung gian.

### 4. Squash Merge
Là sự kết hợp giữa `merge` và `squash`. Nó lấy toàn bộ thay đổi của một topic branch (có thể chứa hàng tá commits) và gom thành **một commit duy nhất** đắp lên base branch.
- Ưu điểm: Lịch sử trên nhánh base siêu gọn (chỉ 1 commit cho 1 tính năng lớn).
- Lịch sử gốc của nhánh topic sẽ bị mất trên branch hiện tại. Các commit cũ sẽ dần bị git dọn dẹp (garbage collected) sau khi bạn xóa nhánh topic.

---

## Block 2 — Thao tác (Application)

### Path A — Command Line

#### Amend Commit
| Thao tác | Lệnh |
|----------|------|
| **Chỉ sửa message** | `git commit --amend` (Mở Editor sửa message) |
| **Sửa file & message** | Sửa file -> `git add <file>` -> `git commit --amend` |
| **Sửa file, giữ nguyên message** | Sửa file -> `git add <file>` -> `git commit --amend --no-edit` |

#### Interactive Rebase
1. Chạy lệnh: `git rebase -i <commit-SHA>` (Chọn SHA của commit **nằm trước** các commit bạn muốn sửa).
2. Git mở Text Editor. Đổi chữ `pick` thành lệnh mong muốn (`edit`, `squash`, `drop`...). Lưu và đóng.
3. Nếu chọn **edit**:
   - Git dừng lại tại commit đó (trạng thái detached HEAD).
   - Bạn sửa file, `git add <file>`.
   - Gõ `git commit --amend` để lưu đè thay đổi (và sửa message nếu cần).
   - Gõ `git rebase --continue` để chạy tiếp các bước rebase còn lại.
4. Nếu chọn **squash**: Git sẽ mở Editor một lần nữa để bạn trộn 2 commit message lại thành 1.

#### Squash Merge
```bash
git checkout master
git merge --squash featureX
# Kết quả merge nằm ở staging area, tiến hành commit để hoàn tất
git commit -m "Add feature X (squashed)"
```

---

### Path B — Sourcetree

#### Amend Commit
- Trên màn hình Commit: Đánh dấu vào ô **Amend last commit**.
- Sửa lại nội dung tin nhắn hoặc tick thêm file mới ở phần Staging.
- Bấm **Commit**. Cảnh báo "rewrite history" hiện ra -> Chọn OK.

#### Interactive Rebase
1. Chọn commit gốc (trước vùng cần sửa), Chuột phải -> **Rebase children interactively**.
2. Một bảng giao diện hiện lên liệt kê các commit con. Chọn vào một commit và bấm các nút tương ứng ở dưới:
   - **Edit message**: Đổi tên commit (tương đương `reword`).
   - **Amend commit**: Dừng lại để sửa file (tương đương `edit`).
   - **Delete**: Xóa commit (tương đương `drop`).
   - **Squash with previous**: Gộp commit này vào commit phía trên nó.
3. Nếu chọn **Amend commit**:
   - Sourcetree tạm dừng (Pause). Trạng thái nhảy về Detached HEAD.
   - Bạn ra ngoài sửa file, kéo lên Stage, bấm nút **Commit**.
   - Vào menu **Actions** -> **Continue Rebase** để đi tiếp.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Gộp nhiều commit rác thành 1 (Squash) trước khi tạo PR

**Bối cảnh:** Khi làm một tính năng, bạn lỡ tạo ra 5 commit rất vụn vặt ở local: "Thêm nút bấm", "Sửa lỗi chính tả", "Fix padding", "Đổi màu nút", "Sẵn sàng". Trước khi tạo Pull Request, bạn muốn gom cả 5 cái này thành 1 commit duy nhất "Tạo nút Bấm Login" cho gọn gàng.

1. Bật **Interactive Rebase** (hoặc `git rebase -i HEAD~5`).
2. Giữ nguyên lệnh `pick` ở dòng đầu tiên ("Thêm nút bấm").
3. Đổi tất cả lệnh `pick` của 4 dòng còn lại thành `squash` (hoặc `fixup`).
4. Lưu lại, Git sẽ hỏi bạn gộp message, bạn nhập commit message mới chung là "Tạo nút Bấm Login".
5. Push nhánh lên remote. PR của bạn giờ chỉ hiển thị đúng 1 commit duy nhất rõ ràng, sạch sẽ.

---

*Nguồn học: **(Command Line) Rewriting History** và **(Sourcetree) Rewriting History**, Module 3 — Version Control with Git.*
# Git Rebasing

## Document Information
- **Doc ID:** 02-089
- **Summary:** Khái niệm rebasing, cách viết lại lịch sử commit, ưu/nhược điểm và thao tác giải quyết conflict khi rebase bằng CLI và Sourcetree — Module 3.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

⚠️ **CẢNH BÁO QUAN TRỌNG:** Rebasing thực hiện hành động **Viết lại lịch sử commit (Rewrite commit history)**.
Quy tắc vàng: **KHÔNG BAO GIỜ rebase các nhánh đã được chia sẻ (pushed) với người khác.**
Chỉ rebase các nhánh local của riêng bạn mà chưa ai khác sử dụng.

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Rebasing là gì?
Rebase (chuyển đổi base/tổ tiên) là hành động dời toàn bộ các commit của một nhánh sang một gốc (parent) mới.

Thay vì dùng lệnh `merge` sinh ra một *merge commit* có 2 parents để gộp nhánh, `rebase` sẽ bứng (nhổ) các commit của bạn và "đắp" chúng lên đỉnh của nhánh gốc. Kết quả là lịch sử commit trở thành **tuyến tính (linear)** hoàn hảo, có thể dễ dàng *fast-forward* merge sau đó.

### Quá trình "Reapplying Commits" bằng Diff
Git không copy bê nguyên commit sang gốc mới, vì mỗi commit chứa ảnh snapshot của toàn dự án. Cách Git hoạt động:
1. Git tính toán sự khác biệt (**Diff / Patch**) giữa commit hiện tại và parent của nó.
2. Nó mang sự khác biệt này "áp dụng lại" (reapply) lên đỉnh của gốc mới.
3. Do parent đã thay đổi, mã hash (ID) của commit mới tạo ra sẽ hoàn toàn khác commit cũ (vd: B trở thành B'). Đó là lý do gọi là "rewrite history".

### Ưu điểm và Nhược điểm

| Tiêu chí | Điểm cộng (Pros) | Điểm trừ (Cons) |
|----------|------------------|-----------------|
| **Lịch sử** | Gọn gàng, tuyến tính, sạch sẽ. Không sinh ra các merge commit rác (unnecessary merge commits). | Bị mất lịch sử thật sự (các mốc thời gian và điểm tách nhánh bị ghi đè). |
| **Bảo trì code** | Nhánh feature dễ dàng cập nhật code mới nhất từ base branch. Code được test trên nền mới nhất. Việc merge vào base sau này rất nhàn (chỉ fast-forward). | Việc thay đổi Commit IDs gây lỗi đồng bộ nghiêm trọng nếu nhánh đã được chia sẻ cho người khác. |
| **Conflict** | | Phải giải quyết conflict từng bước một. (Vì rebase là liên tiếp các lệnh áp dụng lại mã, nên có thể sinh ra conflict ở bất kỳ bước nào). |

---

## Block 2 — Thao tác (Application)

Rebasing là một dạng merge, nên **rất dễ xảy ra Merge Conflict**. Khi conflict xảy ra, quá trình rebase sẽ tạm dừng để bạn xử lý.

### Path A — Command Line

| Thao tác | Lệnh / Ý nghĩa |
|----------|---------------|
| **Bắt đầu Rebase** | `git checkout featureX`<br>`git rebase master`<br>(Hoặc gộp chung: `git rebase master featureX`) |
| **Khi có Conflict** | Git báo `merge conflict`. Dùng `git status` xem file nào bị đỏ. Mở file sửa và xóa các conflict markers. |
| **Tiếp tục Rebase** | `git add <file>`<br>`git rebase --continue`<br>*(Lưu ý: Không dùng `git commit` ở bước này)* |
| **Hủy Rebase** | `git rebase --abort`<br>Nếu bạn thấy conflict quá phức tạp và muốn hủy toàn bộ lệnh rebase để về trạng thái gốc. |

### Path B — Sourcetree

| Thao tác | Hướng dẫn |
|----------|----------|
| **Bắt đầu Rebase** | Checkout nhánh `feature`. Chuột phải vào nhánh `master` chọn **Rebase current changes onto master**. Bấm OK xác nhận cảnh báo viết lại lịch sử. |
| **Khi có Conflict** | Sourcetree hiện thông báo lỗi. File bị đỏ nằm trong mục Uncommitted changes. Mở file bằng Text Editor, xóa marker và sửa logic. |
| **Tiếp tục Rebase** | Bấm Stage cho file đã sửa. Sau đó chuột phải vào nhánh `master` lần nữa -> **Rebase...** -> Sourcetree báo "Rebase in progress", chọn **Continue Rebase**. |
| **Hủy Rebase** | Khi đang kẹt ở conflict, chọn menu **Actions > Abort Rebase**. |

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Rebase Feature Branch trước khi tạo Pull Request

**Bối cảnh:** Bạn làm tính năng trên nhánh `feature-login` mất 3 ngày. Trong 3 ngày đó, `main` đã nhận thêm 5 commit mới từ đồng nghiệp (sửa lỗi, nâng cấp core).

| Bước | Hành động (CLI) | Mục đích |
|------|-----------------|----------|
| 1 | `git checkout main`<br>`git pull` | Cập nhật `main` mới nhất từ máy chủ về máy local. |
| 2 | `git checkout feature-login`<br>`git rebase main` | Bứng các commit của bạn đắp lên đỉnh của 5 commit mới kia. Giúp code của bạn dùng core mới nhất. |
| 3 | Sửa Conflict (nếu có)<br>`git add .`<br>`git rebase --continue` | Đảm bảo code của bạn chạy đúng với logic mới của đồng nghiệp. |
| 4 | `git push -f origin feature-login` | (Cần `-f` force push vì commit ID của bạn đã bị đổi. *Chỉ an toàn nếu `feature-login` là nhánh riêng của bạn*). |
| 5 | Tạo Pull Request | Người review sẽ thấy 1 đường thẳng tắp các commit của bạn ở trên cùng, merge cực kỳ gọn gàng. |

---

*Nguồn học: **(Command Line) Rebasing** và **(Sourcetree) Rebasing**, Module 3 — Version Control with Git.*
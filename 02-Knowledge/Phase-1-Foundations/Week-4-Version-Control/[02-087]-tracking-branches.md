# Git Tracking Branches

## Document Information
- **Doc ID:** 02-087
- **Summary:** Tracking branch (nhánh theo dõi) là gì, cách xem trạng thái và quản lý sự đồng bộ giữa local và remote bằng CLI và Sourcetree — Module 3.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

## Block 1 — Mô hình (Structure)

### Tracking Branch là gì?
**Tracking branch** là một nhánh tại local (máy tính của bạn) hoạt động như một bản sao đại diện cho một nhánh trên remote repository (máy chủ như Bitbucket, GitHub).

- **Cú pháp đặt tên:** `<tên-remote>/<tên-nhánh>` (Ví dụ: `origin/master` hoặc `origin/main`).
- Mặc định khi bạn chạy lệnh `git clone`, Git sẽ tự động tạo một tracking branch cho nhánh mặc định của remote (thường là `master` hoặc `main`).

### Đặc điểm của Tracking Branch
- **Cập nhật độc lập:** Tracking branch đóng vai trò trung gian giữa local branch và remote branch. Nó **chỉ được cập nhật thông qua các lệnh mạng (network commands)** như `clone`, `fetch`, `pull`, `push`.
- Việc bạn commit ở local (`git commit`) sẽ làm local branch tiến tới, nhưng **không** làm thay đổi tracking branch. Tracking branch chỉ di chuyển khi bạn thực sự giao tiếp với remote (ví dụ: chạy `git push`).
- Nhờ tracking branch, Git có thể so sánh nhánh local của bạn đang tiến lên trước (**ahead**) hay tụt lại phía sau (**behind**) so với remote.

### Symbolic Reference (Tham chiếu tượng trưng)
- Trong danh sách nhánh, bạn thường thấy `remotes/origin/HEAD`. Đây là một **symbolic reference** trỏ tới tracking branch mặc định (thường là `origin/master`).
- Việc có `origin/HEAD` cho phép bạn gọi tắt `origin/master` thành `origin` trong các lệnh Git.
- Quản trị viên dự án có thể đổi nhánh mặc định này trên Bitbucket/GitHub (mục Settings > Main branch).

---

## Block 2 — Thao tác (Application)

| Path | Mục đích |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

### Path A — Command Line

| Thao tác | Lệnh / Ý nghĩa |
|----------|---------------|
| **Liệt kê tất cả nhánh** | `git branch --all` (hoặc `-a`) <br> Hiển thị cả nhánh local và nhánh tracking (chữ màu đỏ, bắt đầu bằng `remotes/origin/`). |
| **Xem trạng thái đồng bộ** | `git status` <br> Thông báo "Your branch is ahead of 'origin/master' by 1 commit" (Local có commit mới chưa push) hoặc "up to date" (đã đồng bộ tại thời điểm fetch cuối cùng). |
| **Đổi tracking branch mặc định** | `git remote set-head origin develop` <br> Đổi tham chiếu `origin/HEAD` từ `master` sang `develop`. |
| **Xem log kèm tracking branch** | `git log --all --oneline --graph` <br> Cho phép nhìn thấy vị trí của `origin/master` so với `master` hiện tại trên commit graph. |

*(Lưu ý: `git status` sử dụng thông tin được lưu cache từ lần cuối cùng chạy lệnh mạng. Nếu ai đó push lên remote, `git status` sẽ không biết cho đến khi bạn chạy `git fetch`.)*

### Path B — Sourcetree

| Giao diện / Tính năng | Mô tả |
|-----------------------|-------|
| **Cột Sidebar** | - **Branches**: hiển thị nhánh local.<br>- **Remotes > origin**: hiển thị `HEAD` và tracking branches (vd: `master`). |
| **Dấu hiệu lệch pha (Ahead/Behind)** | - Khi local có commit chưa push, mục Branches sẽ hiển thị icon mũi tên hướng lên kèm số (VD: `1` ⬆️).<br>- Nút **Push** trên thanh công cụ cũng hiển thị số badge tương ứng. |
| **Labels trên Graph** | - `master`: nhãn của nhánh local.<br>- `origin/master`: nhãn của tracking branch.<br>- `origin/HEAD`: nhãn tham chiếu mặc định (thường đứng cùng `origin/master`). |
| **Bộ lọc Graph (Filters)** | Nút dropdown phía trên commit graph giúp thay đổi cách hiển thị: **All branches** (mặc định), Hide/Show remote branches, Ancestor order, Date order. |

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Phát hiện trạng thái "Ahead" sau khi Commit

**Bối cảnh:** Bạn clone repo, tạo 1 local commit mới nhưng chưa push. Bạn muốn kiểm tra trạng thái đồng bộ giữa máy tính và server.

**Path A (CLI):**
1. Gõ lệnh `git status`. Git báo "Your branch is **ahead** of 'origin/master' by 1 commit". Git gợi ý chạy `git push`.
2. Gõ `git log --all --oneline --graph`. Bạn sẽ thấy nhãn `master` nằm trên nhãn `origin/master` 1 bước.
3. Gõ `git push`. Tracking branch `origin/master` sẽ chạy lên khớp với `master` của bạn.

**Path B (Sourcetree):**
1. Nhìn vào cột Branches bên trái, bạn thấy `master` có báo số `1` mũi tên lên ⬆️. Nút Push trên toolbar báo số `1`.
2. Nhìn vào Graph, commit mới nhất có nhãn `master`, commit trước đó giữ nhãn `origin/master` và `origin/HEAD`.
3. Nhấn nút **Push**, chọn nhánh `master` và OK. Ba nhãn sẽ cùng trỏ về commit mới nhất.

---

*Nguồn học: **(Command Line) Tracking Branches** và **(Sourcetree) Tracking Branches**, Module 3 — Version Control with Git.*
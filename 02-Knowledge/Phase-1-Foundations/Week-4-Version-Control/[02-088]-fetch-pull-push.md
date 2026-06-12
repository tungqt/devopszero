# Git Network Commands: Fetch, Pull, Push

## Document Information
- **Doc ID:** 02-088
- **Summary:** Quản lý giao tiếp giữa local và remote repository qua các lệnh mạng: fetch, pull, và push bằng Command Line và Sourcetree — Module 3.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Phần lớn các lệnh Git (`commit`, `checkout`, `branch`...) chỉ hoạt động trên local repository. Để giao tiếp và đồng bộ với remote repository, Git sử dụng 4 lệnh mạng chính: `clone`, `fetch`, `pull`, và `push`. Bài này tập trung vào 3 lệnh cập nhật dữ liệu.

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### 1. `git fetch` (Lấy dữ liệu)
Lệnh `fetch` kết nối với remote repository, tải về các objects (commits, files) và references mới, sau đó **cập nhật tracking branch** (vd: `origin/master`).
- **KHÔNG thay đổi** nhánh local của bạn.
- **KHÔNG thay đổi** files trong working tree.
- Fetch an toàn, cho phép bạn xem remote có gì mới trước khi quyết định gộp vào local.

### 2. `git pull` (Lấy và gộp)
Lệnh `pull` là sự kết hợp của 2 thao tác liên tiếp:
1. `git fetch`
2. `git merge FETCH_HEAD` (gộp tracking branch vừa cập nhật vào nhánh local hiện tại).

Tùy vào trạng thái của local branch, `pull` sẽ xử lý merge theo một trong các cách sau:
- **Fast-forward merge:** Nếu bạn chưa có commit nào mới ở local so với lúc tách ra. Git chỉ dời label nhánh local tiến lên khớp với tracking branch.
- **Merge commit:** Nếu bạn đã có commit mới ở local, trong khi remote cũng có commit mới. Việc pull lúc này giống hệt như merge một feature branch, tạo ra một commit gộp có 2 parents.
- **Hủy (Abort):** Nếu bạn có file đang sửa dở (uncommitted changes) trong working tree và việc pull sẽ ghi đè lên file đó, Git sẽ an toàn hủy thao tác pull. (Giải pháp: commit hoặc stash trước khi pull). Nếu thay đổi dở dang ở một file không liên quan (không bị ghi đè), pull vẫn tiếp tục bình thường.

### 3. `git push` (Đẩy dữ liệu)
Lệnh `push` lấy các commit mới trên nhánh local của bạn và đẩy lên remote repository.
- **Quy tắc quan trọng:** Luôn nên fetch/pull để đồng bộ remote changes **trước khi** push.
- Nếu remote đã có commit mới mà bạn chưa kéo về, lệnh `push` sẽ bị từ chối (rejected) để tránh ghi đè mất công sức của người khác. Git sẽ gợi ý bạn làm lệnh `pull` trước.

---

## Block 2 — Thao tác (Application)

### Path A — Command Line

| Thao tác | Lệnh / Ý nghĩa |
|----------|---------------|
| **Fetch** | `git fetch`<br>Tải về thay đổi. Dùng `git status` sau lệnh này để xem local đang ahead/behind tracking branch. |
| **Pull mặc định** | `git pull`<br>Sẽ fetch và tự động fast-forward nếu được, hoặc tạo merge commit nếu cần. |
| **Pull chỉ Fast-forward** | `git pull --ff-only`<br>Chỉ thực hiện pull nếu có thể fast-forward. Nếu cần merge commit, nó sẽ hủy thao tác để bạn tự xử lý sau. |
| **Pull ép tạo Merge commit** | `git pull --no-ff`<br>Luôn tạo merge commit dù có thể fast-forward. |
| **Push cơ bản** | `git push`<br>Đẩy commit lên remote (yêu cầu tracking branch đã được set). |
| **Push và set Tracking** | `git push -u origin master`<br>Push nhánh `master` lên `origin` và dùng cờ `-u` (viết tắt của `--set-upstream`) để gắn kết local branch với remote branch. Các lần sau chỉ cần gõ `git push`. |

### Path B — Sourcetree

| Thao tác | Hướng dẫn |
|----------|----------|
| **Fetch** | Nhấn nút **Fetch** trên thanh công cụ. Có thể cấu hình Sourcetree tự fetch ngầm (Tools > Options/Preferences > Advanced > "Refresh remote status in the background"). |
| **Pull** | Nhấn nút **Pull**. Một hộp thoại sẽ hiện ra cho phép chọn remote, nhánh, và tùy chọn (như `Commit merged changes immediately` hay `Fast-forward only`). Giao diện sẽ thông báo rõ có bao nhiêu commit chuẩn bị được kéo về. |
| **Push** | Khi local ahead, nút **Push** sẽ hiện số (vd: `2` mũi tên lên). Nhấn **Push**, chọn nhánh muốn đẩy, sau đó OK. Nếu bị reject do out-of-sync, màn hình báo lỗi hiện ra và nhắc bạn thực hiện Pull trước. |

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Vòng đời làm việc nhóm (Fetch -> Pull -> Push)

**Bối cảnh:** Bạn vừa code xong tính năng và commit ở local. Bạn dự định đẩy code lên remote cho mọi người dùng.

| Bước | Lệnh (CLI) / Thao tác GUI | Kết quả / Ý nghĩa |
|------|---------------------------|-------------------|
| 1 | `git push` (hoặc nhấn nút Push) | ❌ **Bị reject.** Có ai đó vừa push code lên remote. |
| 2 | `git fetch` | Tải thông tin mới về. Kiểm tra `git log --all --graph` để thấy nhánh `origin/master` đã chạy lên trước. |
| 3 | `git pull` | Gộp code của người kia vào code của bạn. Vì cả 2 đều có commit mới, Git mở Editor yêu cầu lưu **Merge commit**. |
| 4 | Resolve conflict *(nếu có)* | Sửa file, `git add`, `git commit` nếu lúc pull xảy ra xung đột. |
| 5 | `git push` | ✅ **Thành công.** Code của bạn (bao gồm cả merge commit kết nối code của đồng nghiệp) đã nằm an toàn trên máy chủ. |

---

*Nguồn học: **(Command Line) Fetch, Pull and Push** và **(Sourcetree) Fetch, Pull and Push**, Module 3 — Version Control with Git.*
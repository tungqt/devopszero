# Git Pull Requests

## Document Information
- **Doc ID:** 02-091
- **Summary:** Khái niệm Pull Request (PR), quy trình tạo PR trên một kho lưu trữ chung (Single repo) và qua Fork (Multi-repo) — Module 4.
- **Version:** 0.0.1
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

**Pull Request (PR)** không phải là một lệnh cốt lõi của phần mềm Git cục bộ (CLI), mà là một tính năng được cung cấp bởi các dịch vụ lưu trữ mã nguồn như Bitbucket, GitHub, GitLab.

Mục tiêu cuối cùng của Pull Request là **merge một nhánh** vào dự án chính. Nhưng lợi ích lớn nhất của nó là cung cấp không gian để **giao tiếp nhóm, review code (code review), và phê duyệt (approve)** trước khi mã nguồn thực sự được gộp vào.

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Thời điểm mở Pull Request
Bạn có thể mở Pull Request ở bất kỳ thời điểm nào trong vòng đời của một nhánh:
1. **Ngay khi vừa tạo nhánh:** Tạo ra một không gian để team vào thảo luận, đóng góp ý tưởng ngay từ đầu.
2. **Khi đang code dở:** Khi bạn bị "bí" logic và cần team vào review, hỗ trợ giải quyết vấn đề.
3. **Khi đã code xong:** Tính năng đã sẵn sàng để được team review (Code Review) và sáp nhập (Merge).

*(Lưu ý: Bất kỳ commit nào bạn `push` lên nhánh đó sau khi mở PR đều sẽ tự động được cập nhật vào PR mà không cần tạo PR mới).*

### Hai mô hình Pull Request

#### 1. Single Remote Repository (Một kho lưu trữ)
Mô hình phổ biến cho nhóm nội bộ có quyền ghi (write access) vào kho chung.
- **Cách làm:** Người dùng tự tạo một nhánh mới trực tiếp trên kho lưu trữ chung (ví dụ: tạo nhánh `featureX`). Sau đó tạo PR yêu cầu gộp `featureX` vào `master`/`main` trên cùng kho đó.

#### 2. Multi-Repository / Forking (Hai kho lưu trữ)
Mô hình phổ biến của thế giới mã nguồn mở (Open Source) hoặc khi người dùng **không có quyền ghi** vào kho dự án gốc (Upstream).
- **Forking:** Copy toàn bộ kho lưu trữ gốc (Upstream repository) về tài khoản cá nhân của bạn. Bản copy này gọi là **Fork**, và bạn có toàn quyền quản trị trên Fork này.
- **Cách làm:** Bạn code và commit trên Fork của mình. Khi xong, bạn tạo một PR yêu cầu gộp nhánh từ Fork cá nhân của bạn vào kho Upstream.
- **Đồng bộ Fork:** Khi kho Upstream có cập nhật mới, Fork của bạn sẽ bị "lùi lại phía sau" (behind). Bạn cần đồng bộ (Sync) kho Upstream về Fork của mình (Bitbucket cung cấp nút Sync, hoặc bạn có thể dùng CLI để pull upstream về).

---

## Block 2 — Thao tác (Application)

### Path A — Command Line (Mô hình Single Repo)

1. **Chuẩn bị ở Local:**
   - Tạo nhánh và làm việc: `git checkout -b featureX`
   - Chỉnh sửa, `git add`, `git commit`
   - Đẩy nhánh lên Remote (lần đầu tiên): `git push -u origin featureX`
2. **Trên Remote (Bitbucket/GitHub):**
   - Khi chạy lệnh `push`, Terminal có thể in ra một đoạn URL gợi ý bấm vào để tạo Pull Request.
   - Nhấp vào URL đó, điền Title, Description, chọn Reviewers và bấm **Create Pull Request**.
3. **Review và Merge (Trên Web):**
   - Team xem **Diff** (sự thay đổi file), để lại **Comment**.
   - Reviewer bấm **Approve** nếu đồng ý, hoặc **Decline** để từ chối vĩnh viễn.
   - Khi đủ số Approve theo chính sách, bấm **Merge**. Có thể chọn chiến lược merge như *Merge commit* hoặc *Squash*. PR sẽ tự đóng sau khi Merge.
4. **Dọn dẹp:**
   - Xóa nhánh local: `git branch -d featureX`
   - Xóa nhánh remote: `git push -d origin featureX`

### Path B — Forking & Multi-Repo Pull Request

1. **Tạo Fork (Bitbucket/GitHub):**
   - Vào kho Upstream (kho gốc), bấm nút **Fork this repository**. Đặt tên cho kho copy trên tài khoản của bạn.
2. **Làm việc trên Fork:**
   - Clone Fork của bạn về máy (chứ không clone Upstream).
   - Làm việc bình thường: tạo nhánh, commit, và push lên Fork.
3. **Tạo Pull Request ngược về Upstream:**
   - Lên giao diện web của Fork, chọn **Create Pull Request**.
   - Bitbucket/GitHub sẽ tự hiểu bạn muốn tạo PR từ nhánh của Fork gộp vào nhánh của Upstream. Điền thông tin và xác nhận.
4. **Phía Upstream duyệt PR:**
   - Owner của kho Upstream sẽ thấy PR của bạn. Họ có thể review, comment, và bấm Merge nếu đồng ý.

---

## Block 3 — DevOps scenario (Use case)

### Scenario: Đóng góp mã nguồn cho một dự án Open Source

**Bối cảnh:** Bạn tìm thấy một dự án mã nguồn mở rất hay trên GitHub/Bitbucket. Bạn phát hiện một lỗi logic và muốn sửa nó. Vì bạn không phải thành viên dự án, bạn không thể đẩy code trực tiếp lên kho của họ.

1. **Hành động 1:** Nhấp vào nút **Fork** trên trang của dự án. Bây giờ bạn có một kho copy thuộc về tài khoản của bạn.
2. **Hành động 2:** Clone kho Fork của bạn về máy. Chạy lệnh `git checkout -b fix-bug-logic`. Sửa lỗi và commit.
3. **Hành động 3:** `git push -u origin fix-bug-logic` (Lúc này `origin` là kho Fork của bạn, không phải kho gốc).
4. **Hành động 4:** Lên giao diện web của Fork, bấm tạo Pull Request. Hệ thống sẽ tạo một yêu cầu (PR) gửi thẳng tới chủ dự án gốc (Upstream).
5. **Hành động 5:** Chủ dự án vào xem mã của bạn, thấy hợp lý, họ bấm **Approve** và **Merge**. Lỗi đã được sửa, tên bạn được ghi danh là Contributor (người đóng góp) vào dự án gốc.

---

*Nguồn học: **(Command Line) Pull Requests I**, **(Sourcetree) Pull Requests I**, và **Pull Requests II**, Module 4 — Version Control with Git.*
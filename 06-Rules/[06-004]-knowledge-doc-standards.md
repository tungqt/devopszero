# Quy chuẩn Viết Tài liệu Kiến thức (Knowledge Documentation Rule)

## Document Information
- **Doc ID:** 06-004
- **Summary:** Tiêu chuẩn về cách trình bày, văn phong và tính hệ thống khi viết các bài học trong thư mục 02-Knowledge.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Mục đích
Đảm bảo tất cả các tài liệu học tập trong thư mục `02-Knowledge` đều mang tính học thuật, hệ thống, rõ ràng và có thể dùng làm tài liệu tham khảo (Reference) lâu dài, thay vì chỉ là những câu trả lời đối phó dạng Q&A.

## 2. Tiêu chuẩn Trình bày (Structure Standards)

Khi tạo một tài liệu kiến thức mới, AI BẮT BUỘC phải tuân thủ cấu trúc sau:

### 2.1. Luôn có phần "Tổng quan" (Overview)
- Mọi tài liệu phải bắt đầu bằng một phần giải thích **Tại sao phải học cái này?** (Ví dụ: "Tại sao DevOps phải giỏi Linux?").
- Không bao giờ nhảy ngay vào việc liệt kê câu lệnh hay code mà không có bối cảnh.

### 2.2. Luôn có phần "Môi trườnthực hành" (Environment Setup)
- Nếu tài liệu có chứa các câu lệnh (CLI) hoặc code cần thực thi, BẮT BUỘC phải có mục giải thích rõ ràng về môi trường:
  - Lệnh này chạy ở đâu? (Local Mac/Windows hay Remote Server).
  - Có cần cài đặt công cụ gì trước khi chạy không? (Prerequisites).
- Tuyệt đối không để người đọc phải tự hỏi "Tôi gõ lệnh này vào đâu?".

### 2.3. Phân cấp thông tin rõ ràng (Hierarchy)
- Sử dụng Heading (`##`, `###`) để chia nhỏ các khái niệm.
- Nhóm các lệnh/công cụ có cùng chức năng vào chung một mục (Ví dụ: Nhóm lệnh điều hướng `cd`, `ls` vào một chỗ; Nhóm lệnh thao tác file `cp`, `rm` vào một chỗ).

### 2.4. Luôn có phần "Thực hành" (Hands-on Lab)
- Ở cuối mỗi tài liệu, BẮT BUỘC phải có một bài tập nhỏ (Lab) gồm 3-5 bước để người dùng có thể áp dụng ngay kiến thức vừa học.

## 3. Tiêu chuẩn Văn phong (Tone & Style)
- **Tính hệ thống:** Viết như một cuốn sách giáo khoa chuyên ngành, không viết theo kiểu trò chuyện (chat) hay đối phó (Ví dụ: Không dùng các câu như *"Để trả lời câu hỏi của bạn..."* hay *"Bạn có thể chạy nó ở đây..."*).
- **Tính minh bạch:** Nếu một công cụ có nhiều cách dùng, hãy chỉ ra cách dùng phổ biến nhất (Best Practice) và giải thích tại sao. Cảnh báo rõ ràng nếu có lệnh nguy hiểm (như `rm -rf` hay `chmod 777`).
- **Ngôn ngữ:** Sử dụng tiếng Việt chuẩn, giữ nguyên các thuật ngữ chuyên ngành tiếng Anh (Ví dụ: `Directory`, `Permission`, `Process`) và có chú thích tiếng Việt bên cạnh trong lần xuất hiện đầu tiên.
# Quy chuẩn Viết Tài liệu Kiến thức (Knowledge Documentation Rule)

## Document Information
- **Doc ID:** 06-004
- **Summary:** Tiêu chuẩn về cách trình bày, văn phong và tính hệ thống khi viết các bài học trong thư mục 02-Knowledge.
- **Version:** 0.0.2
- **Date:** 2026-05-19
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Mục đích
Đảm bảo tất cả các tài liệu học tập trong thư mục `02-Knowledge` đều mang tính hệ thống, rõ ràng và dùng được làm **tài liệu tham khảo (Reference) lâu dài**, thay vì transcript khóa học hay Q&A đối phó.

**Quy tắc nội dung & chống trùng lặp:** xem `[06-001]-document-format.md` — **Mục 5**.

## 2. Tiêu chuẩn Trình bày (Structure Standards)

Khi tạo hoặc cập nhật tài liệu trong `02-Knowledge`, AI BẮT BUỘC tuân thủ **3 block** trong `[06-001]` mục 5.2, và các tiêu chuẩn bổ sung dưới đây:

### 2.1. Mở đầu ngắn (DevOps context — không lịch sử)
- Tối đa **2–3 câu**: chủ đề này phục vụ việc gì trong DevOps (server, container, pipeline, bảo mật).
- **Không** kể lịch sử Unix/Linux, timeline khóa học, hay learning objectives từ slide (đã liệt kê trong `[06-001]` mục 5.3).

### 2.2. Môi trường thực hành (Environment Setup)
- Nếu tài liệu có chứa các câu lệnh (CLI) hoặc code cần thực thi, BẮT BUỘC phải có mục giải thích rõ ràng về môi trường:
  - Lệnh này chạy ở đâu? (Local Mac/Windows hay Remote Server).
  - Có cần cài đặt công cụ gì trước khi chạy không? (Prerequisites).
- Tuyệt đối không để người đọc phải tự hỏi "Tôi gõ lệnh này vào đâu?".

### 2.3. Phân cấp thông tin rõ ràng (Hierarchy)
- Sử dụng Heading (`##`, `###`) để chia nhỏ các khái niệm.
- Nhóm các lệnh/công cụ có cùng chức năng vào chung một mục (Ví dụ: Nhóm lệnh điều hướng `cd`, `ls` vào một chỗ; Nhóm lệnh thao tác file `cp`, `rm` vào một chỗ).

### 2.4. Thực hành (Hands-on Lab / DevOps scenario)
- Gộp vào **Block 3 — DevOps scenario** (`[06-001]` mục 5.2): lab **3–5 bước** hoặc một workflow thực tế (EC2, script CI, cron).
- Không bắt buộc mục Lab riêng nếu Block 3 đã đủ hành động cụ thể.

## 3. Tiêu chuẩn Văn phong (Tone & Style)
- **Tính hệ thống:** Viết như một cuốn sách giáo khoa chuyên ngành, không viết theo kiểu trò chuyện (chat) hay đối phó (Ví dụ: Không dùng các câu như *"Để trả lời câu hỏi của bạn..."* hay *"Bạn có thể chạy nó ở đây..."*).
- **Tính minh bạch:** Nếu một công cụ có nhiều cách dùng, hãy chỉ ra cách dùng phổ biến nhất (Best Practice) và giải thích tại sao. Cảnh báo rõ ràng nếu có lệnh nguy hiểm (như `rm -rf` hay `chmod 777`).
- **Ngôn ngữ:** Sử dụng tiếng Việt chuẩn, giữ nguyên các thuật ngữ chuyên ngành tiếng Anh (Ví dụ: `Directory`, `Permission`, `Process`) và có chú thích tiếng Việt bên cạnh trong lần xuất hiện đầu tiên.
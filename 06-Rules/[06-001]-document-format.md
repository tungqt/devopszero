# Quy chuẩn Format Tài liệu (Document Format Rule)

## Document Information
- **Doc ID:** 06-001
- **Summary:** Quy định về định dạng, mã định danh và quản lý phiên bản cho tất cả tài liệu.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Mục đích
Quy định này áp dụng cho TẤT CẢ các file tài liệu (Markdown) được tạo ra trong Workspace này, bất kể là do người dùng tự viết hay do AI tạo ra.

## 2. Template Bắt Buộc

### 2.1. Đối với MỌI file tài liệu (Document Information)
- **Tên file:** Phải luôn có prefix là mã định danh `[XX-XXX]`. Ví dụ: `[00-000]-executive-summary.md`.
- **Nội dung:** Mỗi file tài liệu mới phải luôn bắt đầu bằng block thông tin dưới đây ở ngay đầu file. 

**Mẫu cần copy:**
```markdown
# [Tên Tiêu Đề Của Tài Liệu]

## Document Information
- **Doc ID:** [Mã định danh tài liệu, VD: 03-001]
- **Summary:** [Mô tả ngắn gọn 1-2 câu về nội dung file]
- **Version:** [Ví dụ: 0.0.1]
- **Date:** [YYYY-MM-DD]
- **PRD Reference:** [Link hoặc Mã tham chiếu nếu có, hoặc N/A]
- **Status:** [Draft / Review / Final / Outdated]

---
[Nội dung chi tiết của tài liệu bắt đầu từ đây...]
```

### 2.2. Đối với file Tóm tắt Thư mục (Executive Summary)
- MỖI thư mục (kể cả thư mục gốc và thư mục con) BẮT BUỘC phải có một file tên là `[XX-000]-executive-summary.md`.
- File này có nhiệm vụ giải thích tổng quát nội dung của toàn bộ thư mục đó để AI đọc vào là hiểu ngay ngữ cảnh mà không cần quét qua tất cả các file.

**Mẫu cần copy cho file Executive Summary:**
````markdown
# Executive Summary - [Tên Thư Mục]

## Document Information
- **Doc ID:** [Mã định danh, VD: 01-000]
- **Summary:** Tổng quan về thư mục [Tên Thư Mục]
- **Version:** [Ví dụ: 0.0.1]
- **Date:** [YYYY-MM-DD]
- **PRD Reference:** [Link hoặc N/A]
- **Status:** [Draft / Review / Final]

---

## Mục đích
[Mô tả ngắn gọn mục đích của thư mục này...]

## Directory Map
```text
[Tên-Thư-Mục]/
├── [Tên-File-1]       # [BẮT BUỘC: Chú thích ngắn gọn không quá 15 từ]
└── [Tên-Thư-Mục-Con]/ # [BẮT BUỘC: Chú thích ngắn gọn không quá 15 từ]
```
````

## 3. Quy tắc Mã định danh (Doc ID)
Để AI và người dùng tìm kiếm nhanh mà không cần đọc nội dung, mỗi tài liệu phải có một mã `Doc ID` duy nhất.
- **Format:** `XX-XXX` (hoặc `XX-XXXX` nếu vượt quá 999).
- **2 ký tự đầu (XX):** Là mã số của thư mục gốc.
  - `00`: 00-Overview
  - `01`: 01-Journal
  - `02`: 02-Knowledge
  - `03`: 03-Projects
  - `04`: 04-Toolbox
  - `05`: 05-Document
  - `06`: 06-Rules
- **3 ký tự sau (XXX):** Là số thứ tự tăng dần của tài liệu trong thư mục đó, bắt đầu từ `000`.
- **Ví dụ:** 
  - `00-000`: File executive summary của thư mục Overview.
  - `02-001`: Tài liệu kiến thức đầu tiên trong thư mục Knowledge.
  - `06-002`: Tài liệu rule số 2 trong thư mục Rules.

## 4. Hướng dẫn cho AI
- **Khi được yêu cầu tạo tài liệu mới:** AI BẮT BUỘC phải chèn block `Document Information` này vào đầu file và tự động sinh `Doc ID` phù hợp.
- **Date:** Tự động lấy ngày hiện tại lúc tạo file.
- **Version:** Mặc định là `0.0.1` cho tất cả các file mới trong giai đoạn khởi tạo này (cho đến khi có lệnh bắt đầu thực hiện dự án).
- **Quy tắc Cập nhật Version:** MỖI LẦN có sự thay đổi, chỉnh sửa hoặc cập nhật nội dung của một file, AI BẮT BUỘC phải nâng version của file đó lên (ví dụ: từ `0.0.1` -> `0.0.2`, hoặc `0.1.0` -> `0.2.0` tùy mức độ thay đổi).
- **Status:** Mặc định là `Draft` trừ khi được yêu cầu khác.

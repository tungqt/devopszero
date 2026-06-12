# Quy chuẩn Format Tài liệu (Document Format Rule)

## Document Information
- **Doc ID:** 06-001
- **Summary:** Quy định về định dạng, mã định danh và quản lý phiên bản cho tất cả tài liệu.
- **Version:** 0.0.3
- **Date:** 2026-05-19
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
- **Summary:** [1–2 câu bằng chữ thường, dễ hiểu — xem mục 2.3]
- **Version:** [Ví dụ: 0.0.1]
- **Date:** [YYYY-MM-DD]
- **PRD Reference:** [Link hoặc Mã tham chiếu nếu có, hoặc N/A]
- **Status:** [Draft / Review / Final / Outdated]

---
[Nội dung chi tiết của tài liệu bắt đầu từ đây...]
```

### 2.3. Trường **Summary** — viết cho người đọc, không phải cheat sheet

**Summary** là câu mô tả **file này nói về điều gì** khi tra cứu nhanh (Directory Map, search, AI context). Độc giả có thể **chưa** mở file — Summary phải đọc được như một dòng giới thiệu.

| Viết **đúng** | Viết **sai** (tránh) |
|---------------|----------------------|
| Câu hoàn chỉnh, tiếng Việt (hoặc ngôn ngữ file) tự nhiên | Chuỗi lệnh, flag, tên kỹ thuật nối bằng `;` |
| Nêu **mục đích / kết quả** học được | Liệt kê `git add`, `chmod`, pipeline như menu |
| 1–2 câu, tối đa ~25–35 từ | Copy-paste heading Block 2 hoặc transcript video |
| Thuật ngữ kỹ thuật chỉ khi **cần** và có ngữ cảnh | Giả định reader đã biết cú pháp CLI |

**Ví dụ (bài Git — push lên remote):**

| | Nội dung |
|---|----------|
| ❌ Khó hiểu | `` `git clone` vs `git remote add`; `origin`; `git push -u`; Bitbucket onboarding `` |
| ✅ Dễ hiểu | Hai cách bắt đầu làm việc với repo trên server: tải bản sao về máy, hoặc gắn repo cloud với project local rồi đẩy commit lên để đồng bộ với team. |

**Ví dụ (bài Linux — phân quyền):**

| | Nội dung |
|---|----------|
| ❌ | `` `chmod` u/g/o, `chown`, setuid — Week 1 `` |
| ✅ | Cách đọc và đổi quyền file/thư mục cho user, group và others; áp dụng khi deploy script hoặc bảo vệ config. |

**Lệnh và cú pháp** thuộc **Block 2** trong `02-Knowledge`, không thuộc Summary. Executive Summary của thư mục: mô tả **phạm vi thư mục**, không liệt kê từng lệnh trong từng file con.

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
- **Summary:** tuân thủ **mục 2.3** — câu chữ dễ hiểu; **không** dùng Summary làm danh sách lệnh. Khi user phàn nàn Summary khó đọc, sửa Summary và cập nhật rule nếu cần.
- **Date:** Tự động lấy ngày hiện tại lúc tạo file.
- **Version:** Mặc định là `0.0.1` cho tất cả các file mới trong giai đoạn khởi tạo này (cho đến khi có lệnh bắt đầu thực hiện dự án).
- **Quy tắc Cập nhật Version:** MỖI LẦN có sự thay đổi, chỉnh sửa hoặc cập nhật nội dung của một file, AI BẮT BUỘC phải nâng version của file đó lên (ví dụ: từ `0.0.1` -> `0.0.2`, hoặc `0.1.0` -> `0.2.0` tùy mức độ thay đổi).
- **Status:** Mặc định là `Draft` trừ khi được yêu cầu khác.

## 5. Quy tắc ghi chép — Tài liệu kiến thức (`02-Knowledge`)

Áp dụng cho mọi file trong `02-Knowledge/` (bài học, tổng kết khóa, lab). Mục tiêu: **tài liệu tra cứu theo kỹ năng DevOps**, không phải bản ghi video hay transcript khóa học.

> Chi tiết văn phong và lab: xem thêm `[06-004]-knowledge-doc-standards.md`.

### 5.1. Mô hình tổ chức (ưu tiên kỹ năng, không mirror khóa học)

| Lớp | Vai trò | Ví dụ |
|-----|---------|--------|
| **Canonical (tham chiếu)** | Một chủ đề — một nguồn chính; cập nhật khi học thêm | `linux-filesystem-and-permissions`, `bash-scripting` |
| **Course log (tùy chọn)** | Ghi đã hoàn thành khóa/module; **chỉ link**, không lặp nội dung dài | `Course-Summaries/[02-00X]-...-course-summary.md` |
| **Archive / onboarding** | Nội dung intro trùng hoặc lịch sử; gộp hoặc đánh dấu `Outdated` | File mirror video giới thiệu khóa |

**Nguyên tắc:** Khi hai khóa học dạy cùng chủ đề (ví dụ `chmod`, `ls`, package manager), **bổ sung vào file canonical** hoặc **link chéo Doc ID** — **không** tạo file mới chỉ để chép lại nội dung cũ.

### 5.2. Cấu trúc nội dung bắt buộc (3 block)

Mỗi tài liệu kiến thức mới (hoặc khi refactor) phải có **đúng 3 phần** theo thứ tự sau:

#### Block 1 — Mô hình (Structure)

- **1 sơ đồ ASCII hoặc bảng** mô tả cấu trúc / luồng / quan hệ.
- Trả lời: *Thành phần nào tương tác với nhau? Quy tắc phân loại là gì?*
- Ví dụ: FHS (`/etc`, `/var`, `/home`); permission `u/g/o`; pipeline `cmd1 | cmd2`; 5 lớp Linux (UI → Hardware).

#### Block 2 — Lệnh & pattern (Application)

- Cheat sheet **copy-paste được**: lệnh, flag thường dùng, ví dụ ngắn.
- Nhóm theo chức năng (heading `###`), không liệt kê lan man.
- Nếu có CLI: ghi rõ **chạy ở đâu** (local / SSH server) và **prerequisite** tối thiểu (một dòng).

#### Block 3 — DevOps scenario (Use case)

- **Ít nhất 1 tình huống thực tế** gắn với công việc DevOps, ví dụ:
  - SSH vào EC2 debug log trong `/var/log`
  - Script trong CI pipeline
  - Cron backup / health check
  - Quyền file trước khi deploy service
- Có thể gộp **lab 3–5 bước** vào block này thay vì tách mục riêng (xem `[06-004]`).

### 5.3. Nội dung **không** ghi (loại bỏ mặc định)

AI và người viết **không** thêm các phần sau trừ khi user yêu cầu rõ:

| Loại nội dung | Lý do |
|---------------|--------|
| Timeline lịch sử (Bell Labs, Linus, Tux, GNU, …) | Không dùng khi vận hành |
| “Welcome to module”, learning objectives từ slide | Trùng metadata khóa học |
| Tóm tắt video nguyên văn / transcript dài | Không phải reference |
| Giới thiệu distro/OS trùng file khác | Gộp 1 lần trong canonical |
| Danh sách khóa học YouTube/Coursera khuyến nghị | Thuộc roadmap, không thuộc bài học |

**Thay thế phần “Tổng quan” cũ:** tối đa **2–3 câu** — *tại sao chủ đề này quan trọng với DevOps* (server, container, CI/CD), **không** kể lịch sử hệ điều hành.

### 5.4. Trùng lặp giữa các tuần / khóa học

Khi phát hiện chủ đề đã có file (cùng Doc prefix `02-`):

1. **Đọc file hiện có** trước khi tạo mới.
2. **Ưu tiên bản sâu hơn** làm canonical (ví dụ Week 1–2 cho boot/systemd/package; Week 3 cho shell/pipe/cron).
3. File mới từ khóa sau: chỉ thêm **điểm mới** hoặc **ví dụ DevOps** — phần còn lại dùng link: `*(Chi tiết: [02-038].)*`
4. File mirror khóa cũ: đặt `Status: Outdated` trong Document Information và link tới canonical.

### 5.5. Hướng dẫn cho AI (Knowledge)

- **Tạo file mới trong `02-Knowledge`:** áp dụng mục 2 (Document Information, **gồm Summary mục 2.3**) + mục 5 (3 block + loại trừ).
- **Summary file kiến thức:** trả lời *“Học xong bài này, tôi làm được gì / hiểu gì?”* — không trả lời *“Bài này có những lệnh nào?”*.
- **Ghi chép từ khóa Coursera/video:** không tạo 1 file/video; gộp theo **chủ đề kỹ năng** hoặc cập nhật file canonical.
- **Course summary (`Course-Summaries/`):** checklist module đã học + link Doc ID; **không** copy nội dung dài từ bài học.
- **Refactor:** khi user hoàn thành khóa, đề xuất gộp/archive phần intro trùng thay vì nhân bản file.

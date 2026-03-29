# Quy chuẩn Template Daily Log (Nhật ký hàng ngày)

## Document Information
- **Doc ID:** 06-003
- **Summary:** Template chuẩn để tạo các file nhật ký học tập hàng ngày (Daily Log) trong thư mục 01-Journal.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Mục đích
File này chứa template chuẩn cho việc ghi chép nhật ký học tập (Daily Log). Các file Daily Log sẽ được lưu trữ trong thư mục `01-Journal/Daily/[YYYY]/[MM-Tháng]/`.

## 2. Hướng dẫn cho AI
Khi người dùng yêu cầu "Tạo file daily cho hôm nay", AI BẮT BUỘC phải:
1. Tạo thư mục theo cấu trúc thời gian nếu chưa có (VD: `01-Journal/Daily/2026/03-March/`).
2. Tạo file với tên theo format: `[01-XXX]-[YYYY-MM-DD].md` (VD: `[01-001]-2026-03-27.md`).
3. Copy nguyên văn nội dung template ở mục 3 vào file mới và tự động điền các thông tin (Doc ID, Date).

## 3. Template Bắt Buộc (Mẫu cần copy)

````markdown
# Daily Log: [YYYY-MM-DD]

## Document Information
- **Doc ID:** [01-XXX]
- **Summary:** Nhật ký học tập và làm việc ngày [YYYY-MM-DD].
- **Version:** 0.0.1
- **Date:** [YYYY-MM-DD]
- **PRD Reference:** N/A
- **Status:** Draft

---

## 🎯 1. Mục tiêu hôm nay (Goals)
- [ ] [Ghi mục tiêu 1...]
- [ ] [Ghi mục tiêu 2...]

## 🧠 2. Kiến thức học được (Learnings)
- [Ghi chú nhanh các khái niệm mới học được...]

## 🐛 3. Lỗi gặp phải & Cách giải quyết (Bugs & Fixes)
- **Lỗi:** [Paste log lỗi hoặc mô tả lỗi]
- **Nguyên nhân:** [Tại sao lại lỗi?]
- **Cách fix:** [Làm sao để sửa?]

## 📝 4. Ghi chú nháp / Ý tưởng (Scratchpad)
- [Vứt mọi thứ linh tinh, link bài viết, ý tưởng xẹt qua đầu vào đây...]
````
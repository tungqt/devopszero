# Tìm trợ giúp cho lệnh Linux (Getting Help for Linux Commands)

## Document Information
- **Doc ID:** 02-054
- **Summary:** Bài đọc về cách **tra cứu** lệnh Linux: **`man`** (kể cả `man -k .`, cấu trúc trang man); **TLDR** (`npm install -g tldr`, `tldr`); **Stack Overflow** và **Stack Exchange** (Unix & Linux, Ask Ubuntu, Server Fault); tìm kiếm web; cheat sheet khóa học; **Wikipedia** danh sách lệnh Unix.
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Thử nghiệm lệnh trên terminal rất có giá trị, nhưng nhiều thứ **không thể** chỉ suy ra bằng cách mò. Bạn cần biết **có những gì**, **làm được gì**, và **đi đâu** để có câu trả lời. Bài đọc này tóm tắt các cách **tìm thông tin** hữu ích.

*(Trong khóa học, nếu có liên kết ra ngoài: có thể **chuột phải** → **Open in new tab** để mở tab mới.)*

## 1. Dùng lệnh `man` có sẵn

**`man`** (*manual*) là cách **chuẩn** trên hệ kiểu Unix để xem trợ giúp cho lệnh từ dòng lệnh. Hệ thống manual đã phát triển từ **1971**.

Liệt kê các lệnh có trang man (kèm mô tả ngắn):

```bash
man -k .
```

Xem trang man của một lệnh:

```bash
man ten_lenh
```

Các trang man thường chia **mục**, ví dụ:

| Mục | Nội dung |
|-----|----------|
| **NAME** | Tên lệnh/tính năng và mô tả rất ngắn. |
| **SYNOPSIS** | Cú pháp tóm tắt, tùy chọn và đối số. |
| **DESCRIPTION** | Mô tả chi tiết hơn về chức năng và hành vi. |
| **OPTIONS** | Các tùy chọn và đối số có thể dùng. |
| **EXAMPLES** | Một số ví dụ sử dụng. |
| **SEE ALSO** | Lệnh/tài liệu liên quan. |

Có thể còn các mục khác, ví dụ: **EXIT STATUS**, **RETURN VALUE**, **ENVIRONMENT**, **BUGS**, **FILES**, **AUTHOR**, **REPORTING BUGS**, **HISTORY**, **COPYRIGHT**.

## 2. Cài và dùng lệnh `tldr` (TLDR Pages)

**[TLDR Pages](https://tldr.sh/)** (dự án: [tldr-pages trên GitHub](https://github.com/tldr-pages/tldr)) là tài liệu **miễn phí, mã nguồn mở**, cộng tác xây dựng — mục tiêu là tài liệu **dễ tiếp cận** hơn so với man truyền thống (man thường **dài và chi tiết**).

**TLDR** (*Too Long; Didn't Read*) — hay gọi tắt là **tldr** — tập trung **ví dụ** cho các tình huống dùng lệnh thường gặp; bố cục gần với **cheatsheet**.

Cài công cụ dòng lệnh (theo tài liệu khóa học — cần **Node.js** / **npm**):

```bash
npm install -g tldr
```

Sau khi cài:

```bash
tldr ten_lenh
```

Công cụ hiển thị **tóm tắt ngắn**, dễ hiểu, kèm **ví dụ**.

*(Trên một số distro có thể cài `tldr` qua package manager thay vì npm — tùy môi trường.)*

## 3. Tìm trên Stack Overflow

**[Stack Overflow](https://stackoverflow.com/)** là nền tảng **hỏi–đáp** do cộng đồng xây dựng, phổ biến với lập trình viên, nhà phát triển và quản trị hệ thống; có rất nhiều nội dung về **Linux** và **dòng lệnh**.

*   Dùng thanh tìm kiếm: gõ **tên lệnh** + **từ khóa** / tham số cụ thể.
*   Có thể thêm **tag** như `linux`, `command-line` để thu hẹp.
*   Duyệt câu hỏi/đáp phù hợp; nếu không có, có thể **đặt câu hỏi mới** (tuân theo quy tắc cộng đồng).

**Lưu ý:** xem **ngày** của câu trả lời để đảm bảo thông tin **còn đúng** với phiên bản hiện tại; đọc **bình luận** và thảo luận để hiểu **ngữ cảnh**, rủi ro hoặc giới hạn.

Câu hỏi mới nhất có tag **Linux**: [stackoverflow.com/questions/tagged/linux](https://stackoverflow.com/questions/tagged/linux)

## 4. Tìm trên Stack Exchange

**Stack Exchange** là **mạng lưới** các site hỏi–đáp tương tự Stack Overflow nhưng **rộng chủ đề** hơn (không chỉ lập trình). Một số site liên quan **Linux** và **mã nguồn mở**:

*   **Unix & Linux**
*   **Ask Ubuntu**
*   **Server Fault**

Vào đúng **cộng đồng**, dùng thanh tìm kiếm với tên lệnh và từ khóa.

**Unix & Linux** trên Stack Exchange: [unix.stackexchange.com](https://unix.stackexchange.com/)

## 5. “Cứ Google!”

Công cụ tìm kiếm (ví dụ **Google**) mạnh nếu bạn biết **cách gõ truy vấn** và **lọc kết quả** — ví dụ thêm *Wikipedia*, *Stack Overflow*, hoặc *Linux* vào câu hỏi.

**Cảnh báo:** đừng **tin mù quáng** mọi thứ trên mạng; nhiều nội dung **cũ, sai, hoặc gây nhiễu** — luôn **đối chiếu** với tài liệu chính thức hoặc man page khi làm việc quan trọng.

## 6. Dùng cheat sheet trong khóa học

Trong khóa học sẽ có **cheat sheet** — tóm tắt kiến thức thành **tài liệu tra cứu nhanh**. Hữu ích để **ôn** và hỗ trợ **bài tập được chấm điểm**.

## 7. Wikipedia: danh sách lệnh Unix

Wikipedia duy trì **danh sách lệnh** trên hệ Unix kèm **mô tả ngắn** — tiện tra cứu nhanh: [en.wikipedia.org/wiki/List_of_Unix_commands](https://en.wikipedia.org/wiki/List_of_Unix_commands)

## Tổng kết (Summary)

Bài đọc này đã tóm tắt cách **tìm thêm thông tin** về lệnh Linux: **`man`**, **TLDR (`tldr`)**, **Stack Overflow**, **Stack Exchange**, tìm kiếm web, **cheat sheet** của khóa học, và **Wikipedia**.

Càng dùng lệnh, bạn càng **quen các mẫu** lặp lại; theo thời gian thao tác trở nên **tự nhiên** nhờ **trí nhớ cơ** (*muscle memory*).

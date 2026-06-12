# Tạo và chỉnh sửa tập tin văn bản (Creating and Editing Text Files)

## Document Information
- **Doc ID:** 02-050
- **Summary:** Bài học giới thiệu trình soạn thảo văn bản trên Linux: phân loại CLI vs GUI; **gedit** (GNOME); **GNU nano**, **vi/vim**; tính năng và thao tác cơ bản trong nano (mở file, Ctrl+G, Ctrl+W tìm kiếm); vim với **Insert mode** / **Command mode**, lưu (`:w`), thoát (`:q`, `:q!`). Nhắc tới **emacs** như trình soạn lâu đời, dùng được GUI hoặc dòng lệnh.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Tạo và chỉnh sửa tập tin văn bản". Sau khi xem video này, bạn sẽ có thể:

*   Liệt kê các **trình soạn thảo văn bản phổ biến** trên Linux.
*   Mô tả một **trình soạn thảo dựa trên GUI** phổ biến.
*   Dùng các **trình soạn thảo dòng lệnh** để làm việc với tập tin.

## Vai trò của trình soạn thảo văn bản

Bạn dùng trình soạn thảo văn bản để **viết mã (code)** trong môi trường Linux.

Có nhiều trình soạn thảo; có thể gom thành **hai nhóm chính**:

1. **Trình soạn thảo dòng lệnh (command-line text editors)**
2. **Trình soạn thảo GUI (GUI text editors)**

## Ví dụ trình soạn thảo dòng lệnh

*   **GNU nano** — trình soạn nhỏ, thân thiện, **không phân chế độ (modeless)**.
*   **vi** — trình soạn dòng lệnh truyền thống, ban đầu cho Unix.
*   **vim** — trình soạn dòng lệnh **theo chế độ (mode-based)** rất mạnh, dựa trên **vi**.

## Ví dụ trình soạn thảo GUI và emacs

*   **gedit** — trình soạn **GUI** phổ biến, là trình soạn mặc định trong môi trường **GNOME**.
*   **emacs** (đọc tương tự *e-max*) — một trong các dự án mã nguồn mở, miễn phí **lâu đời** vẫn đang phát triển. Bạn có thể dùng emacs ở **chế độ GUI** hoặc **trong terminal**.

## gedit (GUI)

**gedit** là trình soạn hiện đại, phổ biến, thường **cài sẵn** trên hầu hết các distro Linux.

*   Thiết kế **đa mục đích**; theo triết lý GNOME, gedit nhấn mạnh **đơn giản và dễ dùng**, giao diện **sạch, gọn**.

**Một số tính năng:**

*   Trình duyệt tập tin **tích hợp** (integrated file browser).
*   **Undo / Redo**.
*   **Tìm và thay thế**, hỗ trợ **biểu thức chính quy (regular expressions)** trong chuỗi tìm kiếm.
*   **Mở rộng bằng plugin** từ gói `gedit-plugins`.

Trình soạn dùng **tô màu cú pháp (syntax color coding)** để bạn đọc code và tập trung dễ hơn.

## GNU nano (dòng lệnh)

**GNU nano** là trình soạn dòng lệnh cung cấp:

*   Undo và Redo.
*   Tìm và thay thế (có hỗ trợ regex trong chuỗi tìm).
*   **Syntax highlighting**.
*   **Thụt lề tự động** cho mã.
*   **Đánh số dòng**.
*   Cuộn **theo từng dòng**.
*   **Nhiều buffer** — làm việc với nhiều file cùng lúc.

### Mở file trong nano

```bash
nano ten-file
```

Lệnh này mở cửa sổ soạn thảo; bạn có thể chỉnh sửa nội dung.

### Giao diện và thao tác cơ bản

*   **Vùng chính** hiển thị nội dung file đang mở.
*   **Con trỏ** có thể di chuyển bằng phím mũi tên, **Page Up / Page Down**, hoặc **Home / End**.
*   Gõ chữ sẽ chèn tại con trỏ; **Delete** và **Backspace** để xóa.
*   **Enter** bắt đầu dòng mới.

**Cuối cửa sổ nano** có danh sách lệnh: thực hiện bằng cách giữ **Control (Ctrl)** và nhấn chữ tương ứng.

*   Ví dụ **trợ giúp**: **Ctrl+G** (*Get Help*).

### Tìm chuỗi (Where Is)

*   Nhấn **Ctrl+W** để dùng tùy chọn **Where Is** (tìm chuỗi).
*   Một **khung/pane** mới xuất hiện ở cuối cửa sổ; trong ngoặc vuông có thể thấy **chuỗi tìm gần nhất** (ví dụ `1999`).
*   Gõ chuỗi cần tìm (ví dụ `https`) và nhấn **Enter** — con trỏ nhảy tới **lần xuất hiện đầu tiên** sau vị trí con trỏ hiện tại.

Nano còn nhiều tính năng khác; bạn sẽ thực hành thêm trong **lab** của khóa học.

## Vim (dòng lệnh)

**Vim** là trình soạn dòng lệnh truyền thống và **rất mạnh**. Cần thời gian làm quen; khi quen, thao tác có thể rất nhanh nhờ **cơ bắp tay (muscle memory)**.

Khởi động:

```bash
vim
```

hoặc chỉ định file:

```bash
vim ten-file
```

### Hai chế độ cơ bản

1. **Insert mode** — nhập và sửa văn bản trực tiếp.
2. **Command mode** — di chuyển, tìm kiếm, sao chép/dán, lưu file, thoát, v.v.

Sau khi mở vim, mặc định thường là **Command mode**. Nhấn **`i`** để vào **Insert mode**. Gõ nội dung (ví dụ `some text`), rồi nhấn **Escape** để **thoát Insert mode** và quay lại **Command mode**. Văn bản được ghi vào **buffer** tại vị trí con trỏ.

### Lưu và thoát (tóm tắt)

Các lệnh sau gõ ở **Command mode**, thường bắt đầu bằng **`:`** (dấu hai chấm):

*   **Lưu với tên file** (file mới hoặc đổi tên khi lưu): `:w example.txt`  
  Buffer được ghi ra file; vim có thể hiển thị thông báo (tên file, số dòng/cột, v.v.).
*   **Ghi thay đổi** vào file đã tồn tại: **`:w`** (write).
*   **Thoát**: **`:q`** (quit).
*   **Thoát và bỏ thay đổi** kể từ lần ghi cuối: **`:q!`** (*quit with bang* — dấu chấm than `!`).

Đây chỉ là **giới thiệu rất ngắn**; vim có rất nhiều lệnh để duyệt buffer, tìm kiếm, copy/paste, di chuyển khối văn bản. Bạn sẽ thực hành thêm trong **lab** sau.

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   Có nhiều trình soạn **dòng lệnh** hoặc **GUI** để làm việc với mã trên Linux.
*   **gedit** là trình soạn GUI với nhiều tính năng giúp công việc đơn giản hơn.
*   **GNU nano** là trình soạn dòng lệnh với chức năng tương tự ở định dạng terminal.
*   **vim** là trình soạn dòng lệnh dùng **Insert mode** để nhập dữ liệu và **Command mode** để thao tác với file.

Bạn đã thấy cách dùng trình soạn để làm việc với file có sẵn; điều này hữu ích khi học tiếp các lệnh Linux để **tạo file** và **nối thêm (append)** văn bản.

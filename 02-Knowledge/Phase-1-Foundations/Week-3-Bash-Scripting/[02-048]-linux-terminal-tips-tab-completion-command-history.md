# Mẹo Terminal Linux: Tab completion và lịch sử lệnh (Linux Terminal Tips)

## Document Information
- **Doc ID:** 02-048
- **Summary:** Bài đọc về **tab completion** (tự hoàn thành lệnh, tên tập tin, thư mục khi có khớp duy nhất; gõ Tab hai lần khi có nhiều khả năng) và **command history** (phím mũi tên lên/xuống để gọi lại lệnh đã gõ; chỉ các lệnh được nhập mới nằm trong lịch sử, không tính dòng output).
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

## Mục tiêu học tập (Learning Objectives)

Sau khi hoàn thành bài đọc này, bạn sẽ có thể:

*   Dùng **tab completion** để tự hoàn thành lệnh (và tên tập tin / thư mục).
*   Dùng **command history** để nhanh chóng gọi lại các lệnh trước đó.

## Tab completion trong Shell

Hầu hết các môi trường shell hiện đại hỗ trợ tính năng gọi là **tab completion**. Tính năng này giúp bạn **nhanh chóng và hiệu quả** tự hoàn thành lệnh, tên tập tin hoặc tên thư mục trong lúc gõ ở terminal.

### Tab completion là gì?

**Tab completion** cho phép bạn nhấn phím **Tab** trong khi đang gõ một lệnh hoặc đường dẫn tập tin để shell **tự điền phần còn lại của từ** — **miễn là** chỉ có **một khớp duy nhất** (unique match).

Nếu có **nhiều khả năng** khớp, nhấn **Tab hai lần** thường sẽ khiến shell **hiển thị tất cả các lựa chọn** có sẵn (tùy cấu hình shell).

### Ví dụ

Giả sử bạn đang ở thư mục home (`~`), trong đó có các thư mục con:

*   `Pictures`
*   `Videos`
*   `Documents`
*   `Downloads`

Và trong `Documents` chỉ có **một** thư mục con:

*   `python-examples`

**Bước 1 — một khớp bắt đầu bằng `P`:**

Bạn gõ:

```bash
cd P
```

và nhấn **Tab**, shell sẽ tự hoàn thành thành:

```bash
cd Pictures/
```

vì `Pictures` là **thư mục duy nhất** trong thư mục hiện tại có tên bắt đầu bằng chữ **P**.

### Tab completion với đường dẫn dài

Bạn cũng có thể dùng tab completion cho **đường dẫn dài hơn**.

Nếu bạn gõ:

```bash
cd Do
```

và nhấn **Tab**, có thể **không có gì xảy ra** vì trong thư mục hiện tại có **hơn một** thư mục bắt đầu bằng **`Do`** — shell không biết nên hoàn thành thành `Documents` hay `Downloads`.

Ngược lại, nếu bạn gõ:

```bash
cd Doc
```

và nhấn **Tab**, shell sẽ hoàn thành thành:

```bash
cd Documents/
```

vì chỉ có **một** thư mục bắt đầu bằng **`Doc`**: `Documents`.

Nếu bạn nhấn **Tab** lần nữa (khi đã ở trong ngữ cảnh đúng), shell có thể tiếp tục hoàn thành thành:

```bash
cd Documents/python-examples/
```

vì `python-examples` là **thư mục / mục duy nhất** tồn tại trong `~/Documents`. Chỉ với vài lần gõ và Tab, bạn có thể hoàn thành nhanh các đường dẫn dài trên dòng lệnh.

## Command history (Lịch sử lệnh)

**Command history** cho phép bạn duyệt các lệnh đã gõ trước đó bằng phím **mũi tên lên (Up Arrow)** và **mũi tên xuống (Down Arrow)**.

Giả sử bạn có script Python tên `myprogram.py` trong thư mục `python-examples`, chỉ in `Hello, World!` khi chạy. Bạn vừa gõ chuỗi lệnh sau và shell đang chờ lệnh tiếp theo:

```text
~ $ cd ~/Documents/python-examples
~/Documents/python-examples $ python3 myprogram.py
Hello, World!
~/Documents/python-examples $ cd /
/ $
```

### Chạy lại lệnh cuối cùng

Nếu bạn nhấn **Up Arrow** **một lần**, shell sẽ tự chèn **lệnh cuối cùng** bạn đã gõ (ở đây là `cd /`) lên dòng lệnh — bạn **không cần gõ lại** toàn bộ.

Lưu ý: dòng in ra `Hello, World!` **không phải** là một lệnh; lịch sử chỉ theo các **lệnh bạn nhập và gửi đi** (thường là sau khi nhấn Enter).

### Chạy lại lệnh cũ hơn trong phiên

Nếu bạn nhấn **Up Arrow** thêm **hai lần nữa** (tức là **ba lần** kể từ dòng trống tại `/ $`), shell sẽ chèn lệnh bạn đã chạy **ba lệnh trước đó** trong lịch sử lệnh đã gõ — trong ví dụ khóa học đó là:

```bash
cd ~/Documents/python-examples
```

Một lần nữa: dòng output `Hello, World!` **không được tính** là một mục trong command history. Lịch sử chỉ đi qua **các lệnh bạn đã nhập**, không phải mọi dòng hiển thị trong terminal.

**Mẹo:** Nếu bạn nhấn **Up Arrow** quá nhiều lần, dùng **Down Arrow** để duyệt lịch sử theo **hướng ngược lại**.

Nhấn **Enter** để quay lại thư mục `~/Documents/python-examples` khi dòng lệnh đã hiển thị đúng lệnh đó.

## Tổng kết (Summary)

Chúc mừng! Bạn đã biết thêm vài phím tắt tiện lợi để **tăng tốc** tương tác trên dòng lệnh.

Trong bài đọc này, bạn đã học cách:

*   Dùng **tab completion** để tự hoàn thành lệnh (và đường dẫn).
*   Dùng **command history** (mũi tên lên / xuống) để nhanh chóng gọi lại các lệnh trước đó.

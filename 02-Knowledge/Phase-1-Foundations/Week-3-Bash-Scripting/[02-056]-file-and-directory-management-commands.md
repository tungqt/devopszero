# Lệnh quản lý tập tin và thư mục (File and Directory Management Commands)

## Document Information
- **Doc ID:** 02-056
- **Summary:** **`mkdir`** tạo thư mục; **`rm`** / **`rm -r`** xóa tập tin và cây thư mục (cảnh báo); **`rmdir`** xóa thư mục **rỗng**; **`touch`** tạo file rỗng hoặc cập nhật thời gian sửa; **`cp`** / **`cp -r`** sao chép; **`mv`** di chuyển hoặc đổi tên; **`chmod`** (ví dụ **`+x`**) quyền thực thi.
- **Version:** 0.0.1
- **Date:** 2026-05-15
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lệnh quản lý tập tin và thư mục". Sau khi xem video này, bạn sẽ có thể:

*   **Tạo** và **xóa** tập tin và thư mục.
*   **Sao chép** và **di chuyển** tập tin và thư mục.
*   **Quản lý quyền** tập tin để có thể **thực thi** (ví dụ script).

## Tạo thư mục: `mkdir`

**`mkdir`** (*make directory*) dùng để **tạo thư mục**.

Ví dụ đang ở thư mục **Documents** (đang trống), muốn tạo thư mục **`test`**:

```bash
mkdir test
```

Sau đó thư mục hiện tại có **thư mục con** tên `test`.

## Xóa tập tin và thư mục: `rm`

**`rm`** (*remove*) xóa **tập tin** hoặc **thư mục** (tùy cách dùng).

*   Đang ở **Documents**, muốn xóa tập tin **`file1`**:

```bash
rm file1
```

*   **Không** thể chỉ `rm folder1` nếu thư mục **còn chứa** tập tin con (theo mô tả video — shell sẽ từ chối hoặc không xóa hết tùy hệ; thông điệp chính là cần tùy chọn đệ quy).

Để xóa **thư mục** cùng **toàn bộ** tập tin và thư mục con:

```bash
rm -r folder1
```

*   **`-r`** (*recursive*): xóa thư mục và **mọi đối tượng con**.

Sau khi xóa hết ví dụ trong video, thư mục Documents **trống**.

### Cảnh báo

Luôn **cẩn trọng** với **`rm -r`**: rất dễ **xóa nhầm** thư mục chứa dữ liệu quan trọng.

### Xóa thư mục rỗng: `rmdir` (khuyến nghị thay vì `rm -rf` cho trường hợp rỗng)

Nếu đã tạo thư mục **rỗng** bằng `mkdir` rồi muốn **gỡ** nó, video **không khuyến nghị** dùng **`rm -rf`** cho mục đích này.

Thay vào đó, dùng **`rmdir`** — chỉ dùng để **xóa thư mục rỗng**:

```bash
rmdir ten_thu_muc
```

*   Giúp **tránh** vô tình xóa nhầm tập tin/thư mục có nội dung (vì thư mục **không rỗng** thì `rmdir` sẽ **thất bại**).

Kiểm tra: **`ls`** — thư mục hiện tại **trống** như mong đợi.

## Tạo / cập nhật tập tin: `touch`

**`touch`** có thể:

1. **Tạo** các tập tin **rỗng** mới.  
2. Với tập tin **đã tồn tại**: **cập nhật** thời điểm **sửa đổi lần cuối** (*last-modified*) lên thời điểm hiện tại.

Ví dụ trong Documents trống, tạo bốn file văn bản rỗng:

```bash
touch a.txt b.txt c.txt d.txt
```

**`touch`** với file đã có — ví dụ **`notes.txt`**: thời gian sửa cuối được **làm mới**.

Trong video gợi ý xem thời điểm sửa cuối trước khi `touch`:

```bash
date -r notes.txt
```

*(Trên Linux với **GNU date**, `-r file` in thời điểm sửa đổi của file. Trên macOS, `date` có thể khác; có thể dùng **`stat notes.txt`** để xem mtime.)*

## Sao chép: `cp`

**`cp`** (*copy*) sao chép **tập tin** hoặc **thư mục**.

**Sao chép tập tin:**

*   Chỉ rõ **tên file đích** trong thư mục đích, **hoặc**
*   **Bỏ** tên file đích — mặc định **giữ nguyên tên** file ở nơi đến.

**Sao chép cả thư mục** (đệ quy): cần **`-r`** để `cp` sao chép **mọi** thư mục con và tập tin.

Ví dụ:

*   Có **`notes.txt`** và thư mục **`Documents`** trong thư mục làm việc. Sao chép file vào Documents:

```bash
cp notes.txt Documents
```

*   Không cần ghi **thư mục nguồn** cho file nếu file nằm ở **thư mục hiện tại** — `cp` mặc định lấy từ **`.`**.

*   Sao chép **cả** thư mục `Documents` thành bản sao tên **`Docs_copy`** (cú pháp kiểu `cp -r Documents Docs_copy`):

```bash
cp -r Documents Docs_copy
```

*(Tên thư mục đích trong video có khoảng trắng thừa ở “Docs_copy ” — khi gõ lệnh nên **không** có khoảng thừa ở cuối tên.)*

## Di chuyển / đổi tên: `mv`

**`mv`** (*move*) di chuyển **tập tin** hoặc **thư mục** (và thường dùng để **đổi tên** trong cùng một thư mục cha).

*   **Di chuyển tập tin:** `mv` + các đường dẫn tập tin cần chuyển + **thư mục đích** (hoặc đích cuối).
*   **Di chuyển thư mục:** `mv` + đường dẫn thư mục cần chuyển + đích.

Ví dụ: có **`my_script.sh`** và các thư mục **`Scripts`**, **`Notes`**, **`Documents`**.

Chuyển script vào **Scripts**:

```bash
mv my_script.sh Scripts
```

*   `ls my_script.sh` ở chỗ cũ có thể **không** còn thấy file; `ls Scripts` thấy **`my_script.sh`** trong Scripts.

Chuyển **cả** thư mục **Notes** và **Scripts** vào **Documents** (một lệnh `mv` nhiều nguồn — đích cuối là thư mục):

```bash
mv Scripts Notes Documents
```

*(Thứ tự đối số: các mục cần chuyển trước, **thư mục đích** cuối cùng.)*

Sau đó ở cấp trên có thể chỉ còn **Documents**; bên trong Documents có **Scripts** và **Notes**.

## Quyền tập tin: `chmod` (ví dụ thực thi script)

**`chmod`** (*change mode*) đổi **quyền đọc, ghi, thực thi** trên tập tin (và thư mục).

Ví dụ script **`my_script.sh`** in dòng kiểu *Learning Linux is fun!*

```bash
ls -l my_script.sh
```

*   Thấy quyền **đọc/ghi** (`r`, `w`) nhưng **chưa** có quyền **thực thi** — chạy trực tiếp có thể báo **Permission denied**.

Thêm quyền **thực thi** cho chủ sở hữu (và theo video, dùng **`+x`**):

```bash
chmod +x my_script.sh
```

*   `ls -l my_script.sh` lần nữa — thấy ký tự **`x`** (execute).
*   Chạy script (ví dụ `./my_script.sh`) **thành công**.

*(Chi tiết ký hiệu số và symbolic mode: xem `man chmod`.)*

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   **`touch`** tạo tập tin **mới** (rỗng) hoặc **cập nhật** thời gian sửa đổi cuối của tập tin **đã có**.
*   **`mkdir`** tạo thư mục; **`rmdir`** xóa thư mục **rỗng** — an toàn hơn so với dùng **`rm -rf`** cho việc chỉ “gỡ thư mục trống”.
*   **`cp`** và **`mv`** dùng để **sao chép**, **di chuyển**, và **đổi tên** tập tin/thư mục; **`cp -r`** cho **cây thư mục**.
*   **`chmod`** đổi quyền **đọc, ghi, thực thi** — ví dụ **`+x`** để chạy được shell script.

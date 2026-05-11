# Paths và Biến môi trường PATH trong Linux

## Document Information
- **Doc ID:** 02-007
- **Summary:** Giải thích chi tiết về biến môi trường `$PATH`, cách Linux tìm kiếm lệnh để thực thi, sự khác biệt giữa đường dẫn tuyệt đối/tương đối và cách cấu hình `CDPATH`.
- **Version:** 0.0.1
- **Date:** 2026-05-06
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan về Biến môi trường `$PATH`

Khi bạn gõ một lệnh như `ls`, `fdisk`, hay `cat` vào Terminal và nhấn Enter, làm sao Linux biết chương trình đó nằm ở đâu trên ổ cứng để chạy? Câu trả lời nằm ở biến môi trường **`$PATH`**.

### 1.1. `$PATH` là gì?
`$PATH` là một danh sách các thư mục mà hệ điều hành sẽ tìm kiếm mỗi khi bạn gõ một lệnh. Các thư mục trong danh sách này được ngăn cách nhau bởi dấu hai chấm (`:`).

Để xem `$PATH` hiện tại của bạn, gõ lệnh:
```bash
echo $PATH
```

**Output mẫu:**
```text
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/student/.local/bin:/home/student/bin
```

### 1.2. Cơ chế tìm kiếm (Từ trái sang phải)
Khi bạn gõ lệnh `emacs`, Linux sẽ làm như sau:
1. Tìm trong `/usr/local/bin/`. Nếu có file tên `emacs` ở đây, nó sẽ chạy ngay lập tức và dừng tìm kiếm.
2. Nếu không có, nó tìm tiếp trong `/usr/bin/`.
3. Cứ tiếp tục như vậy từ trái sang phải.
4. Nếu tìm hết danh sách mà vẫn không thấy, nó sẽ báo lỗi `command not found`.

> 📌 **Lưu ý bảo mật/Ghi đè:** Nếu bạn có 2 phiên bản của cùng một phần mềm (ví dụ: `emacs` cũ nằm ở `/usr/bin/` và `emacs` mới bạn tự cài nằm ở `/usr/local/bin/`), Linux sẽ chạy bản mới ở `/usr/local/bin/` vì thư mục đó đứng trước trong danh sách `$PATH`.

Bạn có thể dùng lệnh `which` để xem chính xác Linux đang chạy file thực thi từ thư mục nào:
```bash
which emacs
# Output: /usr/bin/emacs
```

---

## 2. Tại sao phải gõ `./` khi chạy file trong thư mục hiện tại?

Một nguyên tắc bảo mật tối quan trọng của Linux: **Thư mục hiện tại (current directory) KHÔNG BAO GIỜ được đưa vào `$PATH` mặc định.**

Giả sử bạn viết một script tên là `foobar.sh` và đang đứng ngay trong thư mục chứa nó. Nếu bạn gõ:
```bash
foobar.sh
```
Linux sẽ báo `command not found`. Tại sao? Vì nó chỉ tìm trong các thư mục của `$PATH` (như `/usr/bin`), chứ nó không tìm ở chỗ bạn đang đứng.

Để ép Linux chạy file ở thư mục hiện tại, bạn phải chỉ định rõ đường dẫn bằng `./` (dấu chấm đại diện cho thư mục hiện tại):
```bash
./foobar.sh
```

> 🛡️ **Lý do bảo mật:** Giả sử hacker tạo một file mã độc tên là `ls` và để trong thư mục `/tmp`. Nếu thư mục hiện tại có trong `$PATH`, khi bạn `cd` vào `/tmp` và gõ `ls`, bạn sẽ vô tình chạy mã độc của hacker thay vì lệnh `ls` thật của hệ thống.

---

## 3. Cách thêm thư mục mới vào `$PATH`

Khi bạn cài đặt các công cụ mới (như Node.js, Go, Terraform) vào thư mục cá nhân, bạn cần thêm thư mục đó vào `$PATH` để có thể gõ lệnh ở bất cứ đâu.

Giả sử bạn có thư mục chứa công cụ tại: `$HOME/my_bin_dir`

### 3.1. Thêm vào ĐẦU danh sách (Ưu tiên cao nhất)
Dùng cách này nếu bạn muốn lệnh của bạn ghi đè (override) lệnh mặc định của hệ thống:
```bash
export PATH=$HOME/my_bin_dir:$PATH
```

### 3.2. Thêm vào CUỐI danh sách (Ưu tiên thấp nhất)
Dùng cách này nếu bạn chỉ muốn thêm lệnh mới, không muốn ảnh hưởng đến lệnh hệ thống:
```bash
export PATH=$PATH:$HOME/my_bin_dir
```

### 3.3. Lưu `$PATH` vĩnh viễn
Lệnh `export` ở trên chỉ có tác dụng trong phiên làm việc hiện tại. Khi bạn tắt Terminal, nó sẽ mất.
Để lưu vĩnh viễn, bạn phải thêm dòng `export` đó vào file cấu hình khởi tạo của Shell (thường là `~/.bashrc` hoặc `~/.zshrc` nếu dùng Zsh).

```bash
echo 'export PATH=$HOME/my_bin_dir:$PATH' >> ~/.bashrc
source ~/.bashrc  # Nạp lại cấu hình ngay lập tức
```

---

## 4. Đường dẫn Tuyệt đối (Absolute) vs Tương đối (Relative)

Mọi đường dẫn trong Linux đều thuộc 1 trong 2 loại này:

- **Đường dẫn tuyệt đối (Absolute Path):** 
  - LUÔN LUÔN bắt đầu bằng dấu gạch chéo `/` (đại diện cho thư mục gốc - root).
  - Nó chỉ định vị trí chính xác trên ổ cứng, không phụ thuộc vào việc bạn đang đứng ở đâu.
  - Ví dụ: `/var/log/syslog`, `/home/student/bin`.

- **Đường dẫn tương đối (Relative Path):**
  - KHÔNG bắt đầu bằng dấu `/`.
  - Nó tính toán vị trí dựa trên **thư mục hiện tại** bạn đang đứng.
  - Ký hiệu đặc biệt:
    - `./` : Thư mục hiện tại.
    - `../` : Thư mục cha (lùi lên 1 cấp).
  - Ví dụ: Nếu bạn đang ở `/var/log`, thì gõ `cat syslog` (tương đối) sẽ tương đương với `cat /var/log/syslog` (tuyệt đối).

---

## 5. Mẹo nâng cao: Biến `CDPATH`

Tương tự như `$PATH` dùng để tìm lệnh, biến `$CDPATH` dùng để tìm thư mục khi bạn dùng lệnh `cd`.

Giả sử bạn thường xuyên làm việc với các thư mục bên trong `/usr`. Bình thường, nếu bạn đang ở `/home` và gõ `cd bin`, Linux sẽ báo lỗi vì không có thư mục `bin` nào trong `/home`.

Nhưng nếu bạn cấu hình `CDPATH`:
```bash
export CDPATH=/usr:$CDPATH
```

Bây giờ, dù đang đứng ở đâu, nếu bạn gõ `cd bin`, Linux sẽ tự động tìm trong `/usr` và chuyển bạn đến `/usr/bin`.

## 6. Bài tập thực hành (Lab): Hiểu rõ rủi ro bảo mật của `$PATH`

Bài tập này sẽ mô phỏng cách một hacker có thể tạo ra một "Trojan horse" (ngựa gỗ thành Troy) bằng cách lợi dụng biến `$PATH`.

**Bước 1: Tạo một lệnh `ls` giả mạo**
Di chuyển vào thư mục tạm `/tmp` và tạo một file thực thi có tên là `ls`:
```bash
cd /tmp
echo 'echo "Hello, This is MY ls program"' > ls
chmod +x ls
```

**Bước 2: Chạy thử lệnh giả mạo**
Nếu bạn muốn chạy file vừa tạo trong thư mục hiện tại, bạn phải dùng `./`:
```bash
./ls
# Output: Hello, This is MY ls program
```

Nhưng nếu bạn chỉ gõ `ls` (không có `./`), Linux vẫn sẽ chạy lệnh `ls` thật của hệ thống (liệt kê file), vì `/tmp` không nằm trong `$PATH`. Bạn có thể kiểm tra bằng lệnh:
```bash
which ls
# Output: /bin/ls (hoặc /usr/bin/ls)
```

**Bước 3: Đưa thư mục `/tmp` vào ĐẦU `$PATH` (Mô phỏng bị hack)**
Bây giờ, thử thêm `/tmp` vào đầu danh sách `$PATH`:
```bash
export PATH=/tmp:$PATH
```

**Bước 4: Hậu quả**
Bây giờ, dù bạn đang đứng ở bất kỳ đâu trên hệ thống, nếu bạn gõ `ls`, Linux sẽ tìm thấy file `ls` giả mạo của bạn ở `/tmp` ĐẦU TIÊN và chạy nó:
```bash
ls
# Output: Hello, This is MY ls program
```
Lệnh `ls` thật của hệ thống đã bị "chiếm quyền" (hijacked).

> 🚨 **BÀI HỌC RÚT RA:** 
> 1. **KHÔNG BAO GIỜ** thêm thư mục hiện tại (`./`) hoặc các thư mục công cộng như `/tmp` vào `$PATH`. Ví dụ: `export PATH=./:$PATH` là một ý tưởng tồi.
> 2. Nếu làm vậy, ai đó có thể để một file mã độc tên là `ls` hoặc `cd` vào thư mục đó. Khi bạn `cd` vào và gõ `ls`, bạn sẽ kích hoạt mã độc của họ thay vì chạy lệnh hệ thống.

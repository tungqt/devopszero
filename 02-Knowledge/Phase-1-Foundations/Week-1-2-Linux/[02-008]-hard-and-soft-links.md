# Hard Links và Soft (Symbolic) Links trong Linux

## Document Information
- **Doc ID:** 02-008
- **Summary:** Phân biệt Hard Link và Soft Link, khái niệm Inode, cách tạo và ứng dụng thực tế trong quản trị hệ thống Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-06
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan về Inode (Khái niệm cốt lõi)

Để hiểu được Link trong Linux, bạn BẮT BUỘC phải hiểu khái niệm **Inode** (Index Node).

Trong Linux, một file thực chất được chia làm 2 phần riêng biệt:
1. **Dữ liệu thật (Data block):** Nội dung của file nằm trên ổ cứng.
2. **Inode:** Một cấu trúc dữ liệu chứa thông tin về file đó (kích thước, quyền sở hữu, quyền truy cập, và **con trỏ trỏ tới vị trí của Data block**). Mỗi Inode có một số ID duy nhất.

**Tên file (Filename)** mà bạn nhìn thấy (ví dụ `file1.txt`) thực chất chỉ là một cái nhãn (label) trỏ vào một Inode.

Bạn có thể xem số Inode của một file bằng lệnh `ls -i` hoặc `ls -li`:
```bash
$ ls -li file1.txt
84 -rw-rw-r-- 1 coop coop 1551 Jun 16 16:28 file1.txt
```
*(Số `84` ở đầu tiên chính là Inode number).*

---

## 2. Hard Link (Liên kết cứng)

### 2.1. Hard Link là gì?
Hard Link là việc tạo ra **nhiều tên file khác nhau cùng trỏ chung vào MỘT Inode duy nhất**.

**Cách tạo Hard Link:**
```bash
ln file1 file2
```

**Kiểm tra kết quả:**
```bash
$ ls -li file1 file2
84 -rw-rw-r-- 2 coop coop 1551 Jun 16 16:28 file1
84 -rw-rw-r-- 2 coop coop 1551 Jun 16 16:28 file2
```

**Phân tích Output:**
- Cả `file1` và `file2` đều có chung Inode number là `84`.
- Số `2` ở cột thứ 3 (trước chữ `coop`) cho biết Inode này đang có 2 Hard Link trỏ tới nó.
- Về bản chất: Không có file nào là "bản gốc" hay "bản sao". Cả hai đều bình đẳng và là 2 cánh cửa khác nhau để đi vào cùng một căn phòng chứa dữ liệu.

### 2.2. Đặc điểm của Hard Link
- **Tiết kiệm dung lượng:** Dù bạn có tạo 100 Hard Link, dung lượng ổ cứng bị chiếm dụng vẫn chỉ bằng 1 file duy nhất.
- **Xóa file:** Nếu bạn xóa `file1` (`rm file1`), dữ liệu thật vẫn không bị mất. Bạn vẫn có thể đọc được dữ liệu qua `file2`. Dữ liệu thật chỉ bị xóa hoàn toàn khỏi ổ cứng khi **TẤT CẢ** các Hard Link trỏ tới Inode đó bị xóa (số link giảm về 0).
- **Hạn chế lớn nhất:** Hard Link **KHÔNG THỂ** trỏ xuyên qua các phân vùng (partitions/filesystems) khác nhau. (Vì mỗi phân vùng có một bảng Inode riêng, Inode 84 ở phân vùng `/` khác hoàn toàn Inode 84 ở phân vùng `/home`).
- **Không thể tạo Hard Link cho thư mục (Directory):** Để tránh tạo ra vòng lặp vô tận (infinite loop) trong cấu trúc cây thư mục.

### 2.3. Ứng dụng thực tế của Hard Link
Một ví dụ kinh điển trong Linux là lệnh nén file `gzip` và giải nén `gunzip`:
```bash
$ ls -li /bin/g*zip
194339 -rwxr-xr-x 3 root root 62872 Jan 14 13:06 /bin/gunzip
194339 -rwxr-xr-x 3 root root 62872 Jan 14 13:06 /bin/gzip
```
Cả hai lệnh này thực chất là **cùng một file thực thi duy nhất** (chung Inode 194339). Khi chương trình chạy, nó sẽ tự kiểm tra xem người dùng đã gọi nó bằng cái tên nào (`gzip` hay `gunzip`) để quyết định sẽ thực hiện chức năng nén hay giải nén. Điều này giúp tiết kiệm không gian đĩa.

---

## 3. Soft Link / Symbolic Link (Liên kết mềm)

### 3.1. Soft Link là gì?
Soft Link (hay Symlink) hoạt động giống hệt như **Shortcut** trong Windows. Nó là một file đặc biệt, nội dung của file này chỉ đơn giản là chứa **đường dẫn (path)** trỏ tới một file hoặc thư mục khác.

**Cách tạo Soft Link (thêm cờ `-s`):**
```bash
ln -s file1 file2
```

**Kiểm tra kết quả:**
```bash
$ ls -li file1 file2
84 -rw-rw-r-- 1 coop coop 1551 Jun 16 16:28 file1
85 lrwxrwxrwx 1 coop coop    5 Jun 16 16:43 file2 -> file1
```

**Phân tích Output:**
- `file2` có Inode number hoàn toàn khác (`85`). Nó là một file độc lập.
- Ký tự đầu tiên ở cột quyền là chữ `l` (chỉ định đây là một Link).
- Cuối dòng hiển thị rõ ràng `file2 -> file1`.

### 3.2. Đặc điểm của Soft Link
- **Xóa file gốc:** Nếu bạn xóa `file1` (bản gốc), `file2` vẫn tồn tại nhưng sẽ trở thành một **"Dangling link" (Link chết/Link mồ côi)**. Nếu bạn cố đọc `file2`, Linux sẽ báo lỗi "No such file or directory".
- **Vượt ranh giới:** Soft Link **CÓ THỂ** trỏ xuyên qua các phân vùng khác nhau, thậm chí trỏ tới các ổ đĩa mạng (NFS) chưa được mount.
- **Hỗ trợ Thư mục:** Soft Link có thể trỏ tới cả File và Thư mục (Directory).
- **Dung lượng:** Soft Link chiếm một dung lượng cực nhỏ (chỉ bằng độ dài của chuỗi ký tự đường dẫn).

### 3.3. Ứng dụng thực tế của Soft Link (Rất phổ biến trong DevOps)
Soft Link được sử dụng cực kỳ rộng rãi trong quản trị hệ thống:

**Ví dụ 1: Quản lý phiên bản phần mềm (Version Control)**
Giả sử bạn cài đặt Node.js phiên bản 18 và 20:
```bash
/opt/node-v18/bin/node
/opt/node-v20/bin/node
```
Thay vì bắt người dùng phải gõ đường dẫn dài dòng, bạn tạo một Soft Link:
```bash
ln -s /opt/node-v20/bin/node /usr/bin/node
```
Khi có bản cập nhật mới (v22), bạn chỉ cần xóa Soft Link cũ và tạo Soft Link mới trỏ tới bản v22. Các ứng dụng khác gọi lệnh `node` sẽ tự động dùng bản mới mà không cần sửa code.

**Ví dụ 2: Quản lý cấu hình Nginx/Apache**
Trong Nginx, người ta thường lưu tất cả các file cấu hình ở thư mục `sites-available`, nhưng Nginx chỉ chạy các cấu hình nằm ở `sites-enabled`.
Khi muốn bật một trang web, SysAdmin sẽ tạo một Soft Link:
```bash
ln -s /etc/nginx/sites-available/mywebsite.conf /etc/nginx/sites-enabled/
```
Khi muốn tắt trang web, chỉ cần xóa Soft Link ở `sites-enabled` (file gốc ở `sites-available` vẫn còn nguyên).

---

## 4. Bảng So sánh Nhanh

| Tiêu chí | Hard Link | Soft Link (Symlink) |
|---|---|---|
| **Lệnh tạo** | `ln <gốc> <link>` | `ln -s <gốc> <link>` |
| **Bản chất** | Cùng trỏ vào 1 Inode | Là file mới, chứa đường dẫn trỏ tới file gốc |
| **Xóa file gốc** | Link vẫn hoạt động bình thường | Link bị chết (Dangling link) |
| **Xuyên phân vùng (Cross-partition)** | ❌ Không thể | ✅ Có thể |
| **Trỏ tới Thư mục (Directory)** | ❌ Không thể | ✅ Có thể |
| **Mức độ sử dụng thực tế** | Ít dùng (chủ yếu do OS tự dùng) | Rất phổ biến (DevOps dùng liên tục) |

---

## 5. Công cụ hỗ trợ: `symlinks`
Để kiểm tra xem trong hệ thống có các Soft Link nào bị "chết" (Dangling) do file gốc đã bị xóa hay không, bạn có thể dùng công cụ `symlinks`:

```bash
symlinks -rv .
```
Output sẽ liệt kê các link hợp lệ (`relative`, `absolute`) và các link đã chết (`dangling`) để bạn dọn dẹp.

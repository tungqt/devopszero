# Không gian Hoán đổi (Swap Space)

## Document Information
- **Doc ID:** 02-013
- **Summary:** Tìm hiểu về cơ chế Swap (bộ nhớ ảo trên ổ cứng) trong Linux, cách hệ thống overcommit RAM, và các công cụ quản lý phân vùng/file swap.
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Swap?**
Mặc dù RAM ngày nay rất rẻ và các server thường có dung lượng RAM lớn, Swap vẫn đóng vai trò quan trọng như một "chiếc phao cứu sinh". Khi máy chủ đột ngột hết RAM vật lý (do lượng truy cập tăng vọt hoặc một ứng dụng bị lỗi memory leak), nếu không có Swap, hệ thống sẽ sập hoặc kích hoạt OOM Killer ngay lập tức. Nếu có Swap, hệ thống sẽ chậm lại đáng kể (do tốc độ ổ cứng rất chậm so với RAM) nhưng vẫn tiếp tục hoạt động, cho bạn thời gian để kịp thời can thiệp xử lý. Hiểu cách cấu hình và giám sát Swap giúp bạn đảm bảo tính sẵn sàng (High Availability) cho hệ thống.

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Bất kỳ hệ thống Linux nào (Local VM hoặc AWS EC2).
- **Yêu cầu:** 
  - Các lệnh xem thông tin (`free`, `cat /proc/swaps`) có thể chạy bằng user thường.
  - Các lệnh thao tác bật/tắt Swap bắt buộc phải có quyền `root` (`sudo`).

## 3. Cơ chế hoạt động của Swap và Virtual Memory

Hệ thống bộ nhớ ảo (Virtual Memory) của Linux cho phép hệ điều hành hoạt động như thể nó có nhiều RAM hơn thực tế. Khả năng "tiêu xài vượt mức" (overcommission) này hoạt động dựa trên hai cơ chế chính:

1. **Không phải lúc nào ứng dụng cũng dùng hết RAM đã xin:** Nhiều chương trình xin cấp phát rất nhiều RAM nhưng thực tế chỉ dùng một phần nhỏ. Hơn nữa, khi một tiến trình sinh ra tiến trình con (child process), Linux dùng kỹ thuật **COW (Copy On Write)**: tiến trình con sẽ dùng chung vùng nhớ của tiến trình cha. Chỉ khi nào tiến trình con thực sự thay đổi dữ liệu trên một block nhớ (page), hệ thống mới cấp phát một bản sao vùng nhớ mới cho nó.
2. **Swapping (Hoán đổi xuống đĩa):** Khi áp lực bộ nhớ (memory pressure) tăng cao, Kernel sẽ tìm các vùng nhớ ít hoạt động nhất trên RAM và "đẩy" (swap out) chúng xuống một khu vực đặc biệt trên ổ cứng gọi là **Swap**. Khi nào ứng dụng cần lại dữ liệu đó, Kernel sẽ đọc từ ổ cứng và "kéo" (swap in) ngược lại lên RAM.

**Lưu ý quan trọng:**
- **Không bao giờ swap cache file:** Phần lớn RAM thường được dùng để cache nội dung file (để tránh phải đọc ổ cứng nhiều lần). Kernel **không bao giờ** đẩy (swap out) các vùng cache này xuống Swap vì điều đó hoàn toàn vô nghĩa (bản chất file đã nằm sẵn trên ổ cứng rồi). Thay vào đó, Kernel chỉ cần đồng bộ (flush) các "dirty pages" (cache đã bị sửa đổi) xuống file gốc và sau đó giải phóng luôn vùng RAM đó.
- **Kernel Space không bao giờ bị swap:** Vùng nhớ RAM do chính nhân hệ điều hành (Kernel) sử dụng sẽ không bao giờ bị đẩy xuống Swap. (Điều này khác biệt với một số hệ điều hành khác).

## 4. Cấu trúc và Độ ưu tiên của Swap

Linux cho phép bạn tạo Swap bằng 2 cách, và bạn có thể cấu hình nhiều vùng Swap hoạt động cùng lúc:
- **Swap Partition:** Dành hẳn một phân vùng ổ đĩa chuyên biệt (như `/dev/sda6`) chỉ để làm swap.
- **Swap File:** Tạo một file lớn trên ổ cứng (như `/swapfile`) và định dạng nó thành Swap. Cách này rất linh hoạt ở các môi trường Cloud (AWS EC2) vì không cần chia lại phân vùng ổ đĩa.

**Độ ưu tiên (Priority):**
Mỗi vùng Swap được gán một mức độ ưu tiên. Kernel sẽ ưu tiên ghi dữ liệu xuống các vùng Swap có Priority cao trước. Chỉ khi vùng ưu tiên cao đã đầy, nó mới bắt đầu dùng đến vùng có Priority thấp hơn. (Khuyến nghị dung lượng Swap thường bằng tổng số RAM, tuy nhiên với các server hiện đại có RAM rất lớn, tỷ lệ này có thể giảm xuống).

## 5. Các Lệnh Quản lý Swap

### 5.1. Xem Swap đang hoạt động
Có 2 cách để xem dung lượng và trạng thái Swap:

```bash
# Cách 1: Xem chi tiết các vùng Swap (đường dẫn, loại, kích thước, mức độ sử dụng, độ ưu tiên)
cat /proc/swaps

# Cách 2: Xem tổng quan (cùng với dung lượng RAM)
free -mt
```

### 5.2. Các lệnh thao tác với Swap (Yêu cầu `sudo`)
- **`mkswap`**: Định dạng một phân vùng (`/dev/sdX`) hoặc một file rỗng để nó trở thành không gian Swap.
- **`swapon`**: Bật (kích hoạt) một phân vùng/file để hệ thống bắt đầu dùng làm Swap (hoặc bật tất cả các swap cấu hình trong `/etc/fstab`).
- **`swapoff`**: Tắt (hủy kích hoạt) một vùng Swap. Kernel sẽ phải kéo toàn bộ dữ liệu từ vùng swap này lên lại RAM (nếu RAM còn đủ chỗ).

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Kiểm tra các vùng Swap hiện có trên máy chủ và tìm hiểu quy trình tạo một Swap File.

**Bước 1:** Kiểm tra tổng dung lượng Swap hiện tại bằng lệnh `free`.
```bash
free -mt
```
*Quan sát hàng `Swap:`. Nếu cột `total` là 0, nghĩa là máy chủ của bạn hiện không cấu hình Swap (rất phổ biến ở cấu hình mặc định của AWS EC2 Ubuntu/Amazon Linux).*

**Bước 2:** Xem chi tiết các vùng Swap đang được Kernel quản lý.
```bash
cat /proc/swaps
```
*Nếu bạn có Swap, kết quả sẽ liệt kê: Filename (tên file hoặc phân vùng), Type (kiểu partition hay file), Size (kích thước tính bằng KB), Used (lượng đang dùng), và Priority.*

**Bước 3:** Tham khảo quy trình tạo Swap File trên Cloud Server (Lab quan sát).
*(Lưu ý: Bạn có thể thực thi các lệnh này nếu máy chủ EC2 của bạn hiện đang không có Swap).*

```bash
# 1. Tạo 1 file rỗng dung lượng 1GB (1024 MB) làm Swap File:
sudo dd if=/dev/zero of=/swapfile bs=1M count=1024

# 2. Cấp quyền bảo mật (chỉ user root mới được đọc/ghi vào file này):
sudo chmod 600 /swapfile

# 3. Format file thành cấu trúc Swap:
sudo mkswap /swapfile

# 4. Kích hoạt Swap file ngay lập tức:
sudo swapon /swapfile

# 5. Kiểm tra lại xem Swap đã lên chưa:
cat /proc/swaps

# 6. Để Swap tự động bật mỗi khi khởi động lại server, bạn cần thêm dòng sau vào file /etc/fstab:
# /swapfile none swap sw 0 0
```

---

## 7. Lab Nâng cao: Mô phỏng OOM Killer với C Program

**Mục tiêu:** Tự tay biên dịch một chương trình C để ép hệ thống rơi vào trạng thái thiếu hụt bộ nhớ (memory pressure) và tận mắt chứng kiến **OOM Killer** ra tay "trảm" tiến trình.

> 📌 **Cảnh báo (Warning):** Bài Lab này có thể làm treo hệ thống tạm thời. **KHÔNG** thực hiện trên Production server. Bạn có thể làm trên máy ảo cá nhân hoặc một server AWS EC2 riêng biệt dành cho việc test.

### Bước 1: Mở 2 cửa sổ Terminal
Bạn cần mở 2 cửa sổ terminal (SSH session) song song để vừa chạy lệnh vừa theo dõi log hệ thống.
- **Terminal 1:** Dùng để theo dõi Kernel log theo thời gian thực (real-time).
  Lệnh tốt nhất để xem là:
  ```bash
  dmesg -w
  ```
  *(Cờ `-w` giúp theo dõi liên tục giống `tail -f`, và `dmesg` chỉ in ra log của Kernel, tránh bị nhiễu bởi các log ứng dụng khác).*

- **Terminal 2:** Dùng để thực thi các lệnh gây tốn RAM ở các bước tiếp theo.

### Bước 2: Tắt hệ thống Swap
Để bài tập này dễ quan sát và diễn ra nhanh hơn, chúng ta sẽ tắt toàn bộ Swap. Nếu không tắt Swap, máy chủ sẽ cố gắng đẩy dữ liệu xuống ổ cứng khiến quá trình rất lâu và giật lag.
Tại Terminal 2, gõ:
```bash
sudo /sbin/swapoff -a

# Kiểm tra lại xem Swap đã về 0 chưa
free -m
```

### Bước 3: Viết và biên dịch chương trình ngốn RAM
Trên Terminal 2, tạo một file tên là `lab_wastemem.c`:
```bash
nano lab_wastemem.c
```
Dán đoạn mã C sau vào file và lưu lại. *(Đoạn mã này nhận vào một con số và liên tục cấp phát từng Megabyte bộ nhớ cho đến giới hạn đó)*:
```c
/* simple program to consume memory, J. Cooperstein 2/04 */
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

#define MB (1024*1024)

int main(int argc, char **argv) {
    int mb, i;
    char *p;
    
    if (argc < 2) {
        printf("Usage: %s <MB to consume>\n", argv[0]);
        exit(1);
    }
    
    mb = atoi(argv[1]);
    printf("Bắt đầu cấp phát %d MB...\n", mb);
    
    for (i = 0; i < mb; i++) {
        p = malloc(MB);
        if (p == NULL) {
            printf("Lỗi cấp phát tại %d MB\n", i);
            exit(1);
        }
        // Ghi dữ liệu thực sự vào vùng nhớ để ép OS cấp phát RAM vật lý
        memset(p, 0, MB); 
        printf("Đã cấp phát %d MB\n", i + 1);
        usleep(50000); // Tạm dừng 0.05s mỗi lần để dễ quan sát
    }
    
    printf("Hoàn thành! Đang giữ RAM trong 60 giây...\n");
    sleep(60);
    return 0;
}
```

Biên dịch chương trình thành file thực thi `lab_wastemem` *(Nếu báo lỗi thiếu `gcc`, hãy chạy lệnh `sudo apt install gcc -y` trước)*:
```bash
gcc -o lab_wastemem lab_wastemem.c
```

### Bước 4: Thực thi và quan sát OOM Killer hành động
Hãy kiểm tra `free -m` xem bạn còn bao nhiêu RAM, sau đó chạy chương trình và yêu cầu số RAM lớn hơn số bạn đang có.
```bash
# Ví dụ: Nếu còn trống 800MB, hãy thử xin 2000MB
./lab_wastemem 2000
```
- Ngay lúc này, hãy nhìn sang **Terminal 1** đang chạy `dmesg -w`.
- Khi chương trình cố gắng cấp phát đến giới hạn cuối cùng của RAM vật lý, bạn sẽ thấy hệ thống khựng lại một nhịp.
- Sau đó, Kernel sẽ bắt đầu tính điểm **OOM score** cho từng tiến trình. 
- Ngay lập tức, dòng chữ đỏ chót sẽ xuất hiện: **`Out of memory: Killed process <PID> (lab_wastemem)`**.
- Tại Terminal 2, chương trình của bạn sẽ bị tắt ngay lập tức kèm thông báo `Killed`.
- **Kết luận:** Kernel đã cứu hệ thống bằng cách trảm "thủ phạm" ngốn nhiều RAM nhất. Trong một số trường hợp hiếm, OOM Killer có thể giết nhầm cả Shell hoặc ứng dụng vô tội khác nếu điểm OOM score của chúng bị tính toán sai lệch do tải hệ thống lúc đó.

### Bước 5: Dọn dẹp (Cleanup)
Sau khi kết thúc thử nghiệm, hãy đảm bảo bạn **bật lại Swap** để hệ thống được an toàn cho các tác vụ sau này:
```bash
sudo /sbin/swapon -a
free -m
```
# Quản lý Bộ nhớ (Memory Management)

## Document Information
- **Doc ID:** 02-012
- **Summary:** Tìm hiểu cơ chế quản lý bộ nhớ ảo (Virtual Memory), cách hệ thống sử dụng Cache/Buffer và các công cụ giám sát, giải phóng bộ nhớ trên Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-08
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về quản lý bộ nhớ?**
Bộ nhớ (RAM) là tài nguyên đắt đỏ và quan trọng bậc nhất của máy chủ. Khi giám sát một hệ thống Linux, bạn sẽ thường xuyên thấy tình trạng "RAM lúc nào cũng báo đầy", nhưng thực chất hệ thống vẫn đang hoạt động rất mượt mà. Hiểu đúng về cơ chế Virtual Memory, cách hệ thống dùng RAM làm bộ nhớ đệm (Cache/Buffer), và biết cách đọc các thông số cấp phát bộ nhớ sẽ giúp bạn không bị hoảng loạn trước các cảnh báo giả, đồng thời chẩn đoán chính xác khi máy chủ thực sự cạn kiệt tài nguyên (OOM - Out of Memory).

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Bất kỳ hệ thống Linux nào (Local VM hoặc AWS EC2).
- **Yêu cầu:** 
  - Các lệnh xem thông tin (`free`, `cat`) có thể thực thi bằng user thường.
  - Các lệnh can thiệp sâu (như giải phóng cache) bắt buộc phải có quyền `root` (thông qua `sudo`).

## 3. Cơ chế Bộ nhớ ảo (Virtual Memory System)

Giống như mọi hệ điều hành hiện đại, Linux sử dụng hệ thống **Bộ nhớ ảo (Virtual Memory - VM)**.
- **Không gian địa chỉ độc lập:** Mỗi tiến trình (process) khi chạy sẽ được cấp một không gian địa chỉ bộ nhớ ảo riêng biệt và được bảo vệ. 
- **Vai trò của Kernel:** Các địa chỉ ảo này sẽ được Kernel (nhân hệ điều hành) biên dịch (translate) thành các địa chỉ bộ nhớ vật lý (Physical RAM) mỗi khi tiến trình cần truy xuất dữ liệu.
- **Lợi ích:**
  - Kích thước bộ nhớ ảo có thể lớn hơn dung lượng RAM vật lý thực tế.
  - Kernel đảm bảo việc phân bổ RAM công bằng giữa các tiến trình.
  - Cho phép ánh xạ (mapping) trực tiếp một tệp (file) vào không gian ảo của tiến trình.
  - Bảo vệ các vùng nhớ quan trọng (ngăn chặn quyền ghi hoặc thực thi mã độc).

*(Lưu ý: Trên các hệ thống 32-bit cũ, không gian địa chỉ chỉ giới hạn ở 4GB, trong đó 3GB cho User Space và 1GB cho Kernel Space. Hiện nay, hầu hết các máy chủ đều sử dụng kiến trúc 64-bit, loại bỏ hoàn toàn các giới hạn chật hẹp này).*

## 4. Cache và Buffer (Bộ nhớ đệm)

Một trong những khái niệm dễ gây hiểu lầm nhất trên Linux là lượng RAM bị hệ thống "chiếm dụng".
- Các ứng dụng hiếm khi ghi dữ liệu trực tiếp xuống ổ cứng (disk). Thay vào đó, chúng tương tác với hệ thống Virtual Memory.
- Khi ứng dụng đọc/ghi file, Linux sẽ lưu tạm các khối dữ liệu đó vào RAM dưới dạng **Page Cache** hoặc **Buffer**. Dữ liệu sau đó mới được đồng bộ (flush) xuống ổ cứng vào thời điểm thích hợp.
- **Hệ quả:** Trên hầu hết các hệ thống Linux, phần lớn RAM sẽ được dùng cho tầng buffering/caching này thay vì cho bản thân các ứng dụng. Đây là tính năng (feature), không phải lỗi (bug) – nó giúp tăng tốc độ truy xuất file lên hàng vạn lần.

## 5. Các Công cụ Giám sát Bộ nhớ

### 5.1. Lệnh `free`
Lệnh `free` cung cấp báo cáo ngắn gọn về bộ nhớ trống và đã sử dụng.
Thường dùng với cờ `-m` (hiển thị theo Megabyte) hoặc `-g` (hiển thị theo Gigabyte) và `-t` (hiển thị tổng cộng).

```bash
free -mt
```
**Ý nghĩa các cột chính:**
- **total:** Tổng dung lượng RAM vật lý.
- **used:** Lượng RAM đang bị các tiến trình thực sự sử dụng.
- **free:** Lượng RAM hoàn toàn trống (chưa dùng vào việc gì, kể cả cache).
- **buff/cache:** Lượng RAM đang được Linux dùng làm bộ nhớ đệm. Lượng RAM này có thể được giải phóng ngay lập tức nếu ứng dụng cần.
- **available:** (Rất quan trọng) Lượng RAM ước tính còn có thể cấp phát cho các ứng dụng mới khởi chạy mà không cần phải dùng đến Swap. **Đây là con số DevOps cần quan tâm nhất để biết máy chủ có thiếu RAM hay không.**

### 5.2. File `/proc/meminfo`
Nếu `free` quá ngắn gọn, bạn có thể xem thông tin cực kỳ chi tiết do trực tiếp Kernel báo cáo thông qua file hệ thống ảo `/proc/meminfo`.

```bash
cat /proc/meminfo
```
Ở đây bạn sẽ thấy chi tiết các loại bộ nhớ như `MemTotal`, `MemFree`, `MemAvailable`, `Buffers`, `Cached`, `SwapCached`, `Active`, `Inactive`...

**Làm sao để biết RAM đã thực sự đầy hay chỉ là đang chứa Cache?**
Khi đọc `/proc/meminfo`, bạn KHÔNG nên chỉ nhìn vào `MemFree`. Giống như lệnh `free`, `MemFree` có thể rất thấp (chỉ vài MB) nhưng máy chủ vẫn hoàn toàn bình thường vì RAM đang chứa Cache. Để biết chính xác, hãy nhìn vào **`MemAvailable`**:
- **`MemAvailable` = Còn nhiều:** Máy chủ đang rất ổn. Lượng RAM bị chiếm dụng thực chất là `Buffers` và `Cached`, Kernel sẵn sàng vứt bỏ chúng để nhường RAM cho ứng dụng của bạn bất cứ lúc nào.
- **`MemAvailable` = Cạn kiệt (gần bằng 0):** Đây là lúc máy chủ THỰC SỰ ĐẦY RAM. Hệ thống không còn khả năng giải phóng thêm Cache nào nữa. Lúc này hệ thống sẽ bắt đầu sử dụng Swap (làm máy chủ cực kỳ chậm) hoặc kích hoạt **OOM Killer** để "giết" bớt ứng dụng.

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Kiểm tra tình trạng RAM và thử nghiệm cơ chế tự động giải phóng Page Cache (bộ nhớ đệm) của Linux.

**Bước 1:** Kiểm tra dung lượng RAM hiện tại.
```bash
free -mt
```
*Ghi nhận lại con số ở cột `buff/cache` và `available`. Nếu máy chủ đã chạy lâu, cột `buff/cache` thường rất lớn, trong khi cột `free` rất nhỏ.*

**Bước 2:** Chuyển sang quyền Root.
Để can thiệp vào các tham số hệ thống ảo của Kernel, lệnh `sudo` thông thường với `echo` sẽ không hoạt động (do giới hạn redirect `>`). Bạn cần mở một shell của root.
```bash
sudo su
```

**Bước 3:** Giải phóng bộ nhớ đệm (Drop Caches).
Linux cho phép bạn ép hệ thống vứt bỏ các dữ liệu đang cache trên RAM (Page cache, dentry, inode).
```bash
echo 3 > /proc/sys/vm/drop_caches
```
*(Số 3 có nghĩa là giải phóng TẤT CẢ các loại cache. Nếu chỉ muốn giải phóng Page cache, bạn dùng số 1).*

**Bước 4:** Thoát khỏi quyền root.
```bash
exit
```

**Bước 5:** Kiểm tra lại sự thay đổi của RAM.
```bash
free -mt
```
*So sánh với kết quả ở Bước 1, bạn sẽ thấy cột `buff/cache` giảm đi đáng kể, và lượng RAM đó được trả về cho cột `free`. Tuy nhiên, thông số `available` sẽ không thay đổi quá nhiều, minh minh chứng cho việc Linux luôn biết cách dùng RAM rảnh rỗi để làm cache một cách thông minh.*
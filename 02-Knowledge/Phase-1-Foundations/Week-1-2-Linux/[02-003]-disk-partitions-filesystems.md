# Quản lý Ổ đĩa, Phân vùng và Filesystem trong Linux

## Document Information
- **Doc ID:** 02-003
- **Summary:** Phân tích sâu về Disk Partitioning (MBR vs GPT), cách hệ thống Linux nhận diện ổ đĩa, công cụ `fdisk`, định dạng filesystem (`mkfs`) và resize phân vùng.
- **Version:** 0.0.1
- **Date:** 2026-05-02
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)

### 1.1. Tại sao DevOps phải hiểu về Partition?
Một ổ cứng vật lý (Physical Disk) trống không thể dùng được ngay. Trước khi hệ điều hành ghi dữ liệu lên, ổ đĩa phải đi qua **3 bước trừu tượng hóa (abstraction layers)**:

1. **Partitioning (Phân vùng):** Chia ổ đĩa vật lý thành các "lát cắt" logic.
2. **Formatting (Định dạng):** Tạo một Filesystem (ext4, xfs...) trên từng phân vùng.
3. **Mounting (Gắn kết):** Gắn phân vùng đó vào một thư mục (mount point) để OS sử dụng.

Đối với DevOps Engineer, việc thành thạo phân vùng là **bắt buộc** vì các kịch bản thực tế sau:
- **Cloud Storage:** Khi gắn thêm một **EBS Volume** vào EC2 (AWS), một **Persistent Disk** vào GCP, hoặc một **Managed Disk** vào Azure — bạn phải tự tay phân vùng và format trước khi container/app có thể dùng được.
- **Mở rộng dung lượng (Disk Resize):** Khi server báo "Disk Full" (`No space left on device`), bạn phải biết cách mở rộng phân vùng và resize filesystem mà không làm mất dữ liệu.
- **Tách biệt rủi ro:** Trên server production, người ta thường tách `/var/log` (chứa log) và `/home` (chứa dữ liệu user) sang phân vùng riêng — để khi log "tràn" thì hệ thống `/` (root) không bị treo theo.
- **Khắc phục sự cố (Recovery):** Khi server không boot được, kỹ năng đọc bảng phân vùng bằng `fdisk -l` từ chế độ Rescue Mode là kỹ năng sống còn.

### 1.2. Môi trường thực hành (Environment Setup)
Đây là bài học **đặc biệt nguy hiểm** nếu thực hành sai môi trường. Các lệnh phân vùng (`fdisk`, `mkfs`) có thể **xóa sạch toàn bộ dữ liệu** trên ổ đĩa nếu gõ nhầm tên thiết bị (ví dụ: gõ `/dev/sda` thay vì `/dev/sdb`).

**Khuyến nghị môi trường thực hành:**

| Môi trường | Mức độ phù hợp | Lý do |
|---|---|---|
| **MacOS Terminal trực tiếp** | ❌ Không dùng | MacOS không có `fdisk`, `mkfs`, `lsblk`. Lệnh phân vùng của Mac (`diskutil`) khác hoàn toàn Linux. |
| **Docker container Linux** | ⚠️ Hạn chế | Container chia sẻ kernel với host, không có ổ đĩa thật để thực hành. |
| **AWS EC2 + EBS Volume phụ** | ✅ Khuyến nghị | An toàn, có ổ đĩa thật `/dev/xvdb` để thử mà không sợ hỏng OS. (Có thể tận dụng AWS Free Tier). |
| **Máy ảo VirtualBox/UTM** | ✅ Khuyến nghị | Tạo thêm 1 virtual disk trống, thực hành thoải mái. |
| **Linux loop device (file giả lập)** | ✅ Khuyến nghị cho beginner | Tạo 1 file 1GB trên server, "giả lập" thành ổ đĩa để thực hành an toàn 100%. (Xem Lab 3 ở cuối). |

> 💡 **Quy tắc vàng (Golden Rule):** Trước khi gõ bất kỳ lệnh `fdisk`, `mkfs`, `dd` nào — **luôn luôn** chạy `lsblk` hoặc `fdisk -l` để xác nhận chính xác tên thiết bị (device name) bạn đang thao tác. Một ký tự sai (`sda` vs `sdb`) có thể phá hủy toàn bộ server.

---

## 2. Cách Linux nhận diện và đặt tên Ổ đĩa

### 2.1. Cơ chế nhận diện
Khi server khởi động, **Linux Kernel** tự động phát hiện tất cả các ổ đĩa được kết nối sẵn. Bạn **không cần** phải khai báo trong file config nào cả. Trong trường hợp cắm nóng (hotplug — ví dụ cắm USB), hệ thống **udev** sẽ phát hiện thiết bị mới và đọc bảng phân vùng (partition table) ngay lập tức.

### 2.2. Quy ước đặt tên thiết bị (Device Naming Convention)
Tất cả các thiết bị phần cứng trong Linux đều được biểu diễn dưới dạng file trong thư mục `/dev/` (theo triết lý "Everything is a file" của Unix).

| Tên thiết bị | Loại ổ đĩa | Khi nào gặp |
|---|---|---|
| `/dev/sda`, `/dev/sdb`, `/dev/sdc`... | SCSI/SATA/USB Disk | Phổ biến nhất, server vật lý hoặc VM thông thường |
| `/dev/nvme0n1`, `/dev/nvme1n1`... | NVMe SSD | Server hiện đại, EC2 instance đời mới |
| `/dev/xvda`, `/dev/xvdb`... | Xen Virtual Disk | EC2 instance đời cũ (Xen-based) |
| `/dev/vda`, `/dev/vdb`... | VirtIO Disk | KVM/QEMU virtualization (Google Cloud, OpenStack) |
| `/dev/loop0`, `/dev/loop1`... | Loop device | File giả lập thành block device (Lab thực hành) |

**Quy tắc đặt tên Phân vùng:**
- Phân vùng được đánh số nối tiếp sau tên ổ đĩa.
- Ví dụ: `/dev/sda` (ổ đĩa) → `/dev/sda1`, `/dev/sda2`, `/dev/sda3` (các phân vùng).
- Với NVMe: `/dev/nvme0n1` → `/dev/nvme0n1p1`, `/dev/nvme0n1p2` (có thêm chữ `p`).

---

## 3. Hai chuẩn Bảng Phân vùng: MBR vs GPT

Đây là **kiến thức nền tảng** quyết định cách bạn chia phân vùng. Hệ thống lưu thông tin về các phân vùng ở đâu? Câu trả lời nằm ở **Partition Table**.

### 3.1. MBR (Master Boot Record) - Chuẩn cũ
- **Lịch sử:** Ra đời từ thời MS-DOS (đầu thập niên 1980), gắn liền với BIOS.
- **Vị trí lưu trữ:** Lưu ở 512 byte đầu tiên của ổ đĩa.
- **Giới hạn nghiêm trọng:**
  - Tối đa **4 Primary Partitions** (phân vùng chính).
  - Hỗ trợ ổ đĩa **tối đa 2TB**.
  - Không có cơ chế kiểm tra lỗi (checksum) cho bảng phân vùng → dễ hỏng.

**Cách "lách" giới hạn 4 phân vùng:** Người ta sử dụng cấu trúc:
- 3 Primary Partitions (chính) + 1 **Extended Partition** (phân vùng mở rộng).
- Bên trong Extended Partition có thể chứa nhiều **Logical Partitions** (phân vùng logic) — số lượng phụ thuộc loại đĩa (ví dụ: SCSI cho phép tối đa 16 phân vùng).

### 3.2. GPT (GUID Partition Table) - Chuẩn hiện đại
- **Nền tảng:** Dựa trên chuẩn **UEFI** (Unified Extensible Firmware Interface) — thay thế cho BIOS truyền thống.
- **Ưu điểm vượt trội:**
  - Hỗ trợ tối đa **128 phân vùng** mặc định (không cần dùng "trick" Extended/Logical).
  - Hỗ trợ ổ đĩa **lớn hơn 2TB** (lý thuyết tới 9.4 ZB).
  - Có cơ chế **CRC32 checksum** để phát hiện hỏng bảng phân vùng.
  - Lưu **bản sao dự phòng** của partition table ở cuối ổ đĩa → có thể khôi phục khi đầu ổ bị hỏng.
- **Lưu ý quan trọng:** Hầu hết (nhưng không phải tất cả) hệ thống UEFI đều có thể được cấu hình ngược lại để dùng MBR. Vì vậy, ngay cả trên server đời mới, bạn vẫn có thể gặp ổ đĩa định dạng MBR.

### 3.3. Bảng so sánh nhanh

| Tiêu chí | MBR | GPT |
|---|---|---|
| **Tuổi đời** | ~1983 (MS-DOS) | ~2005 (UEFI) |
| **Số phân vùng tối đa** | 4 (Primary) hoặc 3+1 Extended (∞ Logical) | 128 |
| **Dung lượng đĩa tối đa** | 2 TB | ~9.4 ZB |
| **Cơ chế phát hiện lỗi** | Không | CRC32 |
| **Backup partition table** | Không | Có (cuối đĩa) |
| **Firmware tương thích** | BIOS (Legacy) | UEFI |

> 📌 **Best Practice cho DevOps:** Với mọi server mới triển khai từ 2020 trở đi, **luôn ưu tiên GPT**. Chỉ dùng MBR khi buộc phải tương thích với hệ thống legacy hoặc dual-boot Windows cũ.

---

## 4. Công cụ `fdisk` - Quản lý Phân vùng từ Command Line

`fdisk` là tiện ích command-line **kinh điển** để xem, tạo, xóa và thay đổi loại phân vùng.

### 4.1. Xem toàn bộ ổ đĩa và phân vùng
```bash
sudo /sbin/fdisk -l
```

**Phân tích output mẫu:**
```text
Disk /dev/sda: 2000.4 GB, 2000398934016 bytes, 3907029168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk label type: dos          ← "dos" nghĩa là MBR; "gpt" nghĩa là GPT
Disk identifier: 0x000852df

  Device Boot       Start         End      Blocks    Id    System
/dev/sda1            2048  1048578047   524288000    8e    Linux LVM
/dev/sda2      1048578048  2097154047   524288000    8e    Linux LVM
/dev/sda3      2097154048  3907028991   904937472     5    Extended
/dev/sda5      2097156096  3145732095   524288000    8e    Linux LVM
/dev/sda6      3890448384  3907028991     8290304    82    Linux swap / Solaris
```

**Đọc hiểu các trường — Phần A: Metadata của ổ đĩa (Header)**

#### (1) Dòng `Disk /dev/sda: 2000.4 GB, 2000398934016 bytes, 3907029168 sectors`
Dòng này cung cấp **3 cách đo dung lượng** cùng một ổ đĩa:
- `2000.4 GB`: Quy đổi dễ đọc cho người dùng (theo chuẩn thập phân SI: 1 GB = 10⁹ bytes — đây là cách nhà sản xuất ổ cứng dùng để quảng cáo "ổ 2TB").
- `2000398934016 bytes`: Dung lượng chính xác đến từng byte.
- `3907029168 sectors`: Tổng số **sector** trên ổ đĩa (đơn vị nhỏ nhất mà phần cứng có thể đọc/ghi).
- **Mối quan hệ:** `3907029168 sectors × 512 bytes/sector = 2000398934016 bytes` ≈ 2000.4 GB.

> 📌 **Lưu ý "trick" của nhà sản xuất:** Vì dùng SI (10⁹) thay vì binary (2³⁰), ổ "2TB" thực ra chỉ ≈ 1.82 TiB khi OS tính theo binary. Đây là lý do bạn thấy ổ cứng mới mua "thiếu dung lượng" so với quảng cáo.

#### (2) Dòng `Units = sectors of 1 * 512 = 512 bytes`
- **Ý nghĩa:** Tất cả các giá trị `Start`, `End` trong bảng phân vùng phía dưới được **đo bằng sector**, mỗi sector = 512 bytes.
- **Tại sao quan trọng?** Để bạn tự tính được dung lượng phân vùng. Ví dụ với `/dev/sda1`:
  - `End - Start + 1 = 1048578047 - 2048 + 1 = 1048576000 sectors`
  - `1048576000 × 512 bytes ≈ 536.87 GB ≈ 500 GiB`
- **Lịch sử:** 512 bytes là kích thước sector chuẩn từ thập niên 1950 (theo IBM), được duy trì để tương thích ngược suốt 70 năm.

#### (3) Dòng `Sector size (logical/physical): 512 bytes / 4096 bytes` — **CỰC KỲ QUAN TRỌNG**
Đây là điểm **dễ gây hiểu lầm nhất** với DevOps mới. Một ổ đĩa hiện đại có **2 loại sector size** khác nhau:

| Loại | Giá trị | Ý nghĩa |
|---|---|---|
| **Logical sector** | 512 bytes | Đơn vị mà OS "nhìn thấy" và giao tiếp với ổ đĩa. |
| **Physical sector** | 4096 bytes (4K) | Đơn vị mà phần cứng **thật sự** đọc/ghi xuống đĩa. |

- **Đây là kiến trúc "Advanced Format" (AF) hay "512e"** (512-byte emulation): ổ đĩa giả vờ với OS là sector 512B để tương thích với phần mềm cũ, nhưng bên trong gom 8 sector logical thành 1 sector physical 4K để tăng hiệu năng.
- **Hệ quả nghiêm trọng — Partition Misalignment:** Nếu phân vùng bắt đầu ở sector lẻ (ví dụ sector 63 — chuẩn cũ thời Windows XP), mỗi lần OS muốn ghi 1 block 4K sẽ rơi vào **2 physical sector** → ổ đĩa phải **đọc-sửa-ghi (Read-Modify-Write)** cả 2 sector → **giảm hiệu năng tới 30-50%** và tăng wear cho SSD.
- **Cách phòng tránh:** `fdisk` hiện đại **luôn tự động** căn phân vùng đầu tiên ở sector `2048` (= 1 MiB) — chia hết cho mọi cỡ physical sector phổ biến (4K, 8K, 16K). Đây chính là lý do `/dev/sda1` trong output bắt đầu ở `2048` chứ không phải `1` hay `63`.

#### (4) Dòng `I/O size (minimum/optimal): 4096 bytes / 4096 bytes`
- **Minimum I/O size:** Lượng dữ liệu nhỏ nhất ổ đĩa có thể xử lý mà **không bị penalty**. Nếu OS gửi yêu cầu nhỏ hơn → ổ đĩa phải đệm thêm.
- **Optimal I/O size:** Kích thước I/O **lý tưởng** để đạt throughput cao nhất. Đặc biệt quan trọng với:
  - **RAID arrays:** Optimal thường = stripe size × số disk.
  - **SSD/NVMe:** Optimal thường = erase block size (512KB-4MB).
  - **Cloud storage (EBS):** Có optimal I/O riêng tùy loại (gp3, io2...).
- **Ứng dụng thực tế:** Khi tạo filesystem (`mkfs`), nên chọn block size khớp với I/O optimal để tối đa hiệu năng. `mkfs.ext4` hiện đại tự đọc giá trị này và tự cấu hình.

#### (5) Dòng `Disk label type: dos`
- `dos` → Đĩa đang dùng **MBR** (do MBR ra đời từ thời MS-DOS).
- `gpt` → Đĩa đang dùng **GPT**.
- **Đây là dòng quan trọng nhất** để xác định nhanh chuẩn phân vùng.

#### (6) Dòng `Disk identifier: 0x000852df`
- Là chuỗi **UUID 32-bit** (với MBR) hoặc 128-bit (với GPT), gán định danh duy nhất cho ổ đĩa.
- **Ứng dụng:**
  - Dùng trong file `/etc/fstab` để mount ổ đĩa theo UUID thay vì tên `/dev/sdX` (vì tên `/dev/sdX` có thể đổi khi cắm thêm/rút bớt ổ đĩa, gây lỗi boot).
  - Dùng để kiểm tra xem 2 ổ đĩa có phải là bản clone của nhau không (cùng UUID = clone).

---

**Đọc hiểu các trường — Phần B: Bảng phân vùng (Partition Table)**

| Cột | Ý nghĩa |
|---|---|
| `Device` | Tên thiết bị của phân vùng (`/dev/sda1`, `/dev/sda2`...). Số đếm liên tiếp với MBR Primary, **nhảy số khi có Extended** (xem ghi chú bên dưới). |
| `Boot` | Đánh dấu `*` nếu phân vùng là **bootable** (chứa bootloader). Trên MBR, BIOS sẽ tìm phân vùng có cờ này để khởi động. Trong output mẫu **không có** phân vùng nào được đánh dấu (vì có thể server boot từ ổ khác hoặc dùng UEFI). |
| `Start` | Sector **đầu tiên** của phân vùng. Phân vùng đầu luôn bắt đầu ở `2048` (giải thích ở Mục 3 phía trên). |
| `End` | Sector **cuối cùng** của phân vùng (inclusive). |
| `Blocks` | Dung lượng phân vùng tính bằng **block 1KB** (= 2 sectors). Công thức: `Blocks = (End - Start + 1) / 2`. Ví dụ `/dev/sda1`: `(1048578047 - 2048 + 1) / 2 = 524288000 blocks = 500 GiB`. |
| `Id` | **Partition Type ID** (mã hex 1-2 ký tự) — báo cho OS biết phân vùng này dùng cho mục đích gì. |
| `System` | Tên đầy đủ tương ứng với `Id` (do `fdisk` dịch sẵn). |

#### Bảng `Partition Type ID` thường gặp (hệ cơ số 16):
| ID | System | Mô tả |
|---|---|---|
| `83` | Linux | Filesystem Linux native (ext2/3/4, xfs...) — **phổ biến nhất** |
| `82` | Linux swap / Solaris | Phân vùng swap (bộ nhớ ảo) |
| `8e` | Linux LVM | Dành cho **LVM** (Logical Volume Manager) — sẽ học sau |
| `5`  | Extended | Phân vùng mở rộng (chỉ MBR, để chứa Logical Partition) |
| `7`  | HPFS/NTFS/exFAT | Filesystem của Windows |
| `ef` | EFI System | Phân vùng EFI dùng cho UEFI boot |
| `fd` | Linux RAID auto | Dành cho phần mềm RAID (mdadm) |
| `c`  | W95 FAT32 (LBA) | FAT32 — dùng cho USB, thẻ nhớ |

> ⚠️ **Lưu ý:** `Id` chỉ là **gợi ý** (hint) cho OS, không quyết định nội dung thật bên trong. Bạn vẫn có thể format 1 phân vùng `Id=83` bằng `mkfs.xfs` mà không lỗi — chỉ là không đúng convention. Tốt nhất luôn đặt `Id` khớp với filesystem thật để tránh nhầm lẫn về sau.

#### Tại sao có `/dev/sda5` mà không có `/dev/sda4`?
Quan sát output mẫu, bạn sẽ thấy thứ tự là: `sda1`, `sda2`, `sda3`, **sda5**, `sda6` — **bỏ qua `sda4`**. Đây không phải lỗi, mà là **quy ước MBR**:
- **Số 1-4 luôn dành riêng** cho 4 Primary/Extended Partition (kể cả khi không dùng hết).
- **Logical Partition** (nằm bên trong Extended) được đánh số từ **5 trở lên**.
- Trong ví dụ trên: `/dev/sda3` là Extended (Id=5), bên trong chứa `/dev/sda5` (Logical) → sda4 bị "skip".

> 💡 **Mẹo nhận biết nhanh:** Nếu thấy phân vùng đánh số ≥ 5 trên ổ MBR → chắc chắn ổ đó có dùng Extended Partition. GPT không có giới hạn này nên đánh số tuần tự 1, 2, 3, 4, 5...

### 4.2. Các lệnh tương tác trong `fdisk`
Khi gõ `sudo fdisk /dev/sdb` (chỉ định ổ đĩa), bạn vào **chế độ tương tác**. Các phím tắt cốt lõi:

| Phím | Chức năng |
|---|---|
| `m` | Hiển thị menu trợ giúp |
| `p` | **P**rint - In bảng phân vùng hiện tại |
| `n` | **N**ew - Tạo phân vùng mới |
| `d` | **D**elete - Xóa phân vùng |
| `t` | **T**ype - Đổi loại phân vùng (ví dụ đổi sang `82` cho swap) |
| `l` | **L**ist - Liệt kê tất cả ID loại phân vùng |
| `w` | **W**rite - **GHI thay đổi xuống đĩa** (chỉ tới bước này dữ liệu mới thật sự thay đổi) |
| `q` | **Q**uit - Thoát mà KHÔNG lưu (an toàn nếu lỡ tay) |

> ⚠️ **CẢNH BÁO QUAN TRỌNG:** Mọi thao tác trong `fdisk` chỉ nằm trong bộ nhớ. Chỉ khi bạn nhấn `w`, thay đổi mới được ghi xuống đĩa. Nếu lỡ tay, **luôn dùng `q` để thoát** — sẽ không có gì bị mất.

### 4.3. Hạn chế của `fdisk` và giải pháp thay thế
`fdisk` **không** hỗ trợ:
- **Di chuyển (move)** phân vùng.
- **Resize (thay đổi kích thước)** phân vùng trực tiếp.

**Khi cần resize**, bạn phải làm 2 bước theo thứ tự đúng:

**Tăng kích thước (Grow):**
1. Tăng kích thước **phân vùng** trước (ví dụ dùng `growpart` hoặc xóa rồi tạo lại với end sector lớn hơn).
2. Sau đó mới tăng kích thước **filesystem** (`resize2fs` cho ext4, `xfs_growfs` cho XFS).

**Giảm kích thước (Shrink):**
1. Giảm kích thước **filesystem** trước (an toàn cho dữ liệu).
2. Sau đó mới giảm kích thước **phân vùng**.

> 📌 **Lý do:** Nếu giảm partition trước khi giảm filesystem, filesystem sẽ "tràn" ra ngoài vùng đã được cấp → mất dữ liệu vĩnh viễn.

**Các công cụ thay thế mạnh hơn:**
- `parted` (CLI): Hỗ trợ cả MBR và GPT, có thể resize trực tiếp.
- `gdisk`: Chuyên dụng cho GPT.
- `gparted` (GUI): Phiên bản giao diện đồ họa của `parted`, trực quan, an toàn cho người mới — chạy với quyền `root`.

---

## 5. Tạo Filesystem với `mkfs`

Sau khi đã có phân vùng (ví dụ `/dev/sdb1`), nó **vẫn chưa thể dùng được**. Bạn cần "format" — tức là tạo một **Filesystem** (cấu trúc tổ chức file) lên trên đó.

### 5.1. Cú pháp `mkfs`
Có **2 cách viết tương đương** để format một phân vùng thành ext4:

```bash
# Cách 1: Dùng tham số -t (type)
sudo mkfs -t ext4 /dev/sda10

# Cách 2: Dùng lệnh chuyên biệt cho từng filesystem
sudo mkfs.ext4 /dev/sda10
```

> 💡 Cả 2 cách đều cho kết quả giống nhau. Cách 2 phổ biến hơn vì gõ nhanh hơn và dễ tab-completion.

### 5.2. Các loại Filesystem phổ biến

| Filesystem | Lệnh tạo | Đặc điểm | Khi nào dùng |
|---|---|---|---|
| **ext4** | `mkfs.ext4` | Mặc định trên hầu hết distro Linux. Ổn định, performance tốt. | Hầu hết trường hợp |
| **xfs** | `mkfs.xfs` | Mặc định trên RHEL/CentOS. Hiệu năng cao với file lớn. | Database, Big Data |
| **btrfs** | `mkfs.btrfs` | Hỗ trợ snapshot, RAID built-in. | Hệ thống cần snapshot |
| **ntfs** | `mkfs.ntfs` | Filesystem của Windows. | Ổ chia sẻ với Windows |
| **vfat / exfat** | `mkfs.vfat` | Tương thích chéo OS. | USB, thẻ nhớ |
| **swap** | `mkswap` | Không phải FS thật, dùng làm bộ nhớ ảo. | Phân vùng swap |

> ⚠️ **CẢNH BÁO:** Lệnh `mkfs` **xóa toàn bộ dữ liệu** trên phân vùng và không có cách hoàn tác. Luôn double-check tên phân vùng trước khi gõ Enter.

---

## 6. Công cụ `gparted` - Giao diện đồ họa

`gparted` (GNOME Partition Editor) là phiên bản GUI của `parted`. Khi chạy với quyền root, công cụ này cho phép:
- Xem trực quan tất cả ổ đĩa và phân vùng (kéo-thả thanh trượt).
- Tạo, xóa, di chuyển, **resize** phân vùng một cách an toàn.
- Format sang nhiều loại filesystem chỉ bằng vài cú click.
- Kiểm tra lỗi (check) phân vùng.

**Khi nào dùng GUI vs CLI?**
- **GUI (`gparted`):** Khi bạn ngồi trực tiếp trước máy có Desktop (Ubuntu Desktop, Fedora Workstation), hoặc khi setup máy ảo lần đầu.
- **CLI (`fdisk`/`parted`):** Khi quản lý server qua SSH (99% trường hợp DevOps thực tế — server không có GUI).

> 📌 **Best Practice:** DevOps Engineer **bắt buộc** phải thành thạo CLI. GUI chỉ là phương án dự phòng cho những trường hợp đặc biệt.

---

## 7. Workflow chuẩn: Từ ổ đĩa trống đến sẵn sàng dùng

Đây là **quy trình 5 bước** chuẩn khi bạn vừa gắn 1 ổ đĩa mới (ví dụ EBS Volume vừa attach vào EC2):

```text
[Ổ đĩa trống] ─┐
               │
        ┌──────▼──────┐
        │ 1. lsblk    │  ← Xác định tên thiết bị (/dev/xvdb)
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ 2. fdisk    │  ← Tạo bảng phân vùng (n → w)
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ 3. mkfs     │  ← Format thành filesystem (ext4)
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ 4. mkdir    │  ← Tạo mount point (/mnt/data)
        │    + mount  │     và gắn phân vùng vào đó
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ 5. /etc/fstab│  ← Cấu hình tự động mount khi boot
        └─────────────┘
```

(Bước 4 và 5 sẽ được học chi tiết ở tài liệu tiếp theo về **Mount & Filesystem Hierarchy**.)

---

## 8. Bài tập thực hành (Lab 3) - Loop Device an toàn 100%

Vì bạn đang dùng MacOS và không có Linux server thật, **không thể** chạy trực tiếp `fdisk` trên máy. Hãy thực hành theo 1 trong 2 hướng sau:

### Phương án A: Dùng Docker (Quan sát lý thuyết)
```bash
docker run -it --rm --privileged ubuntu:22.04 bash
apt update && apt install -y fdisk util-linux
fdisk -l
lsblk
```
> ⚠️ Lưu ý: Container chia sẻ ổ đĩa với host, nên đây chỉ là quan sát, không tạo phân vùng được.

### Phương án B: Dùng AWS EC2 + EBS (Thực hành thật)

> 🚨 **BẮT BUỘC ĐỌC TRƯỚC:** Nếu bạn chưa từng dùng AWS, **PHẢI** hoàn thành toàn bộ Mục 3 trong tài liệu [`[02-004]-aws-account-billing-protection.md`](./[02-004]-aws-account-billing-protection.md) ("Lá chắn 8 lớp" Billing Protection) **TRƯỚC** khi tạo EC2. Nếu skip bước này, bạn có rủi ro bị bill bất ngờ.

1. Setup billing protection theo `[02-004]` (làm 1 lần duy nhất, mất ~15 phút).
2. Khởi tạo 1 EC2 instance Ubuntu (Free Tier `t2.micro`) — chi tiết Mục 4.1 trong `[02-004]`.
3. Attach thêm 1 EBS Volume 1GB vào instance đó.
4. SSH vào instance.
5. Thực hiện các bước sau:

```bash
# Bước 1: Xác định ổ đĩa mới (thường là /dev/xvdf hoặc /dev/nvme1n1)
lsblk
sudo fdisk -l

# Bước 2: Tạo phân vùng bằng fdisk
sudo fdisk /dev/nvme1n1
# Trong tương tác: 
# - Tạo phân vùng 1 (500M): n → p → 1 → Enter → +500M
# - Tạo phân vùng 2 (phần còn lại): n → p → 2 → Enter → Enter
# - Ghi và thoát: w

# Bước 3: Ép OS nhận diện phân vùng mới (nếu cần)
sudo partprobe /dev/nvme1n1

# Bước 4: Format phân vùng vừa tạo
sudo mkfs.ext4 /dev/nvme1n1p1
sudo mkfs.xfs /dev/nvme1n1p2

# Bước 4: Tạo mount point và mount
sudo mkdir /mnt/mydisk
sudo mount /dev/xvdf1 /mnt/mydisk

# Bước 5: Kiểm tra
df -h | grep mydisk
sudo touch /mnt/mydisk/hello.txt
ls -la /mnt/mydisk
```

### Phương án C: Loop Device trên Linux VM (Khuyến nghị nếu có sẵn VM)
Nếu bạn có sẵn 1 Ubuntu VM (VirtualBox/UTM), đây là cách thực hành **an toàn 100%** vì không đụng vào ổ đĩa thật:

```bash
# Bước 1: Tạo 1 file 100MB rỗng làm "ổ đĩa giả"
dd if=/dev/zero of=~/fake_disk.img bs=1M count=100

# Bước 2: Gắn file đó thành 1 loop device (ổ đĩa ảo)
sudo losetup -fP ~/fake_disk.img
losetup -a  # Xem nó là /dev/loop0 hay loop1...

# Bước 3: Phân vùng ổ đĩa ảo (giả sử là /dev/loop0)
sudo fdisk /dev/loop0
# n → p → 1 → Enter → Enter → w

# Bước 4: Format phân vùng
sudo mkfs.ext4 /dev/loop0p1

# Bước 5: Mount và kiểm tra
sudo mkdir /mnt/loop_test
sudo mount /dev/loop0p1 /mnt/loop_test
df -h | grep loop

# Cleanup khi xong
sudo umount /mnt/loop_test
sudo losetup -d /dev/loop0
rm ~/fake_disk.img
```

### Câu hỏi tự kiểm tra (Self-check):
1. Sự khác biệt cốt lõi giữa MBR và GPT là gì? Khi nào bắt buộc phải dùng GPT?
2. Tại sao không thể có 5 Primary Partitions trên MBR? Cách "lách" là gì?
3. Khi mở rộng dung lượng phân vùng, tại sao phải tăng partition trước rồi mới resize filesystem (chứ không phải ngược lại)?
4. Ý nghĩa của Partition Type ID `82`, `83`, `8e` trong `fdisk` là gì?
5. Vì sao DevOps nên ưu tiên CLI (`fdisk`) thay vì GUI (`gparted`) trong môi trường production?

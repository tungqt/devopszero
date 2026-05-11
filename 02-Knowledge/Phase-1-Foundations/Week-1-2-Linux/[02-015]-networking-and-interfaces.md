# Giao diện Mạng và Networking Cơ bản (Networking and Network Interfaces)

## Document Information
- **Doc ID:** 02-015
- **Summary:** Tìm hiểu về giao thức mạng trên Linux, quy ước đặt tên giao diện mạng (Network Interfaces), và các lệnh/công cụ cơ bản để xem trạng thái mạng.
- **Version:** 0.0.1
- **Date:** 2026-05-09
- **PRD Reference:** N/A
- **Status:** Draft

---

## 1. Tổng quan (Overview)
**Tại sao DevOps cần hiểu về Networking trong Linux?**
Trong môi trường máy chủ, mọi dịch vụ đều giao tiếp thông qua mạng. Khi một ứng dụng web (Nginx) không thể kết nối tới Database (MySQL), hoặc một Server báo lỗi không có mạng, bạn phải biết cách kiểm tra các "Giao diện mạng" (Network Interfaces) trên máy chủ. Việc nắm vững cấu trúc, cách đặt tên và các công cụ hiển thị thông số mạng giúp bạn nhanh chóng chẩn đoán xem lỗi là do phần mềm (cấu hình sai port/socket) hay do thiết lập phần cứng (card mạng bị tắt, rớt gói tin).

## 2. Môi trường Thực hành (Environment Setup)
- **Môi trường:** Bất kỳ hệ thống Linux nào (Local VM hoặc AWS EC2).
- **Yêu cầu:** Tất cả các lệnh kiểm tra thông số mạng trong bài này đều có thể chạy an toàn bằng user thường (không cần `sudo`).

## 3. Lập trình Mạng và Sockets trong Linux

Phần lớn các chương trình mạng trên Linux được xây dựng dựa trên giao diện **socket**. Vì đây là một tiêu chuẩn chung, các chương trình tuân thủ chuẩn này hầu như không cần sửa đổi nhiều để chạy tốt trên Linux.

Bên cạnh đó, Linux bổ sung nhiều tính năng và họ giao thức nâng cao, ví dụ tiêu biểu là **Netlink**.
- **Netlink interface:** Cho phép mở kết nối socket trực tiếp giữa các phân hệ của Kernel (nhân hệ điều hành) với các ứng dụng User Space, hoặc giữa các phân hệ Kernel với nhau. 
- **Ứng dụng:** Cơ chế này được sử dụng rất hiệu quả để xây dựng các ứng dụng Tường lửa (Firewall) và Định tuyến (Routing) như `iptables` hay `iproute2`.

## 4. Tên gọi của Giao diện Mạng (Network Interfaces)

Mỗi máy chủ kết nối với mạng thông qua các Card mạng (Giao diện mạng - Interfaces). Việc đặt tên cho các giao diện này đã thay đổi rất nhiều theo thời gian để đáp ứng sự phức tạp của phần cứng hiện đại.

### 4.1. Chuẩn cũ (Tên tĩnh / Static names)
Trước đây, các kết nối mạng đơn giản và ít thay đổi (chủ yếu là mạng có dây). Tên thiết bị được gán theo thứ tự Kernel tìm thấy chúng lúc khởi động:
- **Mạng có dây (Ethernet):** `eth0`, `eth1`, `eth2`...
- **Mạng không dây (Wireless):** `wlan0`, `wlan1`...

**Vấn đề của chuẩn cũ:** Khi bạn có nhiều card mạng (đặc biệt là trên server hoặc cắm thêm USB Wifi), thứ tự Kernel phát hiện phần cứng trong lúc boot có thể thay đổi sau mỗi lần update Kernel hoặc đổi bản phân phối Linux. Điều này dẫn đến việc `eth0` hôm nay có thể bị biến thành `eth1` vào ngày mai, làm hỏng toàn bộ script quản trị mạng (iptables, routing).

### 4.2. Chuẩn mới (Tên dự đoán được / Predictable Network Interface Names)
Để giải quyết bài toán trên, các bản phân phối hiện đại (thông qua `systemd/udev`) đã chuyển sang cơ chế đặt tên **dự đoán được (Predictable)**. Tên thiết bị mạng giờ đây được sinh ra dựa trên các đặc điểm vật lý cố định của phần cứng, đảm bảo tên không bao giờ thay đổi dù bạn khởi động lại bao nhiêu lần.

Có 5 nguồn thông tin (scheme) có thể được dùng để sinh ra tên:
1. **Firmware / BIOS:** Do BIOS cung cấp (Ví dụ: `eno1` - Ethernet Onboard 1).
2. **PCI Express (Vị trí cắm):** Dựa trên khe cắm vật lý (Ví dụ: `enp2s0`, `enp4s2` - Ethernet cắm ở PCI bus 2, slot 0).
3. **Vị trí địa lý (Geographical):** Dựa trên topology của kết nối vật lý.
4. **MAC Address:** Dựa trên địa chỉ MAC duy nhất của phần cứng (Ít dùng làm tên vì quá dài và khó nhớ).
5. **Classic (Tên cổ điển):** Trở về cách gọi `eth0`, `wlan0` nếu tất cả các cách trên đều thất bại, hoặc nếu người quản trị cố tình cấu hình lại.

*(Bên cạnh đó, mọi máy chủ đều có giao diện **`lo` (Loopback)** mang IP `127.0.0.1`, dùng để máy chủ tự kết nối với các dịch vụ chạy trên chính nó).*

Là một DevOps, bạn buộc phải biết tên chính xác của card mạng (ví dụ `enp3s0`) trước khi cấu hình tường lửa hoặc định tuyến. Cách duy nhất để biết tên là sử dụng công cụ kiểm tra.

## 5. Các Công cụ Quản lý và Giám sát Mạng

Có 2 bộ công cụ chính để tương tác với giao diện mạng trên Linux:
- **`ifconfig` (thuộc gói `net-tools`)**: Công cụ truyền thống đã được sử dụng đáng tin cậy nhiều năm. Tuy nhiên hiện nay nó đã bị coi là lỗi thời. Nó sử dụng các lệnh hệ thống `ioctl` cũ.
- **`ip` (thuộc gói `iproute2`)**: Công cụ tiêu chuẩn hiện tại. Nó linh hoạt hơn, mạnh mẽ hơn và hiệu quả hơn rất nhiều vì nó sử dụng **netlink sockets** thay vì `ioctl` để giao tiếp với Kernel. 

Lệnh `ip` có thể dùng cho rất nhiều tác vụ khác nhau: xem/hiển thị thông tin thiết bị, kiểm soát (tắt/bật) thiết bị, định tuyến (routing), định tuyến theo chính sách (policy-based routing) và tạo đường hầm (tunneling).

Cú pháp cơ bản của lệnh `ip` là:
```bash
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

Để dễ hiểu hơn, bạn có thể tham khảo bảng giải thích chi tiết dưới đây:

| Thành phần | Ý nghĩa | Ví dụ |
|---|---|---|
| **`ip`** | Lệnh gọi công cụ. | `ip` |
| **`[ OPTIONS ]`** | Các tùy chọn làm thay đổi cách hiển thị kết quả (có thể bỏ qua). | `-s` (statistics - hiển thị thống kê), `-c` (color - hiển thị có màu), `-4` (chỉ hiện IPv4). |
| **`OBJECT`** | **Đối tượng** mạng mà bạn muốn thao tác. (Đây là phần quan trọng nhất). | `link` (thiết bị vật lý), `addr` (địa chỉ IP), `route` (bảng định tuyến). |
| **`COMMAND`** | Hành động bạn muốn thực hiện lên `OBJECT` đó. | `show` (hiển thị), `add` (thêm mới), `del` (xóa), `set` (chỉnh sửa). |

> 💡 **Mẹo:** Nếu bạn quên lệnh, chỉ cần gõ `ip OBJECT help` (ví dụ: `ip addr help`), hệ thống sẽ in ra gợi ý cách dùng lệnh cho đối tượng đó.

### 5.1. Bảng tra cứu nhanh (Cheat Sheet) các lệnh `ip` thường dùng

| Mục đích | Lệnh `ip` hiện đại | Lệnh `ifconfig`/cũ tương đương |
|---|---|---|
| **Xem danh sách card mạng** | `ip link` | `ifconfig` (không hiển thị card bị tắt) |
| **Xem địa chỉ IP** | `ip addr` hoặc `ip a` | `ifconfig` |
| **Xem thống kê (packet, byte)** | `ip -s link show eth0` | `ifconfig eth0` |
| **Bật card mạng** | `sudo ip link set eth0 up` | `sudo ifconfig eth0 up` |
| **Tắt card mạng** | `sudo ip link set eth0 down` | `sudo ifconfig eth0 down` |
| **Gán IP tĩnh** | `sudo ip addr add 192.168.1.7 dev eth0` | `sudo ifconfig eth0 192.168.1.7` |
| **Xóa IP khỏi card mạng** | `sudo ip addr del 192.168.1.7 dev eth0` | N/A |
| **Thay đổi MTU** | `sudo ip link set eth0 mtu 1480` | `sudo ifconfig eth0 mtu 1480` |
| **Xem bảng định tuyến (Route)**| `ip route` | `route -n` hoặc `netstat -rn` |
| **Thêm định tuyến (Add Route)**| `sudo ip route add 172.16.1.0/24 via 192.168.1.5` | `sudo route add -net 172.16.1.0 netmask 255.255.255.0 gw 192.168.1.5` |

*(Lưu ý: Đối với việc xin IP động DHCP trên các hệ thống dùng `net-tools` cũ, bạn sẽ dùng lệnh `sudo /sbin/dhclient eth0`. Tuy nhiên việc này thường được quản lý bởi Netplan hoặc NetworkManager trên Linux hiện đại).*

### 5.2. File hệ thống `/proc` và `/sys`
Tương tự như Memory hay CPU, Kernel cũng để lộ các số liệu thống kê mạng thô (raw) ra các file hệ thống để bạn hoặc các script giám sát có thể đọc:
- `/proc/net/dev`: Hiển thị toàn bộ thông kê mạng dưới dạng bảng rút gọn.
- `/sys/class/net/<tên_card>/statistics/`: Thư mục này chứa từng thông số riêng lẻ trên mỗi dòng (ví dụ đọc riêng số byte đã nhận của `eth0`).

---

## 6. Thực hành (Hands-on Lab)

**Mục tiêu:** Khảo sát các giao diện mạng trên máy chủ của bạn, tìm hiểu tên gọi của chúng và cách đọc thông số lưu lượng (Traffic).

**Bước 1:** Sử dụng lệnh `ip` để liệt kê các card mạng.
```bash
ip -s link
```
*Quan sát output. Tìm giao diện `lo` (loopback) và xem thông số `RX/TX`. Sau đó tìm giao diện mạng thật của bạn (có thể là `eth0`, `ens5`, hoặc `eno1` tùy vào EC2 hay VM).*

**Bước 2:** Đọc file thống kê thu gọn từ thư mục `proc`.
```bash
cat /proc/net/dev
```
*Bạn sẽ thấy một bảng với cột Receive và Transmit rất rõ ràng cho từng giao diện. Các công cụ giám sát đồ họa (như Datadog, Zabbix) thực chất cũng chỉ đang liên tục đọc file này để vẽ biểu đồ.*

**Bước 3:** Đọc một thông số cụ thể bằng thư mục `sys` (Lưu ý: hãy thay chữ `<interface_name>` bằng tên card mạng bạn tìm thấy ở Bước 1).
```bash
# Ví dụ nếu tên card mạng của bạn là eth0:
cat /sys/class/net/eth0/statistics/rx_bytes
```
*Kết quả trả về chỉ là một con số duy nhất. Đây là cách các bash script thường dùng để lấy chính xác một thông số mà không cần dùng `awk` hay `grep` để lọc.*

**Bước 4:** Thử nghiệm đọc lệnh `ip` (Không yêu cầu quyền root).
```bash
# Xem danh sách card mạng đơn giản
ip link

# Xem IP được cấp cho card mạng
ip addr
```

---

## 7. Lab Nâng cao: Cấu hình tĩnh Giao diện Mạng (Static Configuration)

**Mục tiêu:** Thử nghiệm việc ngắt mạng, gán một IP tĩnh bằng lệnh `ip`, sau đó khôi phục lại trạng thái nhận IP động (DHCP).

> 📌 **Cảnh báo (Warning):** **KHÔNG** thực hiện lab này trên kết nối SSH tới AWS EC2 hoặc Remote Server. Nếu bạn tự ngắt mạng (`down`) hoặc đổi IP tĩnh không khớp với dải mạng, kết nối SSH của bạn sẽ bị đứt ngay lập tức và bạn sẽ mất quyền điều khiển server. Chỉ thực hiện trên máy ảo Local (VMware/VirtualBox) nơi bạn có thể thao tác trực tiếp trên màn hình console.

### Bước 1: Lưu lại thông tin cấu hình hiện tại
Đầu tiên, bạn cần biết tên card mạng thật và IP nó đang dùng.
```bash
ip addr show
```
*(Giả sử card mạng của bạn tên là `eth0` và IP đang là `192.168.1.100`. Nếu tên khác, hãy thay thế `eth0` bằng tên tương ứng trong các lệnh dưới đây).*

### Bước 2: Tắt card mạng và gán IP tĩnh
Chúng ta sẽ giả lập việc cấu hình một IP tĩnh mới.

```bash
# 1. Tắt card mạng
sudo ip link set eth0 down

# 2. Xóa IP cũ (Tùy chọn, để đảm bảo sạch sẽ)
sudo ip addr flush dev eth0

# 3. Gán IP tĩnh mới (ví dụ: 192.168.1.100/24)
sudo ip addr add 192.168.1.100/24 dev eth0

# 4. Bật lại card mạng
sudo ip link set eth0 up
```

Kiểm tra lại xem IP mới đã nhận chưa:
```bash
ip addr show eth0
```

### Bước 3: Khôi phục cấu hình IP động (DHCP)
Để hệ thống tự động xin lại IP từ Router/DHCP Server (phục hồi như cũ):

```bash
# 1. Tắt card mạng
sudo ip link set eth0 down

# 2. Xóa IP tĩnh vừa gán
sudo ip addr flush dev eth0

# 3. Bật card mạng lên
sudo ip link set eth0 up

# 4. Yêu cầu DHCP Client xin cấp IP mới
sudo dhclient eth0
```

*(Lưu ý: Cách làm bằng dòng lệnh `ip` này chỉ có tác dụng tạm thời trên RAM. Nếu bạn khởi động lại máy (reboot), cấu hình sẽ trở về trạng thái lưu trong file cấu hình gốc của hệ điều hành như Netplan hoặc NetworkManager).*
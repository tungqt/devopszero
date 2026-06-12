# Cài đặt phần mềm và bản cập nhật (Installing Software and Updates)

## Document Information
- **Doc ID:** 02-051
- **Summary:** Bài học về **package** và **package manager**; định dạng **.deb** (Debian/Ubuntu/Mint) và **.rpm** (RHEL/CentOS/Fedora/openSUSE); công cụ **alien** chuyển đổi định dạng; lợi ích của package manager; GUI **Update Manager** (deb) và **PackageKit** (RPM); CLI **apt** (`update`, `upgrade`, `install`) và **yum** (`update`, `install`); ví dụ **pip install pandas**.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Cài đặt phần mềm và bản cập nhật". Sau khi xem video này, bạn sẽ có thể:

*   Mô tả **package** và **package manager**.
*   Phân biệt package cho distro **deb-based** và **RPM-based**.
*   Dùng package manager để **cài bản cập nhật**.
*   Dùng package manager để **cài phần mềm**.

## Package là gì?

Cả **bản cập nhật phần mềm** và **gói cài đặt** trên Linux thường được phân phối dưới dạng tập tin gọi là **packages**.

Package là **tập tin dạng lưu trữ (archive)** chứa các thành phần cần thiết để **cài phần mềm mới** hoặc **cập nhật phần mềm hiện có**.

## Package manager

Bạn dùng **package manager** để quản lý **tải xuống** và **cài đặt** các package.

Mỗi bản phân phối Linux có thể cung cấp **package manager khác nhau** — một số **dựa trên GUI**, một số là **công cụ dòng lệnh**.

## .deb và .rpm

**Deb** và **RPM** là các loại package mà package manager dùng trên Linux; đây là **hai kiểu tập tin khác nhau**, phục vụ các họ hệ điều hành Linux khác nhau.

| Định dạng | Phần mở rộng | Ví dụ distro |
|-----------|--------------|--------------|
| **Debian package** | `.deb` | Debian, **Ubuntu**, Mint, … |
| **RPM** | `.rpm` | CentOS / **RHEL**, **Fedora**, openSUSE, … |

*   **Deb** = **Debian** (tên định dạng gắn với Debian).
*   **RPM** = **Red Hat Package Manager**.

Hai định dạng **tương đương về vai trò** (đóng gói phần mềm/cập nhật); nội dung có thể được dùng trên hệ khác sau khi chuyển đổi. Nếu package bạn cần **chỉ có** ở định dạng kia, có thể chuyển bằng công cụ **alien**:

*   Chuyển từ **RPM sang deb**: dùng lệnh **`alien`** và chỉ rõ tên package (file `.rpm`) cần chuyển.
*   Chuyển sang **định dạng RPM**: dùng **`alien`** kèm tùy chọn **`-r`**.

## Lợi ích của package manager

*   **Tự giải quyết phụ thuộc (dependencies)** giữa các package.
*   **Thông báo** khi có bản cập nhật.
*   Package manager **GUI** có thể **tự kiểm tra** bản cập nhật bảo mật và phần mềm **theo lịch**.
*   Có thể **tự cài** các bản cập nhật hoặc để bạn **chọn** và cài những bản bạn muốn.

## Package manager dựa trên GUI

### Update Manager (hệ deb)

**Update Manager** là công cụ **GUI** để cập nhật hệ **deb-based**.

*   Mặc định thường **kiểm tra cập nhật hàng ngày**, **tự tải và cài các bản cập nhật bảo mật** hàng ngày; các cập nhật khác thường **hiển thị theo tuần**.
*   Bạn có thể **kiểm tra thủ công** bất cứ lúc nào.
*   Khi có cập nhật: **chọn** các bản muốn cài → nhấn **Install Updates** → nếu được hỏi, nhập **mật khẩu người dùng** và OK → cài **nền** trong khi bạn tiếp tục làm việc.

### PackageKit (hệ RPM)

**PackageKit** là công cụ **GUI** để cập nhật hệ **RPM-based**.

*   Khi có cập nhật, thường hiển thị **biểu tượng dạng “starburst”** ở vùng thông báo.
*   **Tự kiểm tra** cập nhật theo **khoảng thời gian cấu hình được**; cũng có thể kiểm tra **thủ công**.
*   Nhấp biểu tượng mở cửa sổ **Software Update**, liệt kê các cập nhật → chọn → **Install Updates** → nhập mật khẩu nếu được yêu cầu → cài nền.

## Package manager dòng lệnh: apt (deb)

**apt** là công cụ **dòng lệnh** để cập nhật/quản lý package trên hệ **deb-based**.

```bash
sudo apt update
```

*   Lệnh này **lấy danh sách** package có sẵn cho distro; đầu ra liệt kê các nguồn, **xây cây phụ thuộc**, và cho biết **có bao nhiêu package có thể nâng cấp**.

```bash
sudo apt upgrade
```

*   **Nâng cấp** tất cả các package đã cài (theo mô tả khóa học).

```bash
sudo apt install ten-goi
```

*   **Cài một package cụ thể** (`package_name` trong tài liệu gốc).

## Package manager dòng lệnh: yum (RPM)

**yum** (*Yellowdog Updater, Modified*) là công cụ **dòng lệnh** để cập nhật hệ **RPM-based**.

```bash
sudo yum update
```

*   Sau khi nhập mật khẩu, **yum** lấy danh sách cập nhật, **hiển thị tóm tắt**, và **hỏi xác nhận** trước khi tải.
*   Nếu xác nhận, yum **tải** các package cập nhật và **cập nhật** hệ thống; khi xong có thể hiển thị thông báo thành công kiểu **`Complete!`**.

## Cài phần mềm mới bằng dòng lệnh

*   Hệ **deb**: dùng **`apt`** với tùy chọn cài đặt (ví dụ `sudo apt install ten-goi`).
*   Hệ **RPM**: dùng **`yum`** với tùy chọn cài đặt (ví dụ `sudo yum install ten-goi`).

## Package manager cho môi trường Python (ví dụ)

Nhiều ứng dụng dùng package manager riêng — ví dụ **pip** hoặc **conda** để quản lý môi trường Python.

Giả sử bạn đã có môi trường Python và **pip**, có thể cài thư viện **pandas** (thường dùng cho xử lý dữ liệu):

```bash
pip install pandas
```

**pip** sẽ (theo mô tả video): tìm package pandas mới nhất, tải xuống, kiểm tra phụ thuộc và cập nhật nếu cần, rồi cài đặt. Khi xong, có thể hiển thị **số phiên bản** phần mềm mới.

## Tổng kết (Summary)

Trong video này, bạn đã học được rằng:

*   **`.deb`** và **`.rpm`** là hai kiểu tập tin package dùng trên Linux; phục vụ các họ distro khác nhau.
*   Hai định dạng **có thể chuyển đổi** lẫn nhau (ví dụ bằng **alien**, kèm **`-r`** khi cần tạo RPM).
*   **Update Manager** và **PackageKit** là các package manager **GUI** phổ biến trên distro **deb-based** và **RPM-based**, tương ứng.
*   **apt** và **yum** là các package manager **dòng lệnh** phổ biến trên **deb-based** và **RPM-based**, tương ứng.

# Các bản phân phối Linux (Linux Distributions)

## Document Information
- **Doc ID:** 02-044
- **Summary:** Bài học giới thiệu về khái niệm bản phân phối (distribution/distro) của Linux, cách phân biệt chúng và tổng quan về một số bản phân phối phổ biến (Debian, Ubuntu, Red Hat, Fedora, SUSE, Arch Linux).
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học Các bản phân phối Linux. Sau khi xem video này, bạn sẽ có thể:
*   Mô tả bản phân phối Linux là gì.
*   Phân biệt giữa một số bản phân phối Linux phổ biến.
*   Và xác định các trường hợp sử dụng (use cases) của một số bản phân phối Linux phổ biến.

## Bản phân phối Linux (Linux distribution) là gì?
Một bản phân phối Linux là một phiên bản (flavor) cụ thể của hệ điều hành Linux. Nó cũng có thể được gọi tắt là **distro**.

Tất cả các bản phân phối Linux đều phải sử dụng kernel Linux. Đây là thành phần cốt lõi của hệ điều hành Linux, cho phép hệ thống sử dụng phần cứng của máy tính một cách chính xác. Ngày nay, có hàng trăm bản phân phối Linux, mỗi bản được điều chỉnh (tailored) cho một đối tượng người dùng hoặc một nhiệm vụ cụ thể.

## Vậy điều gì làm các bản phân phối Linux khác biệt với nhau?
*   Mỗi bản phân phối Linux bao gồm một tập hợp các tiện ích mặc định (default utilities) riêng biệt nằm trong hệ điều hành, chẳng hạn như các lệnh và ứng dụng được đóng gói sẵn đi kèm với bản phân phối đó.
*   Mỗi bản phân phối có **giao diện người dùng đồ họa (GUI)** riêng mà bạn sử dụng để tương tác với hệ điều hành.
*   Mỗi bản phân phối hỗ trợ một tập hợp các lệnh cụ thể mà bạn có thể sử dụng trong một **shell** (một cửa sổ để nhập lệnh và nhận kết quả đầu ra).
*   Và cuối cùng, mỗi bản phân phối cung cấp **các mức độ hỗ trợ khác nhau**. Nó có thể được phát triển và quản lý dưới dạng một dự án được cộng đồng hậu thuẫn (community-backed project) hoặc được duy trì bởi một doanh nghiệp thương mại. Và nó có thể là một phiên bản hỗ trợ dài hạn (**LTS** - Long-term support), hoặc một phiên bản phát hành cuốn chiếu (**rolling release**), nơi các bản cập nhật package ổn định được phát hành liên tục theo lịch trình.

## Các bản phân phối Linux phổ biến

### Debian
**Debian** là một trong những bản phân phối có nguồn gốc lâu đời nhất, với bản phát hành đầu tiên vào năm 1993 (đặt tên là phiên bản 0.01) và bản phát hành chính thức, ổn định đầu tiên vào năm 1996 (phiên bản 1.1). Nó được biết đến vì tính ổn định, độ tin cậy và hoàn toàn là mã nguồn mở. Nó hỗ trợ nhiều kiến trúc máy tính (hoặc các loại phần cứng). Những tính năng này giúp Debian được đánh giá rất cao trong không gian máy chủ (server). Hơn nữa, Debian là bản phân phối do cộng đồng điều hành lớn nhất hiện có.

### Ubuntu
Một bản phân phối phổ biến khác là **Ubuntu**. Đây cũng là một bản phân phối có nguồn gốc sớm, với bản phát hành chính thức đầu tiên vào năm 2004. Ubuntu dựa trên Debian (Debian-based), nghĩa là nó được xây dựng trên nền tảng của Debian và sử dụng nhiều công cụ giống với hệ điều hành Debian. Ubuntu được phát triển và quản lý bởi công ty **Canonical**.
Ubuntu có ba phiên bản chính thức:
*   **Desktop:** Dành cho máy tính cá nhân, máy tính xách tay và máy trạm (workstations).
*   **Server:** Dành cho các máy chủ tập tin đơn giản hoặc các đám mây đa nút (multinode clouds).
*   **Core:** Dành cho Internet vạn vật (Internet of Things - IoT).

### Red Hat Linux (RHEL)
**Red Hat Linux**, giống như Debian, là một bản phân phối Linux "cốt lõi" (core), nghĩa là nó không được bắt nguồn từ một bản phân phối Linux nào khác. Red Hat được biết đến vì sự ổn định, đáng tin cậy và hoàn toàn mã nguồn mở. Nó được quản lý bởi Red Hat, một công ty con của IBM. Ngày nay, nó được xuất xưởng dưới tên gọi **Red Hat Enterprise Linux (hoặc RHEL)**, một phiên bản tập trung hoàn toàn vào khách hàng doanh nghiệp.

### Fedora
Tiếp theo là **Fedora**, được biết đến như một hệ điều hành ổn định hỗ trợ nhiều kiến trúc phần cứng. Nó cũng rất đáng tin cậy và an toàn, cung cấp các tính năng tường lửa (firewall) và bảo mật độc đáo. Nó được phát triển tích cực với một cộng đồng lớn mạnh và đang phát triển. Fedora được tài trợ bởi Red Hat. Do đó, Red Hat sử dụng phần lớn cơ sở mã (code base) của Fedora (tất nhiên là sau khi đã được thử nghiệm).

### SUSE Linux Enterprise (SLE)
Một bản phân phối Linux khác là **SUSE Linux Enterprise**. Còn được gọi là **SLE**, SUSE Linux Enterprise có sẵn ở hai phiên bản: Máy chủ (Server - SLES) và Máy tính để bàn (Desktop - SLED). Nó hỗ trợ nhiều kiến trúc phần cứng, chẳng hạn như ARM cho Raspberry Pi, và sử dụng SUSE Package Hub (cho phép người dùng cài đặt các package không chính thức thuộc SLE). Nó được duy trì bởi một công ty phần mềm mã nguồn mở nhỏ của Đức là SUSE.

### Arch Linux
Và cuối cùng, chúng ta có **Arch Linux**. Cách tiếp cận độc đáo, tự làm (do-it-yourself) của Arch Linux cho phép người dùng tùy chỉnh mọi thành phần của hệ thống. Điều này làm cho nó có khả năng cấu hình rất cao, một sự tự do khiến Arch rất được ưa chuộng. Nhưng điều đó cũng có nghĩa là bạn cần có sự hiểu biết sâu sắc về Linux và các công cụ hệ thống để có thể sử dụng Arch một cách hiệu quả. Và vì Arch không tập trung vào tính ổn định theo cách giống như các bản phân phối khác, nó có quyền truy cập dễ dàng vào các phần mềm mới nhất (nhưng có rất ít sự đảm bảo rằng chúng hoàn toàn ổn định).

## Tổng kết (Summary)
Trong video này, bạn đã học được rằng:
*   Các bản phân phối Linux có thể được phân biệt bởi giao diện người dùng, các ứng dụng shell của chúng, và cách hệ điều hành được hỗ trợ cũng như xây dựng.
*   Thiết kế của một bản phân phối Linux được điều chỉnh để phục vụ một đối tượng người dùng cụ thể.
*   **Debian** được đánh giá cao trong không gian máy chủ vì tính ổn định, độ tin cậy và vì là mã nguồn mở.
*   **Red Hat Enterprise Linux**, công ty con của IBM, tập trung hoàn toàn vào các khách hàng doanh nghiệp.
*   Và **SUSE Linux Enterprise** hỗ trợ nhiều kiến trúc phần cứng, chẳng hạn như ARM cho Raspberry Pi.
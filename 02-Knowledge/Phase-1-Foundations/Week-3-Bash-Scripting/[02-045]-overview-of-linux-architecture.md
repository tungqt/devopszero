# Tổng quan về Kiến trúc Linux (Overview of Linux Architecture)

## Document Information
- **Doc ID:** 02-045
- **Summary:** Bài học giới thiệu về kiến trúc của hệ thống Linux, bao gồm 5 lớp chính: Giao diện người dùng (UI), Ứng dụng (Applications), Hệ điều hành (OS), Kernel, và Phần cứng (Hardware). Bài học cũng giới thiệu tổng quan về hệ thống tập tin (filesystem) của Linux.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Tổng quan về Kiến trúc Linux". Sau khi xem video này, bạn sẽ có thể:
*   Liệt kê các lớp (layers) trong một hệ thống Linux.
*   Giải thích vai trò của từng lớp trong hệ thống Linux.
*   Và mô tả hệ thống tập tin (filesystem) của Linux.

Hệ thống Linux bao gồm năm lớp riêng biệt.
*   Lớp ngoài cùng của kiến trúc Linux là **UI, hay giao diện người dùng (user interface)**, cho phép người dùng tương tác với hệ thống bằng bàn phím hoặc chuột.
*   **Lớp ứng dụng (application layer)** bao gồm các system daemons, shells, ứng dụng người dùng (user apps), và các công cụ được sử dụng để thực hiện các tác vụ trong hệ thống Linux. Các ứng dụng giao tiếp với hệ điều hành để thực hiện các tác vụ.
*   **Hệ điều hành (OS)** chịu trách nhiệm cho các công việc quan trọng đối với sự ổn định của hệ thống, chẳng hạn như lập lịch tác vụ (job scheduling) và theo dõi thời gian.
*   Tất cả các hệ điều hành Linux đều được xây dựng trên nền tảng của **Linux kernel**, thực hiện các công việc cấp thấp quan trọng nhất. Kernel là thành phần cốt lõi của hệ điều hành và chịu trách nhiệm quản lý bộ nhớ, xử lý và bảo mật.
*   Kernel tương tác với **lớp phần cứng (hardware layer)**, bao gồm tất cả các thiết bị vật lý hoặc điện tử trong máy tính như bộ xử lý (processors), mô-đun bộ nhớ (memory modules), thiết bị đầu vào (input devices) và lưu trữ (storage).

## 1. Giao diện người dùng (User Interface - UI)
Lớp đầu tiên trong hệ thống Linux là giao diện người dùng. UI cho phép người dùng thực hiện các tác vụ bằng cách điều khiển các ứng dụng bằng bàn phím.
Các phiên bản Linux dành cho máy tính để bàn (Desktop) bao gồm **Giao diện người dùng đồ họa (Graphical User Interface - GUI)**, tương tự như Microsoft Windows. Điều này mở rộng chức năng của UI sang các thiết bị điều khiển khác, chẳng hạn như chuột.
Bạn có thể sử dụng máy Linux để:
*   Sử dụng trình duyệt web để gửi email cho bạn bè.
*   Hoặc sử dụng trình phát nhạc để nghe bài hát yêu thích của bạn.

## 2. Ứng dụng (Applications)
Ứng dụng là phần mềm cho phép người dùng thực hiện một tác vụ trong hệ thống Linux.
Ứng dụng có thể bao gồm:
*   Các công cụ hệ thống (System tools), chẳng hạn như trình biên dịch (compilers).
*   Ngôn ngữ lập trình (Programming languages).
*   Shells, là các ứng dụng đặc biệt thường là một phần của chính hệ điều hành.
*   Và ứng dụng người dùng (user apps), có thể là bất kỳ loại ứng dụng nào, từ trình duyệt, trình soạn thảo văn bản, đến trò chơi.

## 3. Hệ điều hành (Operating System - OS)
Hệ điều hành kiểm soát các công việc và chương trình quan trọng đối với sức khỏe và sự ổn định của hệ thống. Các chức năng của nó cũng bao gồm:
*   Gán phần mềm cho người dùng.
*   Phát hiện lỗi và thực hiện các biện pháp để ngăn chặn lỗi toàn hệ thống (complete system failures).
*   Và thực hiện quản lý tập tin (file management).

## 4. Linux Kernel
Trong một hệ thống Linux, hệ điều hành được xây dựng trên nền tảng của Linux kernel, thực hiện các hoạt động quan trọng nhất.
Kernel là phần mềm cấp thấp nhất trong hệ thống Linux và có toàn quyền kiểm soát hệ thống. Nó khởi động ngay khi máy tính của bạn khởi động và nằm trong bộ nhớ của máy tính trong khi hệ thống đang chạy. Nó cũng hoạt động như một cầu nối giữa các ứng dụng và phần cứng máy tính của bạn, cho phép cả hai giao tiếp hiệu quả bằng cách sử dụng các "lời gọi hệ thống" (system calls).
Kernel có bốn công việc chính:
*   Quản lý bộ nhớ (Memory management).
*   Quản lý tiến trình (Process management).
*   Quản lý trình điều khiển thiết bị (device drivers) để hỗ trợ phần cứng thích hợp.
*   Và đảm bảo hệ thống luôn an toàn (secure).

## 5. Phần cứng (Hardware)
Lớp cuối cùng trong hệ thống Linux là phần cứng, bao gồm các thiết bị vật lý hoặc điện tử tạo nên máy tính của bạn.
Lớp này có thể bao gồm:
*   Bộ xử lý trung tâm (Central processing unit - CPU), chịu trách nhiệm thực hiện hầu hết các tính toán.
*   Bộ nhớ truy cập ngẫu nhiên (Random access memory - RAM), là một đơn vị lưu trữ nhanh được sử dụng để chứa thông tin tạm thời mà các ứng dụng của bạn cần để chạy.
*   Lưu trữ (Storage) cho dữ liệu cần tồn tại khi máy tính của bạn tắt nguồn.
*   Màn hình (Screen) máy tính của bạn.
*   Và bất kỳ thiết bị USB nào như bàn phím, chuột hoặc ổ USB.

## Hệ thống tập tin Linux (Linux Filesystem)
Hệ thống tập tin Linux là tập hợp các tập tin trên máy của bạn. Nó bao gồm các tập tin cần thiết để chạy máy và các ứng dụng cũng như các tập tin riêng của bạn chứa công việc của bạn.
Cấp cao nhất của hệ thống tập tin là **thư mục gốc (root directory)**, được ký hiệu bằng dấu gạch chéo ngược (`/`).
Bên dưới nó là một cấu trúc giống như cái cây (tree-like structure) của các thư mục và tập tin trong hệ thống. Và hệ thống tập tin chỉ định các quyền truy cập (access rights) thích hợp cho các thư mục và tập tin.

Thư mục gốc chứa nhiều thư mục và tập tin khác.
*   Một trong những thư mục chính là `/bin`, chứa các tập tin nhị phân của người dùng (user binary files). Các tập tin nhị phân chứa mã mà máy của bạn đọc để chạy các chương trình và thực thi các lệnh. Nó được gọi là "slash bin" để biểu thị rằng nó tồn tại ngay bên dưới thư mục gốc.
*   Các thư mục chính khác bao gồm `/usr`, chứa các chương trình của người dùng.
*   `/home`, là thư mục làm việc cá nhân của bạn, nơi bạn nên lưu trữ tất cả các tập tin cá nhân của mình.
*   `/boot`, chứa các tập tin khởi động hệ thống của bạn, các hướng dẫn quan trọng để khởi động hệ thống.
*   Và `/media`, chứa các tập tin liên quan đến các phương tiện tạm thời (temporary media) như ổ đĩa CD hoặc USB được kết nối với hệ thống.

Có một số thư mục khác trong thư mục gốc, nhưng bạn sẽ không cần truy cập chúng trong khóa học này. Tất cả các tập tin và thư mục trong hệ thống Linux được tổ chức vào một trong những thư mục được chỉ định này, tùy thuộc vào mục đích của tập tin hoặc thư mục.

## Tổng kết (Summary)
Trong video này, bạn đã học được rằng:
*   Hệ thống Linux bao gồm năm lớp chính.
*   **Giao diện người dùng (User interface)** là lớp cho phép người dùng tương tác với các ứng dụng bằng các thiết bị điều khiển.
*   **Ứng dụng (Applications)** cho phép người dùng thực hiện các tác vụ nhất định trong hệ thống.
*   **Hệ điều hành (Operating system)** chạy trên nền tảng Linux kernel và rất quan trọng đối với sức khỏe và sự ổn định của hệ thống.
*   **Kernel** là phần mềm cấp thấp nhất và cho phép các ứng dụng tương tác với phần cứng của bạn.
*   **Phần cứng (Hardware)** bao gồm tất cả các thành phần vật lý hoặc điện tử của PC của bạn.
*   Và **Hệ thống tập tin Linux (Linux filesystem)** là một cấu trúc giống như cái cây bao gồm tất cả các thư mục và tập tin trên hệ thống.

# Giới thiệu về Linux và Unix (Introducing Linux and Unix)

## Document Information
- **Doc ID:** 02-043
- **Summary:** Bài học giới thiệu về hệ điều hành, nguồn gốc của Unix, sự ra đời của Linux và các đặc điểm nổi bật cũng như các trường hợp sử dụng phổ biến của Linux ngày nay.
- **Version:** 0.0.1
- **Date:** 2026-05-14
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với phần Giới thiệu về Linux và Unix. Sau khi xem video này, bạn sẽ có thể:
*   Giải thích hệ điều hành (OS) là gì
*   Mô tả nguồn gốc của Linux và Unix
*   Liệt kê một số tính năng của Linux và Unix

## Hệ điều hành là gì?
Đó là phần mềm quản lý phần cứng và tài nguyên máy tính, đồng thời cho phép tương tác với phần cứng để thực hiện các tác vụ hữu ích.

## Unix là gì?
Thực chất, đó là một họ các hệ điều hành. Một số hệ điều hành dựa trên Unix phổ biến bao gồm Oracle Solaris và OpenSolaris, FreeBSD, HP-UX, IBM AIX, và Apple MacOS - hiện đang là một trong những hệ điều hành máy tính để bàn phổ biến nhất hiện nay.

Vào những năm 1960, hệ điều hành Unix nguyên bản được tạo ra tại phòng thí nghiệm AT&T Bell Labs. Tuy nhiên, giống như nhiều hệ điều hành vào thời điểm đó, nó được tạo ra cho một hệ thống phần cứng cụ thể, trong trường hợp này là máy tính PDP-7.

Đến những năm 1970, hệ điều hành Unix được viết lại bằng ngôn ngữ C. Điều này làm cho nó trở nên khác biệt so với các hệ thống khác và giúp nó có khả năng linh hoạt (portable) để hoạt động trên nhiều kiến trúc phần cứng khác nhau.

Và sau đó, vào cuối những năm 1970, trường UC Berkeley đã phát triển Berkeley Software Distribution, hay gọi tắt là BSD. Đây là một tiện ích bổ sung (add-on) cho Unix cung cấp thêm phần mềm và các khả năng mới. Hệ điều hành MacOS nổi tiếng sau này được bắt nguồn từ chính BSD.

## Vậy Linux là gì?
Linux là một dòng hệ điều hành giống Unix (Unix-like). Tuy nhiên, khi nhắc đến Linux, mọi người thường nói về một bản phân phối (distribution) hoặc một phiên bản (flavor) cụ thể. Linux được phát triển như một nỗ lực nhằm tạo ra một phiên bản mã nguồn mở, miễn phí của hệ điều hành Unix.

Các tính năng chính của Linux bao gồm:
*   **Miễn phí và mã nguồn mở (Free and open-source):** Có nghĩa là bất kỳ ai cũng có thể xem mã nguồn. Và với rất nhiều người cùng xem xét mã nguồn đó, Linux đã trở thành hệ điều hành bảo mật nhất trong nhiều năm qua.
*   **Đa người dùng (Multi-user):** Linux được thiết kế để hỗ trợ nhiều người dùng truy cập vào hệ thống cùng một lúc.
*   **Hỗ trợ Đa nhiệm (Multitasking):** Có khả năng chạy nhiều công việc (jobs) và ứng dụng cùng lúc.
*   **Tính di động (Portable):** Linux đã được chuyển đổi (ported) để chạy trên nhiều loại thiết bị và nền tảng phần cứng khác nhau, từ máy tính để bàn, máy chủ (servers), cho đến các thiết bị gia dụng (appliances).

## Linux bắt đầu như thế nào?
*   Vào những năm 1980, **GNU** được phát triển tại MIT. GNU là viết tắt của "GNU's Not Unix" (GNU không phải là Unix) và được tạo ra như một bộ công cụ mã nguồn mở, miễn phí nhằm thay thế các công cụ hệ thống Unix hiện có.
*   Vào năm 1991, **Linus Torvalds** đã phát triển một phiên bản kernel (nhân) giống Unix, mã nguồn mở và miễn phí gọi là **Linux**. Kernel là thành phần cốt lõi của một hệ điều hành, cho phép các thành phần giao tiếp với phần cứng của máy. (Ông đã đăng một bài viết nổi tiếng để chia sẻ tiến trình tạo kernel riêng của mình, trong đó có nhắc đến Minix, một kernel giống Unix khác vào thời điểm đó).
*   Ngay sau đó, vào năm 1992, tiềm năng của việc hợp nhất các công cụ GNU và kernel Linux đã được nhận ra, khi các hệ điều hành Linux phổ biến bắt đầu xuất hiện.
*   Và vào năm 1996, một nhà khoa học máy tính tên là Larry Ewing đã tạo ra chú chim cánh cụt Tux, sau này được Linus Torvalds nhận làm linh vật chính thức của Linux.

Ngày nay, hệ điều hành MacOS (dựa trên BSD) đang chạy trên hàng triệu thiết bị trên toàn thế giới. Hàng tỷ phiên bản Linux chạy trên các máy chủ, phục vụ cho nền tảng web hiện đại. Và đặc biệt là đối với các nhà phát triển (developers), các hệ điều hành Linux hiện đại như Ubuntu đang bắt đầu trở nên phổ biến trong không gian máy tính cá nhân (PC).

## Các trường hợp sử dụng (Use Cases) phổ biến nhất của Linux hiện nay là gì?
*   Đầu tiên, nó được sử dụng trong **hàng tỷ điện thoại thông minh** trên khắp thế giới thông qua hệ điều hành Android (vốn sử dụng kernel dựa trên Linux).
*   Linux cũng được sử dụng rộng rãi trong các **siêu máy tính (supercomputers)**, nơi nhiều máy chủ chạy Linux được nhóm lại với nhau cho các ứng dụng tính toán hiệu năng cao.
*   Các **trung tâm dữ liệu doanh nghiệp và đám mây (Cloud & Enterprise Data Centers)** cũng sử dụng Linux trên hàng triệu máy chủ, để chạy đủ loại ứng dụng, máy chủ web, cơ sở dữ liệu, v.v.
*   Và cuối cùng, các phiên bản của Linux có thể dễ dàng được sử dụng làm **hệ điều hành cho PC cá nhân**. Nhiều người ngày nay cài đặt Linux như một trải nghiệm học tập hoặc làm hệ điều hành sử dụng hàng ngày (daily driver).

## Tổng kết (Summary)
Trong video này, bạn đã học được rằng:
*   Unix là một dòng hệ điều hành có từ những năm 1960.
*   Linux ban đầu được phát triển vào năm 1991 như một giải pháp thay thế miễn phí, mã nguồn mở cho Unix.
*   Linux là hệ điều hành đa người dùng, di động (portable) và hỗ trợ đa nhiệm.
*   Linux ngày nay được sử dụng rộng rãi trong các thiết bị di động, siêu máy tính, trung tâm dữ liệu và máy chủ đám mây.
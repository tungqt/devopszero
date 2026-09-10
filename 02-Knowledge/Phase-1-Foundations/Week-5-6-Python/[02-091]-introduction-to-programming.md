# [02-091] Introduction to Programming and Scripting

## Document Information
- **Doc ID:** 02-091
- **Summary:** Khái niệm cơ bản về lập trình và scripting, cùng với tư duy cần thiết khi bắt đầu viết code tự động hóa trong môi trường IT.
- **Version:** 0.0.1
- **Date:** 2026-06-14
- **PRD Reference:** N/A
- **Status:** Draft

---

## Mở đầu
Lập trình và scripting là kỹ năng cốt lõi trong DevOps dùng để thay thế các thao tác thủ công bằng mã lệnh tự động. Việc nắm vững tư duy lập trình giúp kỹ sư IT quản lý cấu hình, triển khai hạ tầng và xử lý sự cố một cách hệ thống và có thể lặp lại (repeatable).

## 1. Mô hình: Lập trình vs. Scripting (Structure)

Lập trình (Programming) là quá trình tạo ra một tập hợp các chỉ dẫn (recipe of instructions) để máy tính thực thi. Giống như ngôn ngữ con người, ngôn ngữ lập trình cũng được cấu thành từ hai yếu tố cốt lõi:
- **Cú pháp (Syntax):** Quy tắc viết code (giống như ngữ pháp). Ví dụ: cách khai báo biến, cách đóng mở ngoặc.
- **Ngữ nghĩa (Semantics):** Ý nghĩa và kết quả của đoạn code đó khi chạy. Máy tính luôn thực thi *chính xác* những gì được viết, do đó lập trình viên phải đảm bảo ngữ nghĩa rõ ràng.

Trong ngữ cảnh IT và DevOps, ranh giới giữa ngôn ngữ lập trình và scripting thường được phân loại theo mục đích sử dụng:

| Đặc điểm | Programming (Lập trình) | Scripting (Kịch bản) |
|----------|-------------------------|----------------------|
| **Khái niệm** | Xây dựng ứng dụng, phần mềm phức tạp. | Là một dạng chương trình ngắn, đơn giản, chu kỳ phát triển nhanh (rapid deployment). |
| **Mục đích chính** | Tạo ra sản phẩm độc lập, hệ thống lớn. | Tự động hóa tác vụ, kết nối các công cụ có sẵn, xử lý dữ liệu nhanh. |
| **Cách thực thi** | Thường được biên dịch (Compiled) thành mã máy trước khi chạy (VD: Go, C++, Java). | Thường được thông dịch (Interpreted) chạy trực tiếp từng dòng (VD: Python, Bash). |
| **Độ phức tạp** | Cao, đòi hỏi kiến trúc phần mềm chặt chẽ. | Thấp đến trung bình, tập trung vào việc giải quyết nhanh một vấn đề cụ thể. |
| **Ví dụ trong DevOps**| Viết một công cụ CLI mới, xây dựng microservice. | Viết script backup database, dọn dẹp log, trigger CI/CD pipeline. |

*(Lưu ý: Trong thực tế vận hành, hai thuật ngữ "Program" và "Script" thường được dùng thay thế cho nhau).*

## 2. Tư duy khi học lập trình (Application Mindset)

Dựa trên kinh nghiệm thực tế, quá trình viết code (đặc biệt là với người mới) tuân theo các nguyên tắc sau:
- **Code hiếm khi chạy đúng ở lần đầu tiên:** Lỗi (bug) là một phần tất yếu của quá trình phát triển. Việc đọc hiểu thông báo lỗi và sửa lỗi (debugging) quan trọng hơn việc cố gắng viết code hoàn hảo ngay từ đầu.
- **Học qua thực hành (Hands-on):** Cách tốt nhất để hiểu cú pháp là trực tiếp gõ lệnh, chạy thử và quan sát kết quả.
- **Kết nối các khái niệm:** Các kiến thức sẽ rời rạc lúc ban đầu, nhưng thông qua việc giải quyết các bài toán thực tế, chúng sẽ liên kết lại thành một hệ thống.

## 3. Tình huống thực tế (DevOps Scenario)

**Tình huống:** Một hệ thống server cần được dọn dẹp các file log cũ hơn 30 ngày để tránh đầy ổ cứng.
- **Không dùng Scripting:** Kỹ sư IT phải SSH vào từng server mỗi tuần, tự gõ lệnh tìm kiếm và xóa file. Dễ sai sót và tốn thời gian.
- **Dùng Scripting (Python/Bash):** Kỹ sư viết một đoạn script tự động tìm và xóa file, sau đó đặt lịch (cronjob) để script tự chạy mỗi ngày. Hệ thống được bảo trì tự động mà không cần sự can thiệp của con người.
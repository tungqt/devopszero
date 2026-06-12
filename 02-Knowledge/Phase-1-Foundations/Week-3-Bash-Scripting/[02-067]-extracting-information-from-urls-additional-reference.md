# Trích thông tin từ URL (bổ sung) (Extracting Information from URLs — Additional Reference)

## Document Information
- **Doc ID:** 02-067
- **Summary:** Tài liệu bổ sung — đăng ký **CoinStats Open API** (`openapi.coinstats.app`), tạo **API key**, gọi endpoint **`/coins`** bằng **`curl`** (header **Authorization**), trích dữ liệu JSON bằng **`grep`** (pipe từ URL hoặc file).
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Tài liệu này **bổ sung** cho bài pipe và ví dụ JSON (`[02-066]`): bạn có thể trích thông tin **trực tiếp từ URL** (phản hồi API) bằng lệnh `grep` tương tự khi đọc từ file.

## Mục tiêu

*   Lấy JSON từ API qua trình duyệt / **`curl`**.
*   Dùng **`grep -oE`** (và pipe) để trích field cần thiết — ví dụ `"price"`.

*(Cú pháp `grep` chi tiết: `[02-066]`; `curl`: `[02-061]`.)*

## Chuẩn bị: CoinStats Open API

### 1. Đăng nhập / đăng ký

Mở trình duyệt:

**https://openapi.coinstats.app**

Bạn sẽ được chuyển tới **trang đăng nhập**.

### 2. Xác minh tài khoản

*   Nhập **email** và **mật khẩu**.
*   **Xác minh email** theo hướng dẫn.
*   Sau khi xác minh → vào **dashboard**.

### 3. Tạo API key

Trên dashboard:

*   Bấm **Generate API Key**.
*   Hệ thống hiển thị key — **lưu key** ở nơi an toàn (không commit vào git).

### 4. Mở API Docs

Trên dashboard, menu trái → **API Docs**.

### 5. Endpoint `/coins`

*   Chọn endpoint **`/coins`**.
*   Trang docs hiển thị ví dụ **`curl`** trả về JSON.

### 6. Thử trên docs: **Try it**

*   Bấm **Try it** (góc phải trang endpoint).
*   **Bắt buộc:** dán **API key** vào ô header **`Authorization`** (đúng định dạng docs yêu cầu, thường dạng Bearer token).
*   Không authorize → request **không** chạy đúng.

### 7. Kết quả JSON

Sau khi authorize, docs trả **JSON** (danh sách coin, giá, v.v.) — dùng làm mẫu cho bước `grep` bên dưới.

## Trích dữ liệu bằng `grep`

### Từ file (ôn tập)

Lưu JSON mẫu (ví dụ từ docs hoặc response **Try it**) vào file, rồi:

```bash
cat Bitcoinprice.txt | grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

### Từ URL: `curl | grep`

Gọi API (thay `YOUR_API_KEY` và URL đúng theo **API Docs** hiện tại):

```bash
curl -s -H "Authorization: YOUR_API_KEY" \
  "https://openapi.coinstats.app/public/v1/coins" \
  | grep -oE "\"price\"\s*:\s*[0-9]*\.?[0-9]*"
```

| Thành phần | Vai trò |
|------------|--------|
| **`curl -s`** | Tải nội dung URL; `-s` tắt progress bar (dễ pipe) |
| **`-H "Authorization: ..."`** | Header xác thực (key từ dashboard) |
| **`\| grep -oE "..."`** | Chỉ in phần khớp regex (ví dụ trường `price`) |

**Lưu ý:** URL path và tên header có thể thay đổi theo phiên bản API — luôn copy **`curl`** từ **API Docs** sau khi đã authorize.

### Các field khác

Cùng mô hình, đổi pattern `grep`, ví dụ:

```bash
# Ví dụ: symbol BTC (điều chỉnh regex theo cấu trúc JSON thực tế)
curl -s -H "Authorization: YOUR_API_KEY" \
  "https://openapi.coinstats.app/public/v1/coins" \
  | grep -oE "\"symbol\"\s*:\s*\"[^\"]+\""
```

## Quy trình tóm tắt

```text
Đăng ký openapi.coinstats.app
    → Generate API Key (lưu an toàn)
    → API Docs → /coins → Try it (+ Authorization)
    → JSON mẫu
    → curl ... | grep -oE '...'   (hoặc cat file | grep ...)
```

## Bảo mật & DevOps

*   **Không** ghi API key vào script commit lên repo — dùng biến môi trường:

```bash
export COINSTATS_API_KEY="your-key-here"
curl -s -H "Authorization: $COINSTATS_API_KEY" "https://..."
```

*   **`grep` trên JSON lớn** dễ sai khi format đổi — production nên dùng **`jq`**:

```bash
curl -s -H "Authorization: $COINSTATS_API_KEY" "https://..." | jq '.coins[0].price'
```

*   Pipe **`curl | grep`** phù hợp lab và kiểm tra nhanh; CI/monitoring nên có xử lý lỗi HTTP (`curl -f`, kiểm tra exit code).

## Liên kết bài học

| Doc | Nội dung |
|-----|----------|
| `[02-066]` | `sort \| uniq`, `tr`, `grep -oE` trên file JSON |
| `[02-061]` | `curl`, tải URL |
| `[02-065]` | Pipe và filter |

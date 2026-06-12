# Tổng kết module Shell Scripting & điểm nhấn (Summary & Highlights)

## Document Information
- **Doc ID:** 02-072
- **Summary:** Tổng hợp sau khi hoàn thành module **Shell Scripting** — script/shebang, filter & pipe, biến shell/env, metacharacters, quoting, I/O redirection, command substitution, CLI args, concurrent mode, cron.
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chúc mừng bạn đã hoàn thành module này. Tại thời điểm này, bạn đã nắm được các nội dung cốt lõi sau.

## Shell script

*   **Shell script** là chương trình bắt đầu bằng chỉ thị **shebang** (`#!/bin/bash`, …), dùng để **chạy lệnh** và **gọi chương trình** khác.
*   Ngôn ngữ kịch bản được **thông dịch** (*interpreted*), **không** biên dịch trước như C/Java — phát triển nhanh hơn, chạy thường chậm hơn ngôn ngữ compiled.

*(Chi tiết: `[02-064]`.)*

## Filter và pipe

*   **Filter** là lệnh shell: stdin → stdout (ví dụ `sort`, `grep`, `wc`, `tr`).
*   Toán tử **pipe** **`|`** nối filter — đầu ra lệnh trước là đầu vào lệnh sau (**pipeline**).

| Ví dụ | Ý nghĩa |
|--------|---------|
| `sort file \| uniq` | Dòng unique sau khi sắp xếp |
| `curl -s URL \| grep pattern` | Trích field từ phản hồi API |

*(Chi tiết: `[02-065]`, `[02-066]`, `[02-067]`.)*

## Biến shell và môi trường

| Loại | Gán / đọc | Liệt kê |
|------|-----------|---------|
| **Shell variable** | `NAME=value` (không space quanh `=`), `$NAME` | `set` |
| **Environment variable** | `export NAME` (sau khi gán) | `env` |

Biến môi trường có **phạm vi mở rộng** — kế thừa bởi **tiến trình con**.

*(Chi tiết: `[02-065]`.)*

## Metacharacters, quoting, I/O

**Metacharacters** — ký tự đặc biệt shell hiểu theo nghĩa riêng (ví dụ `#`, `;`, `*`, `?`).

**Quoting** quyết định shell coi ký tự là metacharacter hay **literal**:

| Cơ chế | Tác dụng |
|--------|----------|
| `\` | Escape một ký tự |
| `"..."` | Literal trừ metacharacters (ví dụ `$VAR`) |
| `'...'` | Mọi ký tự literal |

**I/O redirection** — chuyển stdin/stdout/stderr:

| Ký hiệu | Ý nghĩa |
|---------|---------|
| `>` | stdout → file (ghi đè) |
| `>>` | stdout → file (append) |
| `2>`, `2>>` | stderr → file |
| `<` | file → stdin |

*(Chi tiết: `[02-068]`, `[02-069]`.)*

## Command substitution & đối số dòng lệnh

*   **Command substitution** — thay lệnh bằng output: `$(command)` hoặc `` `command` ``.
*   **Command-line arguments** — truyền dữ liệu vào script: `./script.sh arg1 arg2` → `$1`, `$2`, `$#`, …

*(Chi tiết: `[02-068]`, `[02-069]`.)*

## Chế độ chạy lệnh

| Chế độ | Cú pháp | Hành vi |
|--------|---------|---------|
| **Tuần tự (batch)** | `cmd1; cmd2` | `cmd2` sau khi `cmd1` xong |
| **Song song (concurrent)** | `cmd1 & cmd2` | `cmd1` chạy **nền**, `cmd2` foreground |

## Bash scripting nâng cao (chuẩn bị lab)

*   **`if` / `then` / `else` / `fi`** — điều kiện, toán tử `==`, `!=`, `-le`, `&&`, `||`
*   **`$(( ))`** — số học nguyên
*   **Arrays** — `arr=(...)`, `${arr[@]}`
*   **`for` loops** — duyệt mảng hoặc `for (( i=0; ... ))`

*(Chi tiết: `[02-070]`.)*

## Cron — lên lịch job

*   Cron job chạy **định kỳ** theo lịch đã chọn.
*   **Cú pháp:** `m h dom mon dow command`  
  (phút, giờ, ngày tháng, tháng, thứ trong tuần + lệnh)
*   **`crontab -e`** — chỉnh sửa bảng cron (editor)
*   **`crontab -l`** — liệt kê mọi cron job trong crontab

| Thành phần | Vai trò |
|------------|---------|
| **cron** | Công cụ / khái niệm job đã lên lịch |
| **crond** | Daemon đọc crontab mỗi phút |
| **crontab** | File lịch + lệnh quản lý |

*(Chi tiết: `[02-071]`.)*

## Bản đồ bài học — module Shell Scripting

```text
[02-064]  Shell scripting basics (shebang, hello_world, chmod)
[02-065]  Filters, pipes, variables
[02-066]  Examples of pipes (sort|uniq, tr, grep JSON)
[02-067]  Extracting info from URLs (bổ sung)
[02-068]  Useful features of the Bash shell (video)
[02-069]  Examples of Bash shell features (reading)
[02-070]  Advanced Bash scripting (if, arrays, for)
[02-071]  Scheduling jobs using cron
[02-072]  Tổng kết module (tài liệu này)
```

**Module trước (lệnh shell thường dùng):** `[02-063]`

## Ghi chú DevOps — ôn nhanh

1. **Script** → shebang + `chmod +x` + đường dẫn tuyệt đối trong cron.  
2. **Pipeline** → `cmd | grep | sort` cho log và API nhanh; JSON nặng dùng `jq`.  
3. **Biến** → `export` chỉ khi process con cần; secret qua env, không hard-code.  
4. **Cron** → log `>> file 2>&1`; kiểm tra `crontab -l` sau deploy.  
5. **Final project** → ôn `[02-070]` (if, array, for) trước khi vào lab.

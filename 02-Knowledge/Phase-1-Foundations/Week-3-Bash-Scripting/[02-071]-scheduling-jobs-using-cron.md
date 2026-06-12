# Lên lịch job bằng Cron (Scheduling Jobs using Cron)

## Document Information
- **Doc ID:** 02-071
- **Summary:** **cron** / **crond** / **crontab**; cú pháp 5 trường (phút, giờ, ngày tháng, tháng, thứ); ví dụ load hàng ngày 00:00, backup Chủ nhật 02:00; **`crontab -e`**, **`-l`**; xóa job; editor nano (**Ctrl+X**, **y**).
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Chào mừng bạn đến với bài học "Lên lịch job bằng Cron". Sau khi xem video này, bạn sẽ có thể:

*   **Lên lịch** cron job với `crontab`.
*   **Mô tả** cú pháp cron.
*   **Thêm** và **gỡ** cron job.

## Vì sao cần cron?

Dù bạn là **sysadmin**, **data engineer**, hay **developer**, đôi khi cần chạy tác vụ **tự động** theo giờ cố định, ví dụ:

| Tác vụ | Lịch gợi ý |
|--------|------------|
| Script **load dữ liệu** | Mỗi ngày **00:00** (nửa đêm) |
| Script **backup** | Mỗi **Chủ nhật 02:00** |

Trên Linux và hệ **Unix-like**, tiện ích **cron** phục vụ việc đó.

## Cron, crond, crontab

| Thuật ngữ | Vai trò |
|-----------|---------|
| **cron** | Tên chung của công cụ chạy job đã lên lịch (lệnh shell hoặc **shell script**) |
| **crond** | **Daemon/dịch vụ** — mỗi phút đọc file crontab và **gửi** job đúng giờ |
| **crontab** | File **“cron table”** chứa lệnh + lịch; cũng là **lệnh** mở editor chỉnh file đó |

## Cú pháp một dòng cron

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── day of week (0–7, 0 và 7 = Chủ nhật)
│ │ │ └──── month (1–12)
│ │ └────── day of month (1–31)
│ └──────── hour (0–23)
└────────── minute (0–59)
```

| Vị trí | Ý nghĩa |
|--------|---------|
| 1 | **Phút** |
| 2 | **Giờ** |
| 3 | **Ngày trong tháng** |
| 4 | **Tháng** |
| 5 | **Thứ trong tuần** |

*   Mỗi cột phải có **số** hoặc **`*`** (wildcard = **bất kỳ**).
*   **`command`** — bất kỳ lệnh shell nào, kể cả gọi **script** (nên dùng đường dẫn đầy đủ trong production).

**Khoảng trắng thừa** giữa các cột thường **bị bỏ qua** — có thể căn cột cho dễ đọc.

### Ví dụ từ khóa học

```cron
30 15 * * 0  date >> sundays.txt
0  0  * * *   /path/to/load_data.sh
0  2  * * 0   /path/to/backup.sh
```

| Dòng | Ý nghĩa |
|------|---------|
| `30 15 * * 0` | **15:30 mỗi Chủ nhật** — append ngày hiện tại vào `sundays.txt` |
| `0 0 * * *` | **00:00 mỗi ngày** — chạy script load |
| `0 2 * * 0` | **02:00 mỗi Chủ nhật** — chạy script backup |

*(Đường dẫn script thay bằng path thực tế trên máy bạn.)*

## Chỉnh sửa crontab: `crontab -e`

```bash
crontab -e
```

*   Mở **editor mặc định** (ví dụ **GNU nano**).
*   File thường có **comment hướng dẫn** cú pháp cron sẵn.
*   Thêm dòng job mới → lưu → job vào **production** (crond sẽ chạy theo lịch).

### Lưu trong nano

1. **Ctrl+X** — thoát  
2. **y** — xác nhận lưu  
3. Enter — xác nhận tên file (nếu được hỏi)

## Xem danh sách job: `crontab -l`

```bash
crontab -l
```

Liệt kê **mọi cron job** và lịch của user hiện tại.

Tránh in cả khối comment dài:

```bash
crontab -l | tail
```

*(Hoặc `grep -v '^#'` để lọc dòng comment.)*

## Gỡ cron job

1. `crontab -e`  
2. **Xóa** dòng job tương ứng  
3. Lưu (**Ctrl+X**, **y**)

Không còn dòng đó → cron **không** chạy job nữa.

## Lưu ý môi trường cron

Job cron chạy với **môi trường tối thiểu** (PATH, biến có thể khác shell đăng nhập):

*   Dùng **đường dẫn tuyệt đối** cho script và lệnh (`/usr/bin/python3`, `/home/user/bin/load.sh`).
*   Redirect log: `0 0 * * * /path/script.sh >> /var/log/load.log 2>&1`
*   User **`root`** vs user thường — mỗi user có crontab riêng (`crontab -e` khi đã `su`/`sudo -u`).

## Tóm tắt bài học

*   Cron job chạy **định kỳ** theo lịch 5 trường.
*   **cron** (công cụ), **crond** (daemon), **crontab** (bảng lịch + lệnh sửa) — vai trò khác nhau.
*   **`crontab -e`** — mở editor thêm/sửa job.
*   **`crontab -l`** — xem job đã đăng ký.
*   Xóa dòng trong crontab — **gỡ** job.

## Ghi chú DevOps

*   Backup / ETL đêm: luôn ghi **log** và kiểm tra mail cron (nếu bật) hoặc monitoring.
*   Tránh chồng lịch: hai job nặng cùng phút có thể quá tải CPU/IO.
*   Thay thế / bổ sung: **systemd timers**, orchestrator (Kubernetes CronJob), CI scheduled pipeline — tùy quy mô.
*   Kiểm tra crond đang chạy: `systemctl status cron` (Debian/Ubuntu) hoặc `crond` trên RHEL.

## Liên kết

| Doc | Nội dung |
|-----|----------|
| `[02-064]` | Shell script, shebang |
| `[02-068]` | Redirection `>>`, `2>&1` cho log cron |
| `[02-070]` | Script nâng cao cho job tự động |

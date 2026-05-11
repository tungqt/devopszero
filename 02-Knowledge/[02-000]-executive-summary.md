# Executive Summary - Knowledge

## Document Information
- **Doc ID:** 02-000
- **Summary:** Tổng quan về thư mục Knowledge, nơi chứa lộ trình và kiến thức DevOps.
- **Version:** 0.0.1
- **Date:** 2026-03-27
- **PRD Reference:** N/A
- **Status:** Draft

---

## Mục đích
Nơi chứa toàn bộ lý thuyết, khái niệm và roadmap học DevOps từ cơ bản đến nâng cao. Được chia thành các Phase (Chặng) rõ ràng để dễ dàng theo dõi tiến độ học tập.

## Directory Map
```text
02-Knowledge/
├── [02-000]-executive-summary.md  # Tổng quan thư mục Knowledge
└── Phase-1-Foundations/           # Nền tảng sống còn & Lập trình
    └── Week-1-2-Linux/            # Sống trong Terminal
        ├── [02-001]-linux-basics.md                       # Lệnh điều hướng, thao tác file, tìm kiếm
        ├── [02-002]-users-permissions-processes.md        # Quản lý User, phân quyền (chmod) và Process
        ├── [02-003]-disk-partitions-filesystems.md        # Phân vùng ổ đĩa (MBR/GPT), fdisk, mkfs
        ├── [02-004]-aws-account-billing-protection.md     # Setup AWS an toàn (MFA, Budgets, Anomaly) trước khi làm Lab
        ├── [02-005]-aws-ec2-creation-guide.md             # Hướng dẫn tạo EC2, SSH, Stop/Terminate và xử lý lỗi
        ├── [02-006]-partitioning-considerations.md        # Chiến lược phân vùng (/boot, /var, /tmp), LVM cơ bản
        ├── [02-007]-paths-and-environment-variables.md    # Biến $PATH, cơ chế tìm lệnh, đường dẫn tuyệt đối/tương đối
        ├── [02-008]-hard-and-soft-links.md                # Phân biệt Hard Link, Soft (Symbolic) Link và Inode
        └── [02-009]-system-components-overview.md         # Tổng quan Module 3: Boot Process, Memory (OOM Killer), Network
```
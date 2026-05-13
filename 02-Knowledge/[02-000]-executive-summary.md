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
        ├── [02-009]-system-components-overview.md         # Tổng quan Module 3: Boot Process, Memory (OOM Killer), Network
        ├── [02-010]-system-boot-process.md                # Quá trình Boot (BIOS, GRUB, Kernel)
        ├── [02-011]-system-initialization.md              # Khởi tạo hệ thống (SysVinit, systemd)
        ├── [02-012]-memory-management.md                  # Quản lý RAM, Cache, Buffer
        ├── [02-013]-swap-space.md                         # Swap Space và Lab OOM Killer
        ├── [02-014]-threading-models.md                   # Process vs Thread, Đa luồng
        ├── [02-015]-networking-and-interfaces.md          # Giao diện mạng, ip vs ifconfig
        ├── [02-016]-module-4-overview.md                  # Tổng quan Module 4: Monitoring, Device, Services
        ├── [02-017]-basic-commands-utilities.md           # Cheat sheet các lệnh cơ bản
        ├── [02-018]-file-transfer-tools.md                # FTP, curl, wget, scp, rsync
        ├── [02-019]-monitoring-performance-utilities.md   # CLI Monitoring: top, vmstat, mpstat
        ├── [02-020]-kernel-modules.md                     # Quản lý Kernel Module (lsmod, modprobe)
        ├── [02-021]-device-management.md                  # Quản lý thiết bị với udev
        ├── [02-022]-managing-system-services.md           # Quản lý dịch vụ với systemctl
        ├── [02-023]-stress-testing.md                     # Giả lập tải hệ thống với stress/stress-ng
        ├── [02-024]-module-5-overview.md                  # Tổng quan Module 5: Package, User/Group, su/sudo
        ├── [02-025]-system-installation.md                # Các phương pháp cài đặt Linux, Multi-boot, Live Media
        ├── [02-026]-graphical-package-management.md       # Quản lý Package bằng GUI (GNOME Software, Synaptic)
        ├── [02-027]-yast-package-management.md            # Quản lý Package bằng GUI trên openSUSE (YaST)
        ├── [02-028]-software-management-and-packaging.md  # Định dạng RPM/DEB và Low/High-level tools
        ├── [02-029]-using-rpm-demo.md                     # Thực hành lệnh rpm (Query, List files, Erase test)
        ├── [02-030]-using-dpkg-demo.md                    # Thực hành lệnh dpkg (list, listfiles, remove)
        ├── [02-031]-using-dnf-demo.md                     # Thực hành lệnh dnf (Install, Remove, Dependencies)
        └── [02-032]-upgrading-and-patching.md             # Nâng cấp, Vá lỗi và Cheat sheet các lệnh Package
```
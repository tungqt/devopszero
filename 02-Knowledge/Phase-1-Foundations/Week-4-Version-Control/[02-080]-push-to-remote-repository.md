# Push lên remote repository (Push to a Remote Repository)

## Document Information
- **Doc ID:** 02-080
- **Summary:** Hai cách bắt đầu làm việc với repo trên server: tải bản sao về máy, hoặc gắn repo cloud với project local rồi đẩy commit lên để đồng bộ với team; hướng dẫn cả terminal và Sourcetree.
- **Version:** 0.0.3
- **Date:** 2026-05-19
- **PRD Reference:** [02-003]-version-control-with-git-course-summary.md
- **Status:** Draft

---

Hai cách **bắt đầu** với remote phụ thuộc bạn **đã có** local repo + commit hay chưa. Sau khi liên kết, **push** ghi commit của một **nhánh** lên remote — nhánh local và remote chứa **cùng commit**. *(Remote trên Bitbucket: [02-079]; local + commit: [02-077], [02-078].)*

| Path | Làm phần |
|------|----------|
| **Command line** | § Path A |
| **Sourcetree** | § Path B |

---

## Block 1 — Mô hình (Structure)

### Hai kịch bản khởi đầu

```text
                    ĐÃ CÓ local repo + commit?
                              │
              ┌───────────────┴───────────────┐
              NO                              YES
              │                               │
              ▼                               ▼
        git clone <url>              git remote add origin <url>
        (tạo local + gắn origin)       (alias origin → URL Bitbucket)
              │                               │
              └───────────────┬───────────────┘
                              ▼
              git push [-u] origin <branch>   (vd. master)
```

| Tình huống | Hành động |
|------------|-----------|
| **Chưa** có local | **`git clone`** — tạo project directory + local repo + tham chiếu remote |
| **Đã** có local + commit | **`git remote add`** → **`git push`** |

**Clone:** danh từ = bản local của remote; động từ = tạo bản đó. Clone luôn gắn **reference** remote → sync sau bằng push/pull.

### `origin`, nhánh, push

```text
LOCAL                              REMOTE (Bitbucket)
master:  A ─── B ─── C             master:  (trống → sau push: A─B─C)
         │                                    ▲
         └──── git push -u origin master ─────┘

Sau push: master (local) và origin/master (remote) — cùng commit
```

| Khái niệm | Ý nghĩa |
|-----------|---------|
| **origin** | Alias mặc định thay URL trong lệnh (`git push origin` thay vì gõ URL) |
| **Branch** | Mọi commit thuộc một nhánh; mặc định **master** (repo mới có thể **main**) |
| **`git push`** | Ghi commit của nhánh local → nhánh cùng tên trên remote |
| **`-u` / `--set-upstream`** | Track local ↔ remote branch; Git báo khi **out of sync** |
| Lần push sau | Có thể chỉ `git push` — Git dùng default / giá trị đã set |

**Push** = chia sẻ với team + **backup** local. Push lần đầu lên remote **trống** thường OK; xung đột khi người khác đã push — học sau.

### Bitbucket sau khi tạo remote ([02-079])

| Tuỳ chọn trên Bitbucket | Bạn đang ở scenario |
|-------------------------|---------------------|
| **I'm starting from scratch** | Chưa có local → **clone** → README → commit → push |
| **I have an existing project** | Đã có local + commit → **remote add** → push |

---

## Block 2 — Lệnh & thao tác (Application)

**Prerequisite:** Remote trên Bitbucket ([02-079]); copy **git clone** command hoặc URL từ trang repo (Bitbucket/GitHub đều hiển thị).

---

### Path A — Command line

#### `git clone` — chưa có local

Trang hosting cung cấp lệnh clone hoặc URL — copy một trong hai.

```bash
cd ~/repos

# Chỉ URL — tên thư mục = tên repo trong URL (bỏ .git)
git clone https://bitbucket.org/workspace/helloworld.git
# → tạo ~/repos/helloworld/

# URL + tên thư mục local tùy chọn
git clone https://bitbucket.org/workspace/helloworld.git my-hello

cd helloworld
ls -la                     # .git/ + file trong working tree
git log --oneline          # commit từ remote đã có trên local
```

| Quy tắc | Chi tiết |
|---------|----------|
| Thư mục tạo ra | Trong cwd hiện tại (nên `cd ~/repos` trước) |
| Tên mặc định | Tên repo trong URL, **không** có `.git` |
| Tên tùy chỉnh | Thêm argument thứ hai sau URL |

#### Xem remote sau clone — `git remote`

```bash
git remote              # liệt kê tên remote (thường: origin)
git remote -v           # verbose: origin  fetch/push  <URL>
```

Dùng **`origin`** trong lệnh thay vì lặp lại URL.

#### `git remote add` — đã có local + commit

Ví dụ: local **`repoa`** đã commit; tạo remote **`repoa`** trống trên Bitbucket.

```bash
cd ~/repos/repoa

git remote add origin https://bitbucket.org/workspace/repoa.git
git remote -v
# origin  https://bitbucket.org/workspace/repoa.git (fetch)
# origin  https://bitbucket.org/workspace/repoa.git (push)
```

| Lệnh | Vai trò |
|------|---------|
| `git remote add <name> <url>` | Ghi thông tin remote vào local; sync qua **alias** |
| Tên thường dùng | **`origin`** |

#### `git push` — đẩy commit lên remote

```bash
git push -u origin master
# Lần đầu có thể hỏi Bitbucket username / password (HTTPS)

# Sau khi -u đã set:
git push
```

| Thành phần lệnh | Ý nghĩa |
|-----------------|----------|
| `-u` | **Set upstream** — track `master` ↔ `origin/master` |
| `origin` | Shortcut remote (lần đầu nên chỉ rõ) |
| `master` | Nhánh local cần push |

Git báo đã thiết lập **tracking relationship** sau push với `-u`. Một nhánh trên mỗi repo → repositories **synchronized**.

#### Flow Bitbucket: *I'm starting from scratch*

```bash
cd ~/repos
git clone <URL-from-Bitbucket>
cd repoa

echo "# repoa" > README.md
git add README.md
git commit -m "Initial commit"
git push -u origin master
```

`origin` được tạo khi **clone**. README trong commit → Bitbucket có thể hiển thị trên homepage repo.

#### Flow Bitbucket: *I have an existing project*

```bash
cd ~/repos/repoa                    # project directory local
git remote add origin <URL-from-Bitbucket>
git push -u origin master
```

Commit trên **master** local → ghi lên **master** remote.

#### Kiểm tra

Bitbucket → repo → tab **Commits** — xác nhận commit đã lên remote.

| Lệnh | Vai trò |
|------|---------|
| `git clone <url> [dir]` | Local mới + `origin` |
| `git remote add origin <url>` | Gắn remote vào local có sẵn |
| `git remote -v` | Xem alias ↔ URL |
| `git push -u origin master` | Push + track nhánh |
| `git push` | Push tiếp (sau `-u`) |

---

### Path B — Sourcetree

#### Clone từ Bitbucket

| Bước | Thao tác |
|------|----------|
| 1 | Bitbucket: repo → **+** → **Clone this repository** |
| 2 | **Clone in Sourcetree** |
| 3 | Xác nhận project directory + tên → **Clone** |
| 4 | Tab **Remotes** — **origin** |

**Settings → Remotes:** `origin` = shorthand cho URL.

#### Clone từ URL bất kỳ (vd. GitHub)

Copy URL → Sourcetree **New** → **Clone from URL** → dán URL → **Clone**.

#### Add remote vào local có sẵn

Right-click **Remotes** → **New remote** → name **origin**, paste URL → **OK**.

#### Push

**Push** → chọn **master** → **master** remote → bật **Track** → **OK**.

Thành công: commit label **master** + **origin/master**. Kiểm tra Bitbucket tab **Commits**.

*Tương đương:* `git push -u origin master`

---

### Lab khóa

1. **Clone** remote Bitbucket → local  
2. Tạo **commit** (vd. README)  
3. **Push** (`-u` lần đầu)  
4. Xác nhận trên Bitbucket **Commits**  

*(Hoặc local [02-077]–[02-078] + remote [02-079] → `remote add` + push.)*

---

## Block 3 — DevOps scenario (Use case)

### Scenario A: Đã code local — đẩy lên Bitbucket lần đầu

**Bối cảnh:** Terraform đã commit trên laptop; remote `infra-prod` trống.

```bash
cd ~/repos/infra-prod
git remote add origin https://bitbucket.org/team/infra-prod.git
git branch -M main          # nếu org dùng main
git push -u origin main
```

### Scenario B: Join team — bắt đầu từ remote

```bash
cd ~/repos
git clone git@bitbucket.org:team/app.git
cd app
# làm việc, commit, git push
```

**CI:** Runner `git clone` URL remote — không cần Sourcetree. **Track (`-u`):** `git status` báo ahead/behind trước khi deploy.

**Không nhầm:** **Clone** = bắt đầu từ remote; **remote add + push** = local trước, remote sau.

---

*Nguồn học: **(Command Line) Push to a Remote Repository** và **(Sourcetree) Push to a Remote Repository**, Module 1 — Version Control with Git.*

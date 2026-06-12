# Giới thiệu Bash scripting nâng cao (Introduction to Advanced Bash Scripting)

## Document Information
- **Doc ID:** 02-070
- **Summary:** Đọc chuẩn bị **final project lab** — **if/then/else/fi**, toán tử logic (`==`, `!=`, `-le`, `&&`, `||`); **arithmetic** `$(( ))`; **arrays** và indexing; **for loops** (duyệt phần tử, index, C-style `for (( ))`).
- **Version:** 0.0.1
- **Date:** 2026-05-16
- **PRD Reference:** [02-002]-hands-on-intro-linux-commands-shell-scripting-course-summary.md
- **Status:** Draft

---

Trong phần **hands-on lab** của **final project**, bạn sẽ dùng lệnh và khái niệm scripting **nâng cao hơn** so với phần video trước. Bài đọc này giúp làm quen trước khi vào lab.

## Mục tiêu

Sau khi đọc xong, bạn có thể viết Bash script:

*   Dùng **conditional** — chỉ chạy khối lệnh khi điều kiện đúng.
*   Dùng **toán tử logic** — so sánh true/false.
*   Thực hiện **phép tính số nguyên** cơ bản.
*   Tạo **array** dạng danh sách và truy cập phần tử.
*   Dùng **for loop** — lặp theo chỉ số hoặc từng phần tử.

*(Cơ bản shebang, biến, pipe: `[02-064]`–`[02-069]`.)*

## Conditionals (`if`)

**Conditional** (*if statement*) báo script làm việc **chỉ khi** điều kiện thỏa.

### Cú pháp `if-then-else-fi`

```bash
if [ condition ]
then
    statement_block_1
else
    statement_block_2
fi
```

| Nhánh | Hành vi |
|-------|---------|
| Điều kiện **đúng** | Chạy `statement_block_1`, thoát khối `if`, tiếp tục lệnh **sau** `fi` |
| Điều kiện **sai** | Chạy `statement_block_2` (khối `else`), rồi tiếp tục sau `fi` |

### Quy tắc quan trọng

*   **Luôn có khoảng trắng** quanh điều kiện trong `[ ]` hoặc `[[ ]]`.
*   Mỗi `if` phải có **`fi`** đóng khối.
*   **`else`** là tùy chọn; nếu không có `else` và điều kiện sai → khối `if` **không làm gì** (có thể `echo` trong `else` để debug).

### Ví dụ: số đối số dòng lệnh `$#`

```bash
if [[ $# == 2 ]]
then
  echo "number of arguments is equal to 2"
else
  echo "number of arguments is not equal to 2"
fi
```

`$#` = số đối số truyền vào script.

**`[` `]`** và **`[[` `]]`** đều hỗ trợ so sánh **số nguyên** với toán tử phù hợp.

### So sánh chuỗi

```bash
string_var="Yes"
if [ "$string_var" == "Yes" ]
then
  echo "match"
fi
```

So sánh chuỗi thường dùng **`[ ]`** (một cặp ngoặc vuông).

**Best practice:** quote biến — `"$string_var"` — tránh lỗi khi giá trị rỗng hoặc có space.

### Nhiều điều kiện: `&&` và `||`

```bash
if [ condition1 ] && [ condition2 ]
then
    echo "conditions 1 and 2 are both true"
else
    echo "one or both conditions are false"
fi
```

```bash
if [ condition1 ] || [ condition2 ]
then
    echo "conditions 1 or 2 are true"
fi
```

| Toán tử | Ý nghĩa |
|---------|---------|
| **`&&`** | **Và** — cả hai điều kiện phải đúng |
| **`\|\|`** | **Hoặc** — ít nhất một điều kiện đúng |

## Toán tử logic (so sánh số)

Dùng trong khối `if` (ví dụ với biến `a`):

| Toán tử | Ý nghĩa | Ví dụ |
|---------|---------|--------|
| **`==`** | Bằng | `[ $a == 2 ]` |
| **`!=`** | Khác | `[ $a != 2 ]` |
| **`<=`** | Nhỏ hơn hoặc bằng (trong `[[ ]]`) | `[[ $a <= 3 ]]` |
| **`-le`** | Tương đương `<=` trong **`[ ]`** | `[ $a -le $b ]` |

**`!`** — phủ định logic (đổi true ↔ false).

Ví dụ `-le`:

```bash
a=1
b=2
if [ $a -le $b ]
then
   echo "a is less than or equal to b"
else
   echo "a is not less than or equal to b"
fi
```

Còn nhiều toán tử (`-lt`, `-gt`, `-eq`, …) — xem [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/).

## Phép tính số học (arithmetic)

Phép **cộng, trừ, nhân, chia** số **nguyên** với **`$(( ))`**:

```bash
echo $((3+2))
```

Hoặc qua biến:

```bash
a=3
b=2
c=$(($a+$b))
echo $c
```

| Ký hiệu | Phép |
|---------|------|
| **`+`** | Cộng |
| **`-`** | Trừ |
| **`*`** | Nhân |
| **`/`** | Chia (số nguyên) |

**Lưu ý:** Bash **chỉ** làm số học **nguyên** — **cắt bỏ** phần thập phân.

```bash
echo $((3/2))    # in 1, không phải 1.5
```

Cần số thực → dùng `bc`, `awk`, hoặc Python.

## Arrays (mảng)

**Array** là cấu trúc **built-in** của Bash — danh sách phần tử **cách nhau bởi khoảng trắng**, đặt trong **ngoặc tròn**.

### Tạo và thêm phần tử

```bash
my_array=(1 2 "three" "four" 5)
```

Mảng rỗng:

```bash
declare -a empty_array
```

Thêm sau khi tạo:

```bash
my_array+=("six")
my_array+=(7)
```

### Truy cập phần tử

```bash
echo ${my_array[0]}    # phần tử đầu — index bắt đầu từ 0
echo ${my_array[2]}    # phần tử thứ ba
echo ${my_array[@]}    # tất cả phần tử
```

## `for` loops

### Duyệt từng giá trị trong mảng

```bash
for item in ${my_array[@]}; do
  echo $item
done
```

Cần **`; do`** … kết thúc bằng **`done`**.

### Duyệt theo chỉ số

```bash
for i in ${!my_array[@]}; do
  echo ${my_array[$i]}
done
```

`${!my_array[@]}` = danh sách **chỉ số** của mảng.

### Vòng lặp C-style (biết số lần lặp)

```bash
N=6
for (( i=0; i<=$N; i++ )) ; do
  echo $i
done
```

In **0** đến **6**.

### Ví dụ: đếm và cộng phần tử mảng

```bash
#!/usr/bin/env bash
my_array=(1 2 3)
count=0
sum=0
for i in ${!my_array[@]}; do
  echo ${my_array[$i]}
  count=$(($count+1))
  sum=$(($sum + ${my_array[$i]}))
done
echo "count=$count sum=$sum"
```

Chạy script (`chmod +x` rồi `./script.sh`) để thấy từng phần tử, tổng **count** và **sum**.

## Tóm tắt

*   **Conditional** — chạy lệnh khi điều kiện đúng; cú pháp `if` … `fi`.
*   **Toán tử logic** — so sánh true/false (`==`, `!=`, `-le`, `&&`, `||`).
*   **Arithmetic** — `$(( ))`, chỉ số nguyên.
*   **Arrays** — `name=(...)`, `${name[i]}`, `${name[@]}`.
*   **`for` loops** — lặp phần tử, lặp index, hoặc `for (( ))`.

Bạn đã sẵn sàng áp dụng trong **hands-on lab** / **final project**.

## Ghi chú DevOps (final project)

*   Kiểm tra **`$#`** hoặc `"$1"` trước khi xử lý — script fail rõ ràng thay vì im lặng.
*   Quote biến trong test: `[ "$var" == "yes" ]`.
*   Trong CI, kết hợp `if` với **exit code** lệnh trước: `if command; then ... fi`.
*   Array + `for` phù hợp danh sách host, file config — scale lớn thường chuyển sang cấu hình YAML + Ansible.

## Liên kết

| Doc | Nội dung |
|-----|----------|
| `[02-064]` | Shebang, chạy script |
| `[02-069]` | `$1`, `$#`, command-line arguments |
| `[02-068]` | Metacharacters, redirection |

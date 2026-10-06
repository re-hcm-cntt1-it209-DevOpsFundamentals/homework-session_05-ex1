# Bài 1: Khôi phục commit đã mất bằng Git Reflog

## 1. Mục tiêu

* Hiểu cách Git lưu trữ lịch sử hoạt động cục bộ thông qua Reflog.
* Sử dụng lệnh `git reflog` để truy vết các commit cũ.
* Mô phỏng tình huống commit bị mất sau khi thực hiện `git reset --hard`.
* Khôi phục commit đã mất bằng hash được tìm thấy trong Reflog.

---

## 2. Môi trường thực hiện

* Git
* Git Bash
* Repository local

Thư mục thực hiện:

```text
ss5/bai1/
```

---

## 3. Các bước thực hiện

### Bước 1: Khởi tạo Git Repository

Khởi tạo repository Git trong thư mục bài tập:

```bash
git init
```

---

### Bước 2: Tạo file và commit tính năng quan trọng

Tạo file `feature.txt`:

```bash
touch feature.txt
```

Nội dung file:

```text
Day la tinh nang quan trong
```

Kiểm tra nội dung:

```bash
cat feature.txt
```

Sau đó đưa file vào Staging Area:

```bash
git add .
```

Tạo commit:

```bash
git commit -m "Them tinh nang quan trong"
```

Commit được tạo với hash:

```text
8d3b1e1 Them tinh nang quan trong
```

Kiểm tra lịch sử:

```bash
git log --oneline
```

Kết quả:

```text
8d3b1e1 (HEAD -> master) Them tinh nang quan trong
```

---

### Bước 3: Tạo thêm một commit để mô phỏng lịch sử Git

Tạo file `base.txt`:

```bash
echo "Noi dung co ban" > base.txt
```

Đưa file vào Staging Area:

```bash
git add .
```

Tạo commit:

```bash
git commit -m "Tao noi dung co ban"
```

Commit được tạo với hash:

```text
46ff60c Tao noi dung co ban
```

Lúc này lịch sử Git gồm:

```text
46ff60c Tao noi dung co ban
8d3b1e1 Them tinh nang quan trong
```

---

### Bước 4: Giả lập việc làm mất commit

Thực hiện:

```bash
git reset --hard HEAD~1
```

Kết quả:

```text
HEAD is now at 8d3b1e1 Them tinh nang quan trong
```

Commit `46ff60c` không còn xuất hiện trong lịch sử hiện tại của branch.

Kiểm tra:

```bash
git log --oneline
```

Kết quả:

```text
8d3b1e1 (HEAD -> master) Them tinh nang quan trong
```

File `base.txt` cũng không còn trong working directory.

Kiểm tra:

```bash
ls
```

Kết quả:

```text
feature.txt
```

Như vậy, commit `46ff60c` đã bị loại khỏi lịch sử hiện tại sau khi thực hiện `git reset --hard`.

---

### Bước 5: Sử dụng Git Reflog để tìm commit đã mất

Sử dụng:

```bash
git reflog
```

Kết quả:

```text
8d3b1e1 (HEAD -> master) HEAD@{0}: reset: moving to HEAD~1
46ff60c HEAD@{1}: commit: Tao noi dung co ban
8d3b1e1 (HEAD -> master) HEAD@{2}: commit (initial): Them tinh nang quan trong
```

Từ Reflog có thể xác định commit đã bị mất:

```text
46ff60c HEAD@{1}: commit: Tao noi dung co ban
```

Hash `46ff60c` chính là commit cần khôi phục.

---

### Bước 6: Khôi phục commit đã mất

Sử dụng hash tìm được từ Reflog:

```bash
git reset --hard 46ff60c
```

Kết quả:

```text
HEAD is now at 46ff60c Tao noi dung co ban
```

Commit đã được đưa trở lại nhánh hiện tại.

---

### Bước 7: Kiểm tra kết quả khôi phục

Kiểm tra lịch sử commit:

```bash
git log --oneline
```

Kết quả:

```text
46ff60c (HEAD -> master) Tao noi dung co ban
8d3b1e1 Them tinh nang quan trong
```

Commit `46ff60c` đã xuất hiện trở lại trong lịch sử Git.

Kiểm tra các file:

```bash
ls
```

Kết quả:

```text
base.txt
feature.txt
```

Kiểm tra nội dung `base.txt`:

```bash
cat base.txt
```

Kết quả:

```text
Noi dung co ban
```

Nội dung `feature.txt` vẫn còn:

```bash
cat feature.txt
```

Kết quả:

```text
Day la tinh nang quan trong
```

---

## 4. Kết quả đạt được

Đã khôi phục thành công commit:

```text
46ff60c Tao noi dung co ban
```

Commit này trước đó đã bị loại khỏi lịch sử branch bằng:

```bash
git reset --hard HEAD~1
```

Sau đó sử dụng:

```bash
git reflog
```

để truy tìm hash của commit và khôi phục bằng:

```bash
git reset --hard 46ff60c
```

Sau khi khôi phục, commit và nội dung file của commit đã trở lại bình thường.

---

## 5. Kiến thức rút ra

### `git log`

Hiển thị lịch sử commit hiện tại của branch.

```bash
git log --oneline
```

Sau khi `reset --hard`, commit `46ff60c` không còn xuất hiện trong `git log`.

### `git reflog`

Theo dõi lịch sử thay đổi của các reference như `HEAD`.

```bash
git reflog
```

Reflog cho phép tìm lại commit mà branch hiện tại không còn trỏ tới.

### `git reset --hard`

Di chuyển reference của branch và đồng thời đưa working directory về đúng trạng thái của commit được chỉ định.

Trong bài này:

```bash
git reset --hard HEAD~1
```

được sử dụng để mô phỏng việc làm mất commit.

Sau đó:

```bash
git reset --hard 46ff60c
```

được sử dụng để khôi phục commit.

---

## 6. Kết luận

Git Reflog là cơ chế hữu ích để truy vết các trạng thái trước đây của `HEAD`. Ngay cả khi một commit không còn xuất hiện trong `git log` sau khi sử dụng `git reset --hard`, commit đó vẫn có thể được tìm thấy thông qua Reflog trong thời gian Git còn lưu giữ thông tin tham chiếu.

Quy trình khôi phục trong bài:

```text
git reset --hard HEAD~1
        ↓
Commit biến mất khỏi git log
        ↓
git reflog
        ↓
Tìm thấy 46ff60c
        ↓
git reset --hard 46ff60c
        ↓
Commit được khôi phục
```

**Kết quả: Hoàn thành bài 1 - Khôi phục commit đã mất bằng Git Reflog.**

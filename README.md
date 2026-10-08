# Bài 4: Lưu trữ dữ liệu đơn giản với Docker Volume

## 1. Giới thiệu & Mục tiêu
- **Mục tiêu:** Tạo một **Named Volume** trong Docker để lưu trữ tệp dữ liệu bền vững (persistence) giữa các lần chạy container.
- **Bối cảnh:** Kiểm tra và xác thực dữ liệu lưu trữ trong Volume không bị mất khi container dừng hoặc bị xóa (`--rm`).

---

## 2. Cấu trúc thư mục

```text
homework/session_09/ex4/
└── README.md
```

---

## 3. Các bước thực hiện

### Bước 1: Tạo Named Volume
Tạo một volume mới có tên là `easy-volume`:
```bash
docker volume create easy-volume
```

*Kết quả:*
```text
easy-volume
```

---

### Bước 2: Kiểm tra danh sách Volume trên máy
Kiểm tra xem volume `easy-volume` đã được khởi tạo thành công trên hệ thống hay chưa:
```bash
docker volume ls
```

*Kết quả mẫu:*
```text
DRIVER    VOLUME NAME
local     easy-volume
```

---

### Bước 3: Ghi dữ liệu vào Volume bằng Container Alpine thứ nhất
Khởi chạy container từ image `alpine`, gắn (mount) volume `easy-volume` vào thư mục `/data` và ghi nội dung `"Luu tru du lieu Docker"` vào tệp `/data/test.txt`:

```bash
docker run --rm -v easy-volume:/data alpine sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"
```

**Giải thích các tham số:**
- `--rm`: Tự động xóa bỏ container ngay sau khi lệnh kết thúc.
- `-v easy-volume:/data`: Gắn (mount) Named Volume `easy-volume` vào thư mục `/data` bên trong container.
- `alpine`: Sử dụng image Linux Alpine nhẹ, tiết kiệm tài nguyên.
- `sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"`: Thực thi lệnh ghi chuỗi ký tự vào file `test.txt`.

---

### Bước 4: Đọc lại dữ liệu bằng Container Alpine thứ hai
Khởi chạy container Alpine thứ hai (độc lập hoàn toàn với container đầu tiên đã bị xóa), mount cùng volume `easy-volume` vào thư mục `/data` để đọc lại nội dung tệp:

```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

---

## 4. Kiểm tra & Kết quả mong đợi

### Lệnh kiểm tra:
```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

### Kết quả mong đợi trên Terminal:
```text
Luu tru du lieu Docker
```

---

## 5. Kết luận
- Mặc dù container thứ nhất đã kết thúc và bị hủy bỏ hoàn toàn (`--rm`), dữ liệu được ghi vào file `test.txt` vẫn được giữ nguyên vẹn trên Named Volume `easy-volume`.
- Khi container thứ hai được khởi tạo và mount cùng volume, nó đọc được chính xác dữ liệu đã ghi trước đó.
- Cơ chế **Docker Volume** cho phép dữ liệu tồn tại độc lập với vòng đời của Container (Data Persistence), thích hợp cho việc lưu trữ cơ sở dữ liệu, file tải lên hoặc cấu hình chia sẻ.

---

## 6. Dọn dẹp tài nguyên (Tùy chọn)

Sau khi hoàn thành bài thực hành, nếu muốn dọn dẹp volume đã tạo, chạy lệnh:
```bash
docker volume rm easy-volume
```

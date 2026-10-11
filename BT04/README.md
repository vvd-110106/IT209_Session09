# Bài 4 – Lưu trữ Dữ liệu Đơn giản với Docker Volume

> **Session:** 09 – Tối ưu hóa dung lượng Image và Layer Caching với Multi-stage Build
> **Bài tập:** Lưu trữ dữ liệu đơn giản với Docker Volume

---

## Mục tiêu

Tạo một **Named Volume** để lưu trữ tệp dữ liệu **bền vững** giữa các lần chạy Container, kiểm chứng rằng dữ liệu không bị mất khi container dừng.

---

## Khái niệm

| Khái niệm | Giải thích |
|---|---|
| **Docker Volume** | Cơ chế lưu trữ dữ liệu bên ngoài container, do Docker quản lý |
| **Named Volume** | Volume được đặt tên cụ thể, dễ tái sử dụng giữa nhiều container |
| **--rm** | Tự động xoá container sau khi chạy xong |
| **-v easy-volume:/data** | Gắn (mount) volume `easy-volume` vào đường dẫn `/data` trong container |

---

## Các bước thực hiện

### Bước 1 – Tạo Named Volume

```bash
docker volume create easy-volume
```

### Bước 2 – Ghi dữ liệu vào Volume

Dùng container Alpine thứ nhất để ghi nội dung vào tệp trong volume:

```bash
docker run --rm -v easy-volume:/data alpine sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"
```

| Tham số | Ý nghĩa |
|---|---|
| `--rm` | Tự động xoá container sau khi lệnh thực thi xong |
| `-v easy-volume:/data` | Mount volume `easy-volume` vào thư mục `/data` trong container |
| `alpine` | Sử dụng image Alpine Linux (nhẹ, ~5MB) |
| `sh -c "echo ... > /data/test.txt"` | Ghi chuỗi văn bản vào tệp `/data/test.txt` |

### Bước 3 – Đọc lại dữ liệu từ Volume

Dùng container Alpine thứ hai (hoàn toàn mới) để đọc tệp từ cùng volume:

```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

---

## Kiểm tra kết quả

```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

**Kết quả mong đợi:**

```
Luu tru du lieu Docker
```

> Dữ liệu vẫn còn nguyên dù container đầu tiên đã bị xoá (`--rm`), chứng minh Volume lưu trữ dữ liệu bền vững độc lập với vòng đời container.

---

## Các lệnh kiểm tra bổ sung

```bash
# Xem danh sách volume trên máy
docker volume ls

# Xem thông tin chi tiết volume
docker volume inspect easy-volume
```

---

## Dọn dẹp tài nguyên (tuỳ chọn)

```bash
# Xoá volume (dữ liệu bên trong sẽ bị xoá vĩnh viễn)
docker volume rm easy-volume
```

---

## Ghi chú

- Dữ liệu trong **Named Volume** được Docker lưu tại `C:\ProgramData\docker\volumes\easy-volume\_data` (Windows) hoặc `/var/lib/docker/volumes/easy-volume/_data` (Linux).
- Vì container Alpine được chạy với `--rm`, container tự động bị xoá sau khi kết thúc nhưng **volume vẫn tồn tại**.
- Đây là cách chuẩn để chia sẻ dữ liệu giữa nhiều container hoặc giữ dữ liệu sau khi container bị xoá.

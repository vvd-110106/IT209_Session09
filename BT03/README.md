# Bài 3 – Đóng gói Trang Web HTML Tĩnh với Docker & Nginx


> **Session:** 09 – Tối ưu hóa dung lượng Image và Layer Caching với Multi-stage Build
> **Bài tập:** Viết Dockerfile 2 câu lệnh đóng gói trang Web HTML tĩnh

---

##  Mục tiêu

Viết tệp Dockerfile **siêu ngắn gọn** (2 câu lệnh) để tự đóng gói một trang HTML tĩnh thành Docker Image sử dụng Nginx Alpine, sau đó khởi chạy và kiểm tra container.

---

##  Cấu trúc thư mục

```
ex3/
├── Dockerfile      # Tệp cấu hình build Docker Image
├── index.html      # Trang HTML tĩnh
└── README.md       # Tài liệu hướng dẫn (file này)
```

---

##  Nội dung các tệp

### `index.html`

```html
<h1>Hello Docker Session 09!</h1>
```

### `Dockerfile`

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

**Giải thích từng lệnh:**

| Lệnh | Ý nghĩa |
|---|---|
| `FROM nginx:alpine` | Sử dụng image Nginx phiên bản Alpine làm base image (nhỏ gọn ~5MB, bảo mật) |
| `COPY index.html /usr/share/nginx/html/index.html` | Sao chép file HTML vào thư mục mặc định mà Nginx phục vụ nội dung tĩnh |

---

##  Các bước thực hiện

### Bước 1 – Build Docker Image

```bash
docker build -t my-html-app:v1 .
```

- `-t my-html-app:v1` — Gắn tag (tên:phiên bản) cho image vừa build
- `.` — Dockerfile nằm ở thư mục hiện tại

### Bước 2 – Khởi chạy Container

```bash
docker run -d -p 8081:80 --name html-app my-html-app:v1
```

| Tham số | Ý nghĩa |
|---|---|
| `-d` | Chạy container ở chế độ nền (detached mode) |
| `-p 8081:80` | Map cổng **8081** của máy host → cổng **80** bên trong container |
| `--name html-app` | Đặt tên cho container là `html-app` |
| `my-html-app:v1` | Tên image được sử dụng để tạo container |

### Bước 3 – Kiểm tra kết quả

```bash
curl http://localhost:8081
```

**Kết quả mong đợi:**

```
<h1>Hello Docker Session 09!</h1>
```

---

##  Kiểm tra trạng thái Container

```bash
# Xem danh sách container đang chạy
docker ps

# Xem log của container
docker logs html-app
```

---

##  Dọn dẹp tài nguyên (tuỳ chọn)

```bash
# Dừng container
docker stop html-app

# Xoá container
docker rm html-app

# Xoá image
docker rmi my-html-app:v1
```

---

##  Ghi chú

- **Nginx Alpine** là lựa chọn tối ưu để phục vụ file tĩnh vì image rất nhỏ (~5MB so với ~130MB của `nginx:latest`).
- Thư mục mặc định Nginx dùng để phục vụ nội dung HTML: `/usr/share/nginx/html/`.
- Dockerfile chỉ cần **2 dòng** vì Nginx Alpine đã có sẵn cấu hình mặc định phù hợp, không cần cấu hình thêm.

# Bài 4: Khai báo GitHub Secrets và cấu hình Đăng nhập Container Registry

## 1. Giới thiệu
Mục tiêu của bài tập:
- Tạo Personal Access Token (classic) với quyền `write:packages` trên GitHub.
- Cấu hình Repository Secret với tên `REGISTRY_TOKEN` để lưu trữ token an toàn.
- Cấu hình quy trình CI tự động trong [.github/workflows/ci.yml](file:///D:/Desktop/IT209/ss11/Bai4/.github/workflows/ci.yml) sử dụng `docker/login-action@v3` để đăng nhập vào GitHub Container Registry (`ghcr.io`).

---

## 2. Các bước thực hiện chi tiết

### Bước 1: Tạo Personal Access Token (PAT classic) trên GitHub
1. Trên tài khoản GitHub cá nhân, nhấn vào ảnh đại diện góc trên cùng bên phải -> chọn **Settings**.
2. Cuộn xuống dưới cùng ở thanh menu bên trái, chọn **Developer settings**.
3. Chọn **Personal access tokens** -> **Tokens (classic)**.
4. Bấm **Generate new token** -> chọn **Generate new token (classic)**.
5. Điền thông tin:
   - **Note**: `GHCR Access Token` (hoặc tên tùy chọn).
   - **Expiration**: Chọn thời hạn (ví dụ: 30 days hoặc tùy ý).
   - **Select scopes**: Tích chọn quyền **`write:packages`** (GitHub sẽ tự động chọn thêm quyền `read:packages`).
6. Bấm nút xanh **Generate token** ở cuối trang.
7. **Copy token ngay lập tức** (dạng `ghp_...`) và lưu lại, vì token chỉ hiển thị 1 lần duy nhất.

---

### Bước 2: Thêm Secret vào Repository GitHub
1. Truy cập vào Repository của bạn trên GitHub.
2. Chọn tab **Settings** của repository.
3. Ở menu bên trái, chọn **Secrets and variables** -> chọn **Actions**.
4. Tại mục **Repository secrets**, bấm **New repository secret**.
5. Nhập các giá trị:
   - **Name**: `REGISTRY_TOKEN` *(chú ý viết chính xác chữ hoa)*
   - **Secret**: Dán chuỗi Token vừa copy ở Bước 1 vào đây.
6. Bấm nút **Add secret**.
7. 👉 **Chụp ảnh màn hình mục Repository Secrets** hiển thị biến `REGISTRY_TOKEN` đã khởi tạo thành công để dùng làm sản phẩm nộp bài.

---

### Bước 3: Cấu hình Workflow GitHub Actions
File workflow đã được tạo tại [.github/workflows/ci.yml](file:///D:/Desktop/IT209/ss11/Bai4/.github/workflows/ci.yml):

```yaml
name: CI - Login to GHCR

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]
  workflow_dispatch:

jobs:
  login:
    name: Login to GitHub Container Registry
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

### Bước 4: Đẩy mã nguồn lên GitHub và kiểm tra kết quả
1. Khởi tạo / commit và đẩy mã nguồn lên GitHub:
   ```bash
   git init
   git add .
   git commit -m "feat: configure ghcr login with registry token secret"
   git branch -M main
   git remote add origin <URL_REPOSITORY_CUA_BAN>
   git push -u origin main
   ```
2. Trên giao diện GitHub của repository:
   - Chuyển sang tab **Actions**.
   - Bấm vào lần chạy workflow mới nhất (**CI - Login to GHCR**).
   - Bấm vào job **Login to GitHub Container Registry**.
   - Mở rộng bước **Đăng nhập vào GitHub Container Registry**.
   - Xác nhận log hiển thị dòng thông báo:
     ```text
     Login Succeeded
     ```

---

## 3. Sản phẩm nộp bài
1. **Tệp kịch bản CI**: `.github/workflows/ci.yml` (hoặc đặt theo đường dẫn cấu trúc yêu cầu của giảng viên: `homework/session_11/ex4/ci.yml`).
2. **Ảnh chụp màn hình**: Trang **Settings -> Secrets and variables -> Actions** hiển thị biến `REGISTRY_TOKEN` trong mục **Repository secrets**.

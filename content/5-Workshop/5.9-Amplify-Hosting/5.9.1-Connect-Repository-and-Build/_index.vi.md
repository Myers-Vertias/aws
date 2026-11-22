---
title: "Kết nối Repository Frontend và Cấu hình Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

## Mục tiêu

Kết nối Amplify tới repository chứa UniCloud DMS, không phải repository tài liệu workshop.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Kết nối đúng repository trong Amplify

Trong AWS Console, mở **AWS Amplify → Create new app → Host web app**. Chọn GitHub và cấp quyền truy cập repository nếu AWS hỏi. Tìm repository ứng dụng `serverless-document-management` rồi chọn nó. Repository `aws` chứa báo cáo, không phải mã frontend cần deploy.

### Bước 2: Chọn branch main và thư mục ứng dụng

Chọn branch `main`. Ở bước thiết lập build, để app root tại thư mục gốc repository vì `package.json` và `package-lock.json` nằm ở đó. Nếu Amplify hỏi monorepo, không khai báo thư mục con.

### Bước 3: Nhập lệnh build và thư mục kết quả

Đặt build command là `npm ci && npm run build` và artifact/output directory là `dist`. Đây là thư mục Vite tạo sau khi build.

Để refresh trực tiếp một route React Router không trả về 404, mở **Hosting → Rewrites and redirects → Manage redirects**. Thêm quy tắc rewrite 200 tới `/index.html`. Có thể dùng JSON SPA rule trong [tài liệu AWS Amplify](https://docs.aws.amazon.com/amplify/latest/userguide/redirect-rewrite-examples.html):

```json
[
  {
    "source": "</^[^.]+$|\\.(?!(css|gif|ico|jpg|js|png|txt|svg|woff|woff2|ttf|map|json|webp)$)([^.]+$)/>",
    "status": "200",
    "target": "/index.html",
    "condition": null
  }
]
```

Quy tắc này chuyển các đường dẫn của ứng dụng về `index.html` để React Router xử lý; các file tĩnh có phần mở rộng trong biểu thức được giữ nguyên.

### Bước 4: Lưu cấu hình và theo dõi build đầu tiên

Lưu cấu hình để Amplify bắt đầu build branch `main`. Mở build log và chờ trạng thái thành công. Nếu build lỗi, kiểm tra lệnh build, app root và `dist`. Thêm biến `VITE_*` trong Amplify trước lần redeploy theo mục 5.9.2.

## Minh chứng triển khai

Nhánh production `main` đã deploy trên Amplify; trạng thái nhánh và frontend được chụp ở mục 5.9.2. Không lưu màn hình chọn repository và build settings ban đầu.

## Kết quả mong đợi

Nhánh ứng dụng `main` đã deploy và phục vụ frontend. Không chụp riêng chi tiết repository/build settings.

## Khắc phục sự cố

- Không chọn nhầm repository tài liệu workshop.
- Với monorepo, đặt đúng thư mục gốc của ứng dụng.

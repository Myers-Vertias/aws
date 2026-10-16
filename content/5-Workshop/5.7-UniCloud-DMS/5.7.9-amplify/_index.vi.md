---
title: "Triển khai bằng AWS Amplify Hosting"
date: 2026-10-16
weight: 9
chapter: false
pre: " <b> 5.7.9 </b> "
---

## Mục tiêu

Host frontend React/Vite từ repository mã nguồn ứng dụng. **Không chọn nhầm repository tài liệu workshop**; hãy chọn repository chứa frontend UniCloud DMS và package.json của ứng dụng.

## Bước 1 — Kết nối repository frontend

1. Mở [AWS Amplify console](https://console.aws.amazon.com/amplify/).
2. Tạo ứng dụng mới và chọn quy trình kết nối Git provider.
3. Cho phép truy cập repository chứa frontend UniCloud DMS.
4. Chọn đúng repository frontend và branch muốn triển khai.
5. Xác nhận framework/build setting được nhận diện.

**[Vị trí ảnh 5.7.9-01 — Repository frontend và branch đã chọn]**  
Chụp tên repository và branch. Không đưa thông tin repository riêng không liên quan vào ảnh.

## Bước 2 — Cấu hình build Vite

Dùng package manager và lockfile được repository hỗ trợ. Với project hiện đang dùng npm, cấu hình build tương đương:

~~~yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: dist
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
~~~

Điều chỉnh nếu repository sử dụng package manager khác hoặc có cấu trúc monorepo. Thư mục đầu ra của frontend Vite tiêu chuẩn là **dist**.

## Bước 3 — Thêm biến môi trường frontend

Trong phần environment variables của Amplify, cấu hình các giá trị công khai cần cho trình duyệt, chẳng hạn Region, Cognito User Pool ID, Cognito App Client ID và API base URL.

- Dùng chính xác tên biến mà frontend đang tham chiếu.
- Không thêm AWS secret access key hoặc credentials riêng tư.
- Cập nhật callback/sign-out URL của Cognito và origin CORS của S3/API để chứa domain triển khai thật khi cần.
- Không mặc định rằng build frontend thành công đồng nghĩa backend endpoint truy cập được.

**[Vị trí ảnh 5.7.9-02 — Build setting và biến môi trường đã che giá trị nhạy cảm]**  
Chụp cấu hình build và tên biến, đồng thời che giá trị nhạy cảm.

## Bước 4 — Triển khai và xác minh

1. Bắt đầu deployment lần đầu.
2. Chờ trạng thái build và deployment báo thành công.
3. Mở URL Amplify được cấp.
4. Kiểm tra màn hình đăng nhập và ít nhất một luồng tài liệu cần xác thực.
5. Xác nhận API request dùng API URL triển khai và console trình duyệt không có lỗi cấu hình hoặc CORS.

**[Vị trí ảnh 5.7.9-03 — Deployment thành công và website hoạt động]**  
Chèn ảnh deployment thật sau khi xác minh.

## Kết quả mong đợi

Amplify phục vụ frontend qua HTTPS bằng domain được cấp và deployment được liên kết với đúng repository/branch frontend.

## Khắc phục sự cố

- **Build thất bại:** kiểm tra Amplify build log, cấu hình Node.js/package manager và lỗi lint/build.
- **Trang trắng hoặc refresh báo 404:** kiểm tra thư mục artifact và cấu hình SPA routing/rewrites cho route React Router.
- **API không hoạt động trên website triển khai:** cập nhật API base URL và CORS origin chính xác; kiểm tra authorizer và Network panel của trình duyệt.
- **Redirect đăng nhập thất bại:** cập nhật Cognito callback/sign-out URL khớp chính xác deployed origin.

## Lưu ý cleanup

Amplify hosting có thể phát sinh chi phí tùy mức sử dụng và gói tài khoản. Xóa ứng dụng Amplify sau khi kiểm thử nếu không còn cần dùng.

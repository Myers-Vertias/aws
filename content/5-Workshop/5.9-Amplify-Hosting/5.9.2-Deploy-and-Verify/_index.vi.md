---
title: "Cấu hình Môi trường, Deploy và Xác minh"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.9.2 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Triển khai frontend và xác minh xác thực cùng API call thật.

## Các bước thực hiện

1. Trong Amplify Console, mở app → Hosting → Environment variables và đặt năm biến `VITE_*` theo CloudFormation Outputs; không thêm secret access key. Lưu cấu hình để tạo deployment mới.
2. Lấy domain HTTPS của branch. Cập nhật `AllowedFrontendOrigins` trong SAM stack để bao gồm origin chính xác này; frontend hiện dùng Cognito API trực tiếp nên không cần managed-login callback URL.
3. Trong Amplify → branch, theo dõi build log đến trạng thái thành công; kiểm tra artifact directory `dist`, build command `npm ci && npm run build` và SPA rewrite.
4. Dùng domain production làm origin trong trình duyệt. CORS API và bucket phải trùng scheme/host của domain; URL API, Pool ID, Client ID và bucket phải lấy cùng một stack.

## Minh chứng triển khai và xác minh

![Ảnh 5.9.2: Nhánh production main đã deploy trên AWS Amplify](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-amplify-production-branch.png)

![Ảnh 5.9.2: Outputs của CloudFormation stack dms-dev sau khi triển khai](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-cloudformation-stack-outputs.png)

Parameter `AllowedFrontendOrigins` của stack đang triển khai có origin Amplify production và các origin Vite local. Lambda execution role ARN đã được che trong ảnh báo cáo.

![Ảnh 5.9.2: Frontend origins được cấu hình trên stack dms-dev](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-allowed-frontend-origins.png)

Ảnh kiểm tra API health được ghi ở mục 5.7.2. Đăng nhập, liệt kê tài liệu, upload/download và các thao tác tài liệu đã được thực hiện trên ứng dụng đã deploy; xem mục 5.8.2, 5.10.1 và 5.10.2. Không chụp thêm ảnh tổng hợp riêng vì các luồng này đã có minh chứng ở những mục đó.

## Kết quả mong đợi

Ứng dụng truy cập được qua HTTPS và hoàn thành luồng backend đã kiểm chứng.

## Khắc phục sự cố

- Build thành công không đảm bảo API kết nối; kiểm tra Network và CORS.
- Callback URL phải khớp chính xác origin triển khai.

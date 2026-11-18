---
title: "Cấu hình Môi trường và Xác thực"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.8.1 </b> "
---

{{% notice info %}}
Frontend đã cấu hình Cognito và API đang triển khai. Đăng nhập thật cùng request tài liệu có xác thực được ghi ở mục 5.8.2; không chụp `.env.local` vì chứa cấu hình riêng của môi trường.
{{% /notice %}}

## Mục tiêu

Cung cấp cấu hình công khai và kết nối frontend với xác thực Cognito thật.

## Các bước thực hiện

1. Từ CloudFormation Outputs lấy Region, `UserPoolId`, `UserPoolClientId`, `ApiBaseUrl`, `DocumentsBucketName`. Ở repository root, copy `.env.example` thành `.env.local` rồi điền các biến tương ứng: `VITE_AWS_REGION`, `VITE_COGNITO_USER_POOL_ID`, `VITE_COGNITO_USER_POOL_CLIENT_ID`, `VITE_API_BASE_URL`, `VITE_S3_BUCKET_NAME`.
2. Chạy `npm ci`, rồi `npm run dev`; sau mỗi lần đổi `.env.local`, khởi động lại Vite. Tệp local đã bị Git ignore và không được commit.
3. Auth service gọi Cognito User Pools API cho đăng ký, xác nhận email, đăng nhập, refresh session và đăng xuất. API client lấy ID token rồi gắn `Authorization: Bearer <token>` vào request.
4. Chỉ chứa public IDs/URLs trong biến `VITE_*`; tuyệt đối không đưa AWS secret access key, credentials, password hay token vào bundle.

## Xác minh

Dashboard local sau đăng nhập và request lấy danh sách tài liệu có xác thực được ghi ở mục 5.8.2. Không lưu ảnh riêng màn hình đăng nhập; báo cáo không chứa mật khẩu, mã xác nhận hoặc token.

## Kết quả mong đợi

Đã xác minh đăng nhập Cognito thật và truy cập API được bảo vệ. `.env.local` chứa cấu hình riêng nên không đưa vào ảnh hoặc Git.

## Khắc phục sự cố

- Khởi động lại Vite sau khi đổi giá trị .env.
- Không log hay commit token session đầy đủ.

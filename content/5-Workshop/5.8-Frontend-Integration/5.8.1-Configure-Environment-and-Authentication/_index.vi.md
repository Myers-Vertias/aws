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

1. Tạo `.env.local` với Region `ap-southeast-1`, User Pool `ap-southeast-1_hWIAdC6Xu`, App Client `epodlpjbopbdc809elojsad3a` và API `https://6wcs1urcm2.execute-api.ap-southeast-1.amazonaws.com`. Tệp này bị Git bỏ qua; `.env.example` chỉ chứa placeholder.
2. Frontend gọi Cognito User Pools JSON API để đăng ký, xác nhận email, đăng nhập, làm mới phiên và đăng xuất. Mật khẩu tối thiểu 12 ký tự theo chính sách User Pool.
3. API client tự lấy Cognito ID token và gắn `Authorization: Bearer` vào request đến API Gateway.
4. Các biến `VITE_*` là cấu hình công khai, không chứa AWS secret key. Khởi động lại Vite sau khi đổi environment.

## Xác minh

Dashboard local sau đăng nhập và request lấy danh sách tài liệu có xác thực được ghi ở mục 5.8.2. Không lưu ảnh riêng màn hình đăng nhập; báo cáo không chứa mật khẩu, mã xác nhận hoặc token.

## Kết quả mong đợi

Đã xác minh đăng nhập Cognito thật và truy cập API được bảo vệ. `.env.local` chứa cấu hình riêng nên không đưa vào ảnh hoặc Git.

## Khắc phục sự cố

- Khởi động lại Vite sau khi đổi giá trị .env.
- Không log hay commit token session đầy đủ.

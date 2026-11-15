---
title: "Cấu hình App Client và kiểm thử xác thực"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Cấu hình app client an toàn cho trình duyệt và xác minh luồng đăng ký/đăng nhập sau tích hợp.

## Các bước thực hiện

1. Tạo app client dành cho ứng dụng single-page trên trình duyệt. Không tạo client secret cho SPA public.
2. Ghi App Client ID và chỉ cấu hình callback/sign-out URL local/deployed thực tế nếu dùng managed login.
3. Tích hợp API xác thực Cognito/AWS Amplify JavaScript đã chọn: đăng ký, xác nhận, đăng nhập, lấy session và đăng xuất.
4. Kiểm thử bằng tài khoản riêng. Không để token trong browser log, ảnh chụp hay Git.

## Cấu hình đã triển khai và xác minh

Web client public đang triển khai có ID `epodlpjbopbdc809elojsad3a`; template đặt `GenerateSecret: false`. Frontend gọi Cognito User Pools API để đăng ký, xác nhận email, đăng nhập, làm mới session và đăng xuất. Đã dùng tài khoản owner và recipient thật trong luồng tài liệu. Dashboard sau đăng nhập và kết quả API có xác thực được ghi ở mục 5.8.2; không lưu ảnh riêng màn hình cấu hình App Client.

## Kết quả mong đợi

Frontend đã xác thực người dùng bằng Cognito thật. App client ID và User Pool ID được ghi ở mục 5.9.2; browser client không có client secret.

## Khắc phục sự cố

- SPA trên trình duyệt không được chứa client secret.
- Redirect URL phải khớp chính xác scheme, host, port và path.

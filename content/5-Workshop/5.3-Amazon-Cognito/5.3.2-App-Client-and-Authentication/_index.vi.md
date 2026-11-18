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

1. Mở User Pool do stack tạo → App integration → App clients, rồi chọn web client thuộc stack. Template đã tạo public SPA client với `GenerateSecret: false`; không tạo client secret.
2. Lấy Client ID từ CloudFormation Outputs. Nếu ứng dụng dùng managed login thì khai báo callback/sign-out URL chính xác; frontend hiện tại gọi Cognito User Pools API trực tiếp nên không dùng redirect callback.
3. Điền Pool ID, Client ID và Region vào `.env.local` theo mục 5.8.1. Service xác thực gọi Cognito cho đăng ký, xác nhận email, đăng nhập, làm mới session và đăng xuất.
4. Giữ access/ID token trong phiên ứng dụng, không ghi token đầy đủ vào log, ảnh hay Git.

## Cấu hình đã triển khai và xác minh

Web client public đang triển khai có ID `epodlpjbopbdc809elojsad3a`; template đặt `GenerateSecret: false`. Frontend gọi Cognito User Pools API để đăng ký, xác nhận email, đăng nhập, làm mới session và đăng xuất. Đã dùng tài khoản owner và recipient thật trong luồng tài liệu. Dashboard sau đăng nhập và kết quả API có xác thực được ghi ở mục 5.8.2; không lưu ảnh riêng màn hình cấu hình App Client.

## Kết quả mong đợi

Frontend đã xác thực người dùng bằng Cognito thật. App client ID và User Pool ID được ghi ở mục 5.9.2; browser client không có client secret.

## Khắc phục sự cố

- SPA trên trình duyệt không được chứa client secret.
- Redirect URL phải khớp chính xác scheme, host, port và path.

---
title: "Cấu hình App Client và kiểm thử xác thực"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Cấu hình app client an toàn cho trình duyệt và xác minh luồng đăng ký/đăng nhập sau tích hợp.

## Các bước thực hiện

1. Tạo app client dành cho ứng dụng single-page trên trình duyệt. Không tạo client secret cho SPA public.
2. Ghi App Client ID và chỉ cấu hình callback/sign-out URL local/deployed thực tế nếu dùng managed login.
3. Tích hợp API xác thực Cognito/AWS Amplify JavaScript đã chọn: đăng ký, xác nhận, đăng nhập, lấy session và đăng xuất.
4. Kiểm thử bằng tài khoản riêng. Không để token trong browser log, ảnh chụp hay Git.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.3.2 — Cấu hình App Client và kết quả đăng nhập thật, đã che thông tin nhạy cảm.]**

## Kết quả mong đợi

Frontend xác thực được người dùng thử nghiệm bằng Cognito thay vì mock auth.

## Khắc phục sự cố

- SPA trên trình duyệt không được chứa client secret.
- Redirect URL phải khớp chính xác scheme, host, port và path.

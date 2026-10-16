---
title: "Cấu hình Môi trường và Xác thực"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.8.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Cung cấp cấu hình công khai và kết nối frontend với xác thực Cognito thật.

## Các bước thực hiện

1. Cấu hình Region, User Pool ID, App Client ID và API base URL bằng đúng tên biến code sử dụng.
2. Biến Vite đưa ra trình duyệt là công khai; không đặt AWS secret key hay credentials trong `VITE_*`.
3. Thay hành vi đăng ký/đăng nhập/session/logout mock bằng Cognito SDK đã chọn và đính kèm token phù hợp vào request cần bảo vệ.
4. Khởi động lại Vite sau khi đổi environment và kiểm thử bằng tài khoản riêng.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.8.1 — Cấu hình môi trường đã làm sạch và kết quả đăng nhập Cognito thật.]**

## Kết quả mong đợi

Ứng dụng dùng session người dùng thật và gọi API base URL đã cấu hình.

## Khắc phục sự cố

- Khởi động lại Vite sau khi đổi giá trị .env.
- Không log hay commit token session đầy đủ.

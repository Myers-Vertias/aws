---
title: "Tạo Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Tạo thư mục người dùng dùng để xác thực tài khoản UniCloud DMS.

## Các bước thực hiện

1. Mở Amazon Cognito console và tạo User Pool tại Region đã chọn.
2. Chọn email làm định danh đăng nhập và cấu hình xác minh/khôi phục tài khoản cho lab.
3. Đặt tên development rõ ràng như `unicloud-dms-dev-users`; xem lại lựa chọn trước khi tạo pool.
4. Ghi Pool ID và Region vào ghi chú cục bộ. Không ghi mật khẩu hay token người dùng vào tài liệu.

## Cấu hình đã triển khai và xác minh

Stack `dms-dev` tạo User Pool `ap-southeast-1_hWIAdC6Xu` tại `ap-southeast-1`. SAM template cấu hình email để đăng nhập/xác minh, khôi phục tài khoản qua email đã xác minh và mật khẩu tối thiểu 12 ký tự gồm chữ hoa, chữ thường, số và ký hiệu. Output của pool/client được chụp ở mục 5.9.2. Đã đăng ký, xác nhận và đăng nhập Cognito thật qua ứng dụng; không lưu ảnh riêng màn hình cài đặt pool.

## Kết quả mong đợi

User Pool development đã triển khai tại Singapore và frontend đã dùng Cognito thật. Xem mục 5.3.2 và 5.8.2 về app client và kết quả đăng nhập.

## Khắc phục sự cố

- Nếu sai Region, sửa trước khi tạo resource phụ thuộc.
- Không đưa mã xác minh hoặc credentials vào minh chứng.

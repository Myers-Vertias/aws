---
title: "Tạo Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Tạo thư mục người dùng dùng để xác thực tài khoản UniCloud DMS.

## Các bước thực hiện

1. Mở Amazon Cognito console và tạo User Pool tại Region đã chọn.
2. Chọn email làm định danh đăng nhập và cấu hình xác minh/khôi phục tài khoản cho lab.
3. Đặt tên development rõ ràng như `unicloud-dms-dev-users`; xem lại lựa chọn trước khi tạo pool.
4. Ghi Pool ID và Region vào ghi chú cục bộ. Không ghi mật khẩu hay token người dùng vào tài liệu.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.3.1 — Cài đặt đăng nhập User Pool và thông tin pool đã tạo.]**

## Kết quả mong đợi

User Pool development tồn tại tại Region dự kiến.

## Khắc phục sự cố

- Nếu sai Region, sửa trước khi tạo resource phụ thuộc.
- Không đưa mã xác minh hoặc credentials vào minh chứng.

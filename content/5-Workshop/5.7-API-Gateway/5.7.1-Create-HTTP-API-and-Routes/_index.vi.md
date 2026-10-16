---
title: "Tạo HTTP API và Route"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Kết nối API Gateway route với Lambda integration tương ứng.

## Các bước thực hiện

1. Tạo HTTP API cùng Region với Lambda và Cognito.
2. Thêm integration/route khớp mục 5.2.2 và service layer. Ghi API invoke URL cùng stage.
3. Đảm bảo API Gateway có quyền gọi Lambda function đã chọn.
4. Giữ method, path parameter và response mong đợi khớp code frontend.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.7.1 — Danh sách API route và ánh xạ Lambda integration.]**

## Kết quả mong đợi

Route trỏ đúng handler và invoke URL được ghi nhận.

## Khắc phục sự cố

- Kiểm tra stage, method, route key và integration khi request trả 404/500.
- Không tạo route ngoài tài liệu mà frontend không dùng.

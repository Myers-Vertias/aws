---
title: "Tạo S3 bucket riêng tư"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.5.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Tạo bucket file với public access bị chặn và mã hóa được bật.

## Các bước thực hiện

1. Tạo bucket duy nhất toàn cầu như `unicloud-dms-dev-your-unique-suffix` tại Region đã chọn.
2. Giữ bật toàn bộ Block Public Access và mã hóa mặc định.
3. Không thêm public-read ACL hay bucket policy công khai. Giữ object tài liệu riêng tư.
4. Chỉ bật versioning nếu ứng dụng và quy trình cleanup đã tính đến version cũ.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.5.1 — Thuộc tính bucket hiển thị Region, Block Public Access và mã hóa.]**

## Kết quả mong đợi

S3 bucket sẵn sàng và vẫn riêng tư.

## Khắc phục sự cố

- Thêm suffix ngẫu nhiên nếu tên bucket đã được sử dụng.
- Không tắt chặn public access để né lỗi upload.

---
title: "Tạo bảng Documents và owner index"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.4.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Tạo bảng metadata chính theo thiết kế khóa đã duyệt.

## Các bước thực hiện

1. Tạo `UniCloudDMS-Documents-dev` tại Region đã chọn, dùng partition key String `documentId`.
2. Dùng on-demand cho lab nhỏ có tải biến động nếu thiết kế không quy định khác; giữ bật mã hóa.
3. Thêm GSI `ownerSub-updatedAt-index` với partition key `ownerSub`, sort key `updatedAt` nếu đây là cách truy vấn đã duyệt.
4. Chờ bảng và index chuyển sang Active trước khi test. Tránh scan toàn bảng khi liệt kê theo người dùng.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.4.1 — Key schema bảng Documents và owner index đã Active.]**

## Kết quả mong đợi

Bảng chính và index truy vấn theo owner sẵn sàng cho dữ liệu thử nghiệm.

## Khắc phục sự cố

- Tên và kiểu khóa DynamoDB phân biệt hoa/thường.
- Kiểm tra trạng thái index trước khi xử lý lỗi query.

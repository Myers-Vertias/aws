---
title: "Triển khai Handler Metadata Tài liệu"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.6.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Triển khai thao tác tạo/liệt kê/chi tiết/cập nhật bằng người gọi đã xác thực và mô hình dữ liệu đã duyệt.

## Các bước thực hiện

1. Triển khai Lambda handler khớp API contract và đóng gói dependency SDK cần thiết.
2. Đọc Cognito `sub` từ authorizer context đã xác thực. Không tin owner ID do trình duyệt gửi.
3. Validate tên file, category, tags, mô tả, metadata file và cấu trúc request ở server.
4. Query theo key/index đã thiết kế. Kiểm tra owner/share trước khi trả chi tiết hoặc cho phép sửa metadata.
5. Trả JSON error code và HTTP status nhất quán; không log token hay nội dung tài liệu.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.6.2 — Lambda test invocation với metadata tổng hợp và item DynamoDB tương ứng.]**

## Kết quả mong đợi

Handler metadata thao tác đúng bản ghi theo người dùng.

## Khắc phục sự cố

- Kiểm tra tên key phân biệt hoa/thường, cấu hình index và sub lấy từ token.
- Tránh scan toàn bảng cho truy vấn Tài liệu của tôi thông thường.

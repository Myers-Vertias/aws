---
title: "Xác định kiến trúc mục tiêu"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.2.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Mô tả luồng danh tính, API call, metadata và file trong ứng dụng.

## Các bước thực hiện

1. Dùng luồng mục tiêu: frontend React → Cognito → API Gateway → Lambda → DynamoDB và S3 riêng tư.
2. API authorizer xác thực token; Lambda kiểm tra quyền sở hữu hoặc share còn hiệu lực với thao tác tài liệu.
3. DynamoDB lưu metadata và quan hệ. S3 lưu bytes file. Trình duyệt truyền file bằng presigned URL ngắn hạn chỉ được cấp sau khi phân quyền.
4. Vẽ sơ đồ dịch vụ, chiều request, ranh giới tin cậy và nơi lưu từng loại dữ liệu. Cập nhật sơ đồ nếu implementation cuối khác.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.2.1 — Sơ đồ kiến trúc UniCloud DMS đã thống nhất, thể hiện dịch vụ và chiều request.]**

## Kết quả mong đợi

Có kiến trúc mục tiêu và sơ đồ luồng dữ liệu được xem xét trước triển khai.

## Khắc phục sự cố

- Không mô tả sơ đồ mục tiêu như kiến trúc đã triển khai nếu resource chưa được tạo và xác minh.
- Giữ sơ đồ khớp loại API và dịch vụ hosting thực tế được chọn.

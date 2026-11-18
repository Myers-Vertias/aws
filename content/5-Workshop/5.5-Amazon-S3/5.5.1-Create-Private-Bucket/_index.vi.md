---
title: "Tạo S3 bucket riêng tư"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.5.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Tạo bucket file với public access bị chặn và mã hóa được bật.

## Các bước thực hiện

1. Deploy SAM stack theo mục 5.1.3; template tạo Documents bucket và tự sinh tên duy nhất. Không tạo thêm bucket hoặc đổi sang tên trong ví dụ.
2. Mở CloudFormation → stack → Outputs, lấy `DocumentsBucketName`; từ Resources mở `DocumentsBucket` trong S3 Console.
3. Trong Permissions, xác nhận cả bốn Block Public Access controls đang bật và không có public bucket policy/ACL. Trong Properties, xác nhận default encryption SSE-S3 (AES-256).
4. Giữ bucket private. Chỉ bật versioning khi đã thiết kế vòng đời version và cách dọn toàn bộ object version/delete marker.

## Minh chứng triển khai

![Ảnh 5.5.1: S3 bucket bật Block all public access](/images/5-Workshop/5.5-Amazon-S3/5.5.1-s3-block-public-access.png)

![Ảnh 5.5.1: Bật mã hóa phía máy chủ mặc định bằng khóa S3 quản lý](/images/5-Workshop/5.5-Amazon-S3/5.5.1-s3-default-encryption.png)

Bucket đang triển khai dùng mã hóa phía máy chủ bằng khóa Amazon S3 quản lý (SSE-S3). Ảnh xác nhận Region Singapore, Block Public Access và mã hóa mặc định. Upload/download bằng presigned URL được ghi ở mục 5.8.2.

## Kết quả mong đợi

Documents bucket đã triển khai vẫn riêng tư, bật Block Public Access và SSE-S3.

## Khắc phục sự cố

- Thêm suffix ngẫu nhiên nếu tên bucket đã được sử dụng.
- Không tắt chặn public access để né lỗi upload.

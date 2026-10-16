---
title: "Tạo Execution Role theo đặc quyền tối thiểu"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.6.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Chỉ cấp cho Lambda quyền log, DynamoDB và S3 mà function thực sự cần.

## Các bước thực hiện

1. Tạo role được Lambda tin cậy và gắn policy ghi log CloudWatch cơ bản.
2. Thêm policy giới hạn vào ARN bảng/index và bucket/prefix cần cho từng function.
3. Tách role khi nhu cầu quyền khác nhau đáng kể. Xem lại từng action trước khi gắn policy.
4. Kiểm thử quyền qua invocation thật; không sửa AccessDenied bằng wildcard cho mọi tài nguyên.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.6.1 — Execution role với policy giới hạn theo tài nguyên.]**

## Kết quả mong đợi

Lambda role chỉ cấp các quyền cần thiết.

## Khắc phục sự cố

- Kiểm tra Region và ARN khi thao tác hợp lệ bị từ chối.
- Không dùng chung role workshop cho workload production không liên quan.

---
title: "Cấu hình Môi trường, Deploy và Xác minh"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.9.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Triển khai frontend và xác minh xác thực cùng API call thật.

## Các bước thực hiện

1. Cấu hình Region công khai, Cognito ID và API base URL. Không thêm secret access key vào biến frontend.
2. Cập nhật Cognito callback/sign-out URL và CORS S3/API với domain Amplify chính xác khi cần.
3. Chạy deployment và xem build log đến khi trạng thái thành công.
4. Mở URL HTTPS được cấp, kiểm thử đăng nhập và một thao tác tài liệu cần bảo vệ, sau đó refresh route lồng nhau.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.9.2 — Amplify deployment thành công và frontend chạy với API dự kiến.]**

## Kết quả mong đợi

Ứng dụng truy cập được qua HTTPS và hoàn thành luồng backend đã kiểm chứng.

## Khắc phục sự cố

- Build thành công không đảm bảo API kết nối; kiểm tra Network và CORS.
- Callback URL phải khớp chính xác origin triển khai.

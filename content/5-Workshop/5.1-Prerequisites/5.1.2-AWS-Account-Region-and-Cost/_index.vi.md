---
title: "Chuẩn bị tài khoản AWS và kiểm soát chi phí"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.1.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Chọn tài khoản development và thống nhất Region cùng quy ước đặt tên tài nguyên.

## Các bước thực hiện

1. Đăng nhập đúng tài khoản AWS. Không dùng root access key cho phát triển hằng ngày.
2. Chọn một Region cho Cognito, DynamoDB, Lambda, API Gateway và S3 rồi ghi vào ghi chú lab.
3. Kiểm tra giá và billing AWS hiện tại. Cân nhắc tạo cảnh báo ngân sách trước khi tạo resource; cảnh báo không tự động dừng chi tiêu.
4. Dùng tiền tố development như `unicloud-dms-dev`. Thêm hậu tố duy nhất cho tên toàn cầu như S3 bucket.
5. Dùng IAM identity theo đặc quyền tối thiểu. Không sửa AccessDenied bằng cách gắn quyền quản trị quá rộng.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.1.2 — Region AWS và thiết lập ngân sách/kiểm soát chi phí, đã che dữ liệu tài khoản nhạy cảm.]**

## Kết quả mong đợi

Region, tiền tố resource và phương án kiểm soát chi phí được ghi lại trước khi cấp phát.

## Khắc phục sự cố

- Điều kiện free tier tùy tài khoản và dịch vụ; kiểm tra bảng giá hiện hành, không mặc định resource miễn phí.
- Không chụp credentials, mật khẩu hoặc token.

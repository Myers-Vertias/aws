---
title: "Xóa Resource Workshop và Kiểm tra Chi phí"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Xóa an toàn resource tạo cho lab theo thứ tự phụ thuộc.

## Các bước thực hiện

1. Trước mỗi lần xóa, xác nhận AWS account, Region, tên resource chính xác và tiền tố development. Không xóa resource của project khác.
2. Sau khi lưu minh chứng, xóa Amplify app và API Gateway API của workshop nếu không còn cần, rồi xóa Lambda và log group riêng của workshop.
3. Làm trống S3 bucket; nếu bật versioning, xóa mọi object version và delete marker trước khi xóa bucket.
4. Chỉ xóa bảng DynamoDB và Cognito development User Pool sau khi test xong. Xóa IAM role chỉ dành cho workshop khi resource phụ thuộc đã bị xóa.
5. Kiểm tra Billing/Cost Explorer sau cleanup; dữ liệu chi phí có thể trễ và một số resource dùng chung có thể được giữ lại có chủ đích.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.12.2 — Danh sách resource cuối cùng và kiểm tra billing, đã che dữ liệu tài khoản không liên quan.]**

## Kết quả mong đợi

Resource lab không còn dùng được xóa an toàn và resource giữ lại được ghi rõ.

## Khắc phục sự cố

- Bucket bật versioning cần xóa tất cả version và delete marker trước khi xóa.
- Xóa Cognito User Pool có thể xóa vĩnh viễn tài khoản thử nghiệm. Xác minh đúng pool của workshop.

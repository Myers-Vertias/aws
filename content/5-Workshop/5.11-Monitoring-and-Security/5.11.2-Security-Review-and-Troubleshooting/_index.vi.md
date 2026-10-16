---
title: "Rà soát Bảo mật và Khắc phục Lỗi"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.11.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Theo trình tự chẩn đoán nhất quán và xác minh lưu trữ riêng tư cùng IAM đặc quyền tối thiểu.

## Các bước thực hiện

1. Kiểm tra Network panel, rồi API Gateway route/authorizer, Lambda log, key/index DynamoDB, CORS/signature S3 và issuer/audience Cognito.
2. Rà soát Block Public Access và bucket policy S3; xác nhận không object upload nào public.
3. Rà soát mỗi Lambda role để loại quyền wildcard thừa và xác nhận mọi document route đều kiểm tra owner/share.
4. Xem Billing/Cost Explorer và ghi lại resource được chủ động giữ sau workshop.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.11.2 — Rà soát bảo mật đã làm sạch và dấu vết từ request lỗi tới nguyên nhân.]**

## Kết quả mong đợi

Các kiểm tra bảo mật và minh chứng xử lý lỗi được ghi nhận trước cleanup.

## Khắc phục sự cố

- Không sửa lỗi truy cập bằng cách public S3 hoặc cấp AdministratorAccess.
- 401 thường liên quan xác thực, 403 liên quan phân quyền; hãy xác minh response thực tế.

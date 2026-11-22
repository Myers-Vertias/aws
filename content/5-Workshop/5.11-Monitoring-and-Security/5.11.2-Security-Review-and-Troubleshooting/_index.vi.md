---
title: "Rà soát Bảo mật và Khắc phục Lỗi"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.11.2 </b> "
---

{{% notice info %}}
Bằng chứng S3 Block Public Access và SSE-S3 được ghi ở mục 5.5.1. Ảnh quyền IAM ở mục 5.6.1 xác nhận role Lambda có policy ghi log cơ bản và policy runtime của dự án.
{{% /notice %}}

## Mục tiêu

Theo trình tự chẩn đoán nhất quán và xác minh lưu trữ riêng tư cùng IAM đặc quyền tối thiểu.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Bắt đầu từ CloudFormation stack để xác định resource và

Bắt đầu từ CloudFormation stack để xác định resource và Region; sau đó đối chiếu API route/authorizer, Lambda function, DynamoDB tables/indexes và Cognito Pool/Client với template.

### Bước 2: Trong S3 → Permissions, xem Block Public Access, bucket

Trong S3 → Permissions, xem Block Public Access, bucket policy và CORS; trong Properties xem SSE-S3. Bucket vẫn private, CORS không cấp quyền đọc object.

### Bước 3: Trong IAM role, so từng Action/Resource với

Trong IAM role, so từng Action/Resource với `backend/iam/lambda-runtime-policy.template.json`; kiểm tra role trust cho Lambda và PassRole được giới hạn. Nếu danh tính lab không có `iam:GetRole`, nhờ quản trị viên xem policy, không tăng quyền của chính user để vượt lỗi.

### Bước 4: Kiểm tra frontend chỉ chứa public VITE_*, API xác minh

Kiểm tra frontend chỉ chứa public `VITE_*`, API xác minh JWT, Lambda lấy caller `sub` từ claims; xem Billing/Budget để nhận biết resource còn hoạt động.

## Minh chứng

Ảnh role và các policy được gắn nằm ở mục 5.6.1. Các thao tác API có xác thực và truy cập tài liệu chưa chia sẻ được ghi ở mục 5.7.2 và 5.10.2.

## Kết quả mong đợi

Các kiểm tra bảo mật và minh chứng xử lý lỗi được ghi nhận trước cleanup.

## Khắc phục sự cố

- Không sửa lỗi truy cập bằng cách public S3 hoặc cấp AdministratorAccess.
- 401 thường liên quan xác thực, 403 liên quan phân quyền; hãy xác minh response thực tế.

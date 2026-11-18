---
title: "Rà soát Bảo mật và Khắc phục Lỗi"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.11.2 </b> "
---

{{% notice info %}}
Bằng chứng S3 Block Public Access và SSE-S3 được ghi ở mục 5.5.1. User `dms-dev` không thể xem Lambda execution role vì IAM từ chối `iam:GetRole`; chưa xác minh được policy có least-privilege hay không.
{{% /notice %}}

## Mục tiêu

Theo trình tự chẩn đoán nhất quán và xác minh lưu trữ riêng tư cùng IAM đặc quyền tối thiểu.

## Các bước thực hiện

1. Bắt đầu từ CloudFormation stack để xác định resource và Region; sau đó đối chiếu API route/authorizer, Lambda function, DynamoDB tables/indexes và Cognito Pool/Client với template.
2. Trong S3 → Permissions, xem Block Public Access, bucket policy và CORS; trong Properties xem SSE-S3. Bucket vẫn private, CORS không cấp quyền đọc object.
3. Trong IAM role, so từng Action/Resource với `backend/iam/lambda-runtime-policy.template.json`; kiểm tra role trust cho Lambda và PassRole được giới hạn. Nếu danh tính lab không có `iam:GetRole`, nhờ quản trị viên xem policy, không tăng quyền của chính user để vượt lỗi.
4. Kiểm tra frontend chỉ chứa public `VITE_*`, API xác minh JWT, Lambda lấy caller `sub` từ claims; xem Billing/Budget để nhận biết resource còn hoạt động.

## Vị trí ảnh chụp màn hình

![Ảnh 5.11.2: IAM từ chối dms-dev quyền xem Lambda execution role](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.2-iam-role-review-access-denied.png)

Không có quyền IAM nào bị thay đổi. Nội dung policy của role và phạm vi least-privilege chưa thể xác minh bằng phiên đăng nhập này.

## Kết quả mong đợi

Các kiểm tra bảo mật và minh chứng xử lý lỗi được ghi nhận trước cleanup.

## Khắc phục sự cố

- Không sửa lỗi truy cập bằng cách public S3 hoặc cấp AdministratorAccess.
- 401 thường liên quan xác thực, 403 liên quan phân quyền; hãy xác minh response thực tế.

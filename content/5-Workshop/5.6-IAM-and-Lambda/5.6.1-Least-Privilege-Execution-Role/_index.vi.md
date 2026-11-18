---
title: "Tạo Execution Role theo đặc quyền tối thiểu"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.6.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Tạo execution role riêng cho Lambda và cấp quyền theo nhu cầu thực tế của function.

## Các bước thực hiện

1. Tạo execution role theo mục 5.1.3 với trusted entity Lambda và policy `AWSLambdaBasicExecutionRole` để ghi CloudWatch Logs. Lấy ARN vì SAM yêu cầu parameter `LambdaExecutionRoleArn`.
2. Deploy stack trước để sinh tên bảng, bucket và User Pool. Sau đó thay placeholder trong `backend/iam/lambda-runtime-policy.template.json` bằng đúng ARN/tên resource lấy từ Outputs và gắn policy runtime vào role.
3. Mở IAM → Roles → role của Lambda → Permissions để xem policy. Giữ quyền ở đúng ba DynamoDB table/index, prefix tài liệu trong S3 và User Pool của project.
4. Danh tính triển khai cần `iam:PassRole` giới hạn cho role này và Lambda. Không gắn quyền IAM rộng vào Lambda role.

## Vị trí ảnh chụp màn hình

![Ảnh 5.6.1: Lambda role có policy ghi log cơ bản](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-basic-execution-policy.png)

![Ảnh 5.6.1: DmsDocumentRuntimeAccess đã gắn vào Lambda role](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-runtime-policy-attached.png)

## Kết quả mong đợi

Ảnh trang quyền role hiển thị `AWSLambdaBasicExecutionRole` và `DmsDocumentRuntimeAccess`. File policy trong repo giới hạn theo resource. Các document operation có bảo vệ đã được thao tác sau đó; nội dung policy live vẫn chưa xác minh được do IAM từ chối `iam:GetRole`.

## Khắc phục sự cố

- Kiểm tra Region và ARN khi thao tác hợp lệ bị từ chối.
- Không dùng chung role workshop cho workload production không liên quan.

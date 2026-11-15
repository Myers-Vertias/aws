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

1. Trong IAM, tạo role `dms-lambda-execution` với trusted entity là Lambda.
2. Gắn policy AWS managed `AWSLambdaBasicExecutionRole` để function ghi log vào CloudWatch Logs.
3. Ảnh trang quyền của role ghi nhận inline policy `DmsDocumentRuntimeAccess`. File policy trong `backend/iam/lambda-runtime-policy.ap-southeast-1.json` giới hạn DynamoDB tables/indexes, prefix object tài liệu trong S3 và thao tác tra cứu người nhận Cognito. Sau đó IAM user `dms-dev` bị từ chối `iam:GetRole`, nên chưa thể mở lại độc lập nội dung policy live trong lần rà soát này.
4. Group triển khai có inline policy `DmsPassLambdaExecutionRole`, chỉ cho phép chuyển role này cho Lambda; không cấp quyền `iam:PassRole` trên mọi role.

## Vị trí ảnh chụp màn hình

![Ảnh 5.6.1: Lambda role có policy ghi log cơ bản](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-basic-execution-policy.png)

![Ảnh 5.6.1: DmsDocumentRuntimeAccess đã gắn vào Lambda role](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-runtime-policy-attached.png)

## Kết quả mong đợi

Ảnh trang quyền role hiển thị `AWSLambdaBasicExecutionRole` và `DmsDocumentRuntimeAccess`. File policy trong repo giới hạn theo resource. Các document operation có bảo vệ đã được thao tác sau đó; nội dung policy live vẫn chưa xác minh được do IAM từ chối `iam:GetRole`.

## Khắc phục sự cố

- Kiểm tra Region và ARN khi thao tác hợp lệ bị từ chối.
- Không dùng chung role workshop cho workload production không liên quan.

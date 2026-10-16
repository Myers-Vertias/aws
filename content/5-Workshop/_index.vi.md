---
title: "Workshop"
date: 2026-10-16
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop: Xây dựng UniCloud DMS trên AWS

Workshop này hướng dẫn xây dựng UniCloud DMS từ frontend React/Vite hiện có đến ứng dụng quản lý tài liệu được backend AWS hỗ trợ.

{{% notice warning %}}
**Trạng thái:** Đây là khung hướng dẫn chuẩn bị trước khi triển khai backend. Các vị trí ảnh là placeholder; chỉ thay bằng ảnh thật sau khi thực hiện và xác minh bước tương ứng. Không coi kết quả mong đợi là bằng chứng triển khai.
{{% /notice %}}

## Nội dung Workshop

- [5.1 Điều kiện tiên quyết](5.1-Prerequisites/)
- [5.2 Thiết kế Backend](5.2-Backend-Design/)
- [5.3 Amazon Cognito](5.3-Amazon-Cognito/)
- [5.4 Amazon DynamoDB](5.4-Amazon-DynamoDB/)
- [5.5 Amazon S3](5.5-Amazon-S3/)
- [5.6 IAM và AWS Lambda](5.6-IAM-and-Lambda/)
- [5.7 Amazon API Gateway](5.7-API-Gateway/)
- [5.8 Tích hợp Frontend](5.8-Frontend-Integration/)
- [5.9 AWS Amplify Hosting](5.9-Amplify-Hosting/)
- [5.10 Kiểm thử End-to-End](5.10-End-to-End-Testing/)
- [5.11 Giám sát và Bảo mật](5.11-Monitoring-and-Security/)
- [5.12 Dọn dẹp](5.12-Cleanup/)

## Nguyên tắc chung

- Dùng cùng AWS Region trừ khi có lý do rõ ràng khác.
- Giữ S3 riêng tư; kiểm tra phân quyền backend trước khi cấp URL file.
- Không đưa credentials, JWT đầy đủ hoặc presigned URL vào ảnh/Git.
- Chỉ ghi test đạt sau khi đã chạy thực tế và dọn dẹp resource sau khi lưu minh chứng.

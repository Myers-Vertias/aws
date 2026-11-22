---
title: "Workshop"
date: 2026-10-16
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop: Xây dựng UniCloud DMS trên AWS

Workshop này hướng dẫn xây dựng UniCloud DMS từ frontend React/Vite hiện có đến ứng dụng quản lý tài liệu được backend AWS hỗ trợ.

## Cách làm theo Workshop

Nếu lần đầu cài đặt, hãy đọc theo thứ tự dưới đây. Các mục 5.3–5.7 giải thích và giúp kiểm tra dịch vụ; **không cần tạo thủ công từng dịch vụ trong Console**, vì SAM tạo các tài nguyên được mô tả trong mục 5.1.3.

1. Hoàn thành [5.1 Điều kiện tiên quyết](5.1-Prerequisites/) để cài công cụ và chuẩn bị tài khoản AWS.
2. Đọc [5.2 Thiết kế Backend](5.2-Backend-Design/) để hiểu luồng dữ liệu và mô hình trước khi deploy.
3. Làm theo [5.1.3 Quy trình triển khai Backend bằng AWS SAM](5.1-Prerequisites/5.1.3-Backend-Deployment-Runbook/) để tạo backend và ghi lại các Outputs.
4. Dùng các mục 5.3–5.7 để đối chiếu Cognito, DynamoDB, S3, Lambda và API Gateway với stack đã tạo.
5. Làm theo [5.8 Tích hợp Frontend](5.8-Frontend-Integration/) và [5.9 AWS Amplify Hosting](5.9-Amplify-Hosting/), rồi chạy kiểm tra ở mục 5.10.
6. Chỉ làm [5.12 Dọn dẹp](5.12-Cleanup/) sau khi đã lưu minh chứng và không còn cần ứng dụng hoạt động.

Các ảnh ở phần 5 ghi nhận cấu hình của lần triển khai đã hoàn tất trước đây. Tài nguyên AWS của dự án được dọn ngày 19/11/2026, nên người làm theo cần deploy stack mới bằng account/Region của mình và không dùng lại ID/URL trong ảnh.

{{% notice warning %}}
**Trạng thái triển khai (15 tháng 11 năm 2026):** Ứng dụng `dms-dev` đã triển khai tại `ap-southeast-1`; frontend React đang được phục vụ qua Amplify. Đã quan sát đăng nhập Cognito thật, gọi API có xác thực, upload/download S3, lưu metadata, Favorites, Thùng rác/khôi phục và chia sẻ giữa hai tài khoản.
{{% /notice %}}

## Đã triển khai và xác minh

- Tạo Cognito User Pool và public web app client; đăng ký và đăng nhập bằng tài khoản Cognito thật.
- Triển khai HTTP API, Lambda, S3 riêng tư và ba bảng DynamoDB. Bảng cùng access index được ghi ở mục 5.4.1 và 5.4.2.
- Xác minh request lấy danh sách tài liệu đã đăng nhập (`200`), request chưa xác thực (`401`) và tài khoản thứ hai đã đăng nhập gọi tài liệu chưa chia sẻ (`404`).
- Xác minh upload, lưu metadata, download, Favorites, Thùng rác/khôi phục, chia sẻ, tài khoản nhận xem/tải tài liệu, lọc danh mục, tìm theo thẻ và giao diện tiếng Việt/Anh. Minh chứng nằm ở các mục 5.7.2, 5.8.2 và 5.10.
- Cấu hình S3 riêng tư, mã hóa mặc định SSE-S3 và CORS cho origin local cùng Amplify.

## Ghi chú trạng thái hiện tại

- Form upload local và API upload đã deploy đều từ chối file vượt giới hạn 20 MiB; ảnh request API `20 MiB + 1 byte` trả HTTP `400` nằm ở mục 5.10.1.
- Ảnh quyền IAM xác nhận role Lambda có `AWSLambdaBasicExecutionRole` và `DmsDocumentRuntimeAccess`. Policy runtime trong source giới hạn theo resource; các thao tác tài liệu có kiểm tra quyền đã được thực hiện.
- Đã thêm sơ đồ kiến trúc của hệ thống đã deploy và phần thiết kế data model. Billing được chụp ngày 18/11/2026; Bills tháng 11 khi đó vẫn Pending nên chưa phải hóa đơn cuối cùng.
- Tài nguyên AWS của dự án đã được cleanup ngày 19/11/2026. Ảnh thao tác và trạng thái sau khi xóa vừa hướng dẫn từng bước, vừa là minh chứng cho lần cleanup này; xem mục 5.12.1 và 5.12.2.

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

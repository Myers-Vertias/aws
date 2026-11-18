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
**Trạng thái triển khai (15 tháng 11 năm 2026):** Ứng dụng `dms-dev` đã triển khai tại `ap-southeast-1`; frontend React đang được phục vụ qua Amplify. Đã quan sát đăng nhập Cognito thật, gọi API có xác thực, upload/download S3, lưu metadata, Favorites, Thùng rác/khôi phục và chia sẻ giữa hai tài khoản.
{{% /notice %}}

## Đã triển khai và xác minh

- Tạo Cognito User Pool và public web app client; đăng ký và đăng nhập bằng tài khoản Cognito thật.
- Triển khai HTTP API, Lambda, S3 riêng tư và ba bảng DynamoDB. Bảng cùng access index được ghi ở mục 5.4.1 và 5.4.2.
- Xác minh request lấy danh sách tài liệu đã đăng nhập (`200`), request chưa xác thực (`401`) và tài khoản thứ hai đã đăng nhập gọi tài liệu chưa chia sẻ (`404`).
- Xác minh upload, lưu metadata, download, Favorites, Thùng rác/khôi phục, chia sẻ, tài khoản nhận xem/tải tài liệu, lọc danh mục, tìm theo thẻ và giao diện tiếng Việt/Anh. Minh chứng nằm ở các mục 5.7.2, 5.8.2 và 5.10.
- Cấu hình S3 riêng tư, mã hóa mặc định SSE-S3 và CORS cho origin local cùng Amplify.

## Ghi chú trạng thái hiện tại

- Form upload local và API upload đã deploy đều từ chối file vượt giới hạn 20 MiB. Các luồng ứng dụng đã xác minh được liệt kê ở trên và trong mục 5.12.1.
- CloudWatch chưa có log ghép từng request với route/status. IAM user `dms-dev` bị từ chối `iam:GetRole`, nên chưa thể kiểm tra độc lập nội dung policy hiện hành của execution role.
- Đã thêm sơ đồ kiến trúc của hệ thống đã deploy. Sơ đồ data model riêng vẫn còn thiếu nếu rubric yêu cầu. Đã chụp kết quả kiểm tra billing bước đầu ngày 18/11/2026 nhưng Bills tháng 11 vẫn Pending; tài nguyên AWS của workshop chưa được cleanup. Bảng tổng kết minh chứng nằm ở mục 5.12.1.

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

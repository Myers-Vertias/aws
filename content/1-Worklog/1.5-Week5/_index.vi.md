---
title: "Worklog Tuần 5"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5

* Tìm hiểu các dịch vụ AWS còn lại trong kiến trúc dự án quản lý tài liệu.
* Nắm được vai trò của AWS Lambda, Amazon S3, Amazon DynamoDB, IAM và Amazon CloudWatch.
* Hiểu sơ bộ cách các dịch vụ này phối hợp trong một ứng dụng serverless.

### Các công việc đã thực hiện trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ 6 | **Tìm hiểu các dịch vụ backend và lưu trữ cho project**<br>• Tìm hiểu AWS Lambda và cách function xử lý tác vụ mà không cần tự quản lý máy chủ. Tham khảo cách dùng Lambda để tự động bật/tắt EC2 nhằm tối ưu chi phí.<br>• Tìm hiểu Amazon S3, các khái niệm bucket và object, cách lưu trữ file và kiểm soát quyền truy cập.<br>• Tìm hiểu Amazon DynamoDB trong vai trò cơ sở dữ liệu NoSQL để lưu metadata của tài liệu.<br>• Tìm hiểu IAM role và quyền mà Lambda cần để truy cập các dịch vụ AWS theo nguyên tắc tối thiểu cần thiết.<br>• Tìm hiểu Amazon CloudWatch Logs, metrics và alarms để theo dõi hoạt động, phát hiện lỗi của ứng dụng.<br>• Tổng hợp luồng phối hợp dự kiến: API Gateway gọi Lambda; Lambda làm việc với DynamoDB và S3; CloudWatch tiếp nhận log và metrics. | 16/10/2026 | 16/10/2026 | [Serverless Journey](https://000078.awsfcaj.com/vi/); [Tối ưu chi phí EC2 với Lambda](https://000022.awsfcaj.com/vi/); [CloudWatch Workshop](https://000008.awsfcaj.com/vi/); [Amazon S3](https://000057.awsfcaj.com/vi/) |

### Kết quả đạt được tuần 5

* Hiểu vai trò của AWS Lambda trong việc chạy logic backend theo hướng serverless.
* Nắm được Amazon S3 dùng để lưu trữ file và Amazon DynamoDB dùng để lưu metadata trong kiến trúc dự án.
* Hiểu IAM role cấp quyền cho Lambda truy cập các tài nguyên AWS cần thiết.
* Biết Amazon CloudWatch hỗ trợ xem log, theo dõi metrics và thiết lập cảnh báo.
* Hình dung được cách các dịch vụ trên phối hợp với Cognito, Amplify và API Gateway đã tìm hiểu ở tuần 4 để tạo thành kiến trúc tổng thể.



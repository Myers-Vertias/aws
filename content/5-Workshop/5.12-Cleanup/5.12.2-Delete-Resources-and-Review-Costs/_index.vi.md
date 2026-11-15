---
title: "Xóa Resource Workshop và Kiểm tra Chi phí"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Xóa an toàn resource tạo cho lab theo thứ tự phụ thuộc.

## Trạng thái hiện tại

Chưa thực hiện cleanup. Amplify app, API Gateway API, Lambda function, CloudWatch log group, S3 bucket, DynamoDB table, Cognito development pool và role liên quan vẫn được giữ để ứng dụng tiếp tục chạy. Chưa ghi nhận bước kiểm tra Billing/Cost Explorer sau deploy. Budget 10 USD chỉ gửi cảnh báo, không giới hạn chi tiêu. Báo cáo này không khẳng định đã xóa resource nào.

## Các bước thực hiện

1. Trước mỗi lần xóa, xác nhận AWS account, Region, tên resource chính xác và tiền tố development. Không xóa resource của project khác.
2. Sau khi lưu minh chứng, xóa Amplify app và API Gateway API của workshop nếu không còn cần, rồi xóa Lambda và log group riêng của workshop.
3. Làm trống S3 bucket; nếu bật versioning, xóa mọi object version và delete marker trước khi xóa bucket.
4. Chỉ xóa bảng DynamoDB và Cognito development User Pool sau khi test xong. Xóa IAM role chỉ dành cho workshop khi resource phụ thuộc đã bị xóa.
5. Kiểm tra Billing/Cost Explorer sau cleanup; dữ liệu chi phí có thể trễ và một số resource dùng chung có thể được giữ lại có chủ đích.

## Trạng thái minh chứng

Không có ảnh xóa resource hoặc kiểm tra billing cuối cùng vì hai việc này chưa được thực hiện. Trước khi xóa trong tương lai, cần kiểm tra lại account và region, đồng thời giữ nguyên resource của project khi ứng dụng còn được sử dụng.

## Kết quả mong đợi

Nếu sau này được cho phép và thực hiện cleanup, hãy ghi lại resource đã xóa/giữ lại và kiểm tra dữ liệu chi phí sau độ trễ. Đây là việc cần làm trong tương lai, không phải kết quả của lần workshop này.

## Khắc phục sự cố

- Bucket bật versioning cần xóa tất cả version và delete marker trước khi xóa.
- Xóa Cognito User Pool có thể xóa vĩnh viễn tài khoản thử nghiệm. Xác minh đúng pool của workshop.

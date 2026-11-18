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

Chưa thực hiện cleanup vì các resource của ứng dụng vẫn được giữ lại. Đã kiểm tra billing bước đầu ngày 18/11/2026: Cost Explorer cho khoảng 01–18/11 hiển thị `-$0.00` và không có cột chi phí nhìn thấy được; trang Bills tháng 11 hiển thị `USD 0.00`, trạng thái `Pending`. Đây là ảnh chụp dữ liệu tạm thời, chưa phải hóa đơn cuối cùng. Amplify app, API Gateway API, Lambda function, CloudWatch log group, S3 bucket, DynamoDB table, Cognito development pool và role liên quan vẫn còn để project tiếp tục hoạt động. Budget 10 USD chỉ gửi cảnh báo, không giới hạn chi tiêu. Báo cáo này không khẳng định đã xóa resource nào.

## Kiểm kê CloudFormation chỉ đọc

Ngày 18/11/2026, stack `dms-dev` tại `ap-southeast-1` hiển thị trạng thái `UPDATE_COMPLETE` và 13 resource. Các resource ứng dụng gồm `DmsUserPool`, `DmsUserPoolClient`, `DocumentsApi` và default stage, `DocumentsFunction` cùng các quyền API/CORS, `HealthFunction` cùng quyền API, `DocumentsTable`, `SharesTable`, `FavoritesTable` và `DocumentsBucket`. Đây là resource của project và vẫn đang được sử dụng. Stack riêng `aws-sam-cli-managed-default` không thuộc stack ứng dụng `dms-dev` nên không nằm trong inventory project này.

![Danh sách resource CloudFormation của stack dms-dev, phần trên; đã che thông tin tài khoản](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-top.png)

![Danh sách resource CloudFormation của stack dms-dev, phần dưới; đã che thông tin tài khoản](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-bottom.png)

## Các bước thực hiện

1. Xác nhận account, Region, stack name và ứng dụng không còn cần hoạt động; lưu outputs/dữ liệu/minh chứng cần giữ. Không bắt đầu cleanup nếu app vẫn đang được dùng.
2. Amplify app và SAM artifact bucket độc lập với stack ứng dụng. Xóa Amplify app/branch trong Amplify Console nếu không cần giữ hosting.
3. Trước khi xóa CloudFormation stack, mở S3 bucket tài liệu và xóa object; nếu versioning bật, xóa cả versions và delete markers. Không xóa nhầm bucket artifact SAM.
4. Mở CloudFormation → Stacks → chọn đúng stack lab → Delete. Theo dõi Events đến `DELETE_COMPLETE`; stack quản lý Cognito, API, Lambda, DynamoDB và bucket.
5. Sau đó kiểm tra IAM role, CloudWatch log groups, Amplify và SAM artifact bucket còn tồn tại độc lập; chỉ xóa resource riêng của lab. Xem lại Budget và Billing/Cost Explorer sau độ trễ dữ liệu.

Cleanup này chưa được thực hiện cho môi trường `dms-dev` vì ứng dụng vẫn được giữ lại. Đây là quy trình hướng dẫn, không phải xác nhận đã xóa tài nguyên.

## Trạng thái minh chứng

Ảnh kiểm tra billing bước đầu được lưu ở mục 5.12.1. Chưa có ảnh xóa resource vì cleanup chưa được thực hiện. Trước khi xóa trong tương lai, cần kiểm tra lại account và Region, đồng thời giữ nguyên resource của project khi ứng dụng còn được sử dụng.

## Kết quả mong đợi

Nếu sau này được cho phép và thực hiện cleanup, hãy ghi lại resource đã xóa/giữ lại và kiểm tra dữ liệu chi phí sau độ trễ. Đây là việc cần làm trong tương lai, không phải kết quả của lần workshop này.

## Khắc phục sự cố

- Bucket bật versioning cần xóa tất cả version và delete marker trước khi xóa.
- Xóa Cognito User Pool có thể xóa vĩnh viễn tài khoản thử nghiệm. Xác minh đúng pool của workshop.

---
title: "Xóa Resource Workshop và Kiểm tra Chi phí"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
Trang này hướng dẫn cách dọn dẹp tài nguyên AWS sau khi hoàn tất demo. Ảnh giao diện minh họa vị trí thao tác và thông báo có thể xuất hiện; chúng không phải xác nhận rằng một môi trường AWS bất kỳ đã được cleanup.
{{% /notice %}}

## Mục tiêu

Xóa an toàn resource tạo cho lab theo thứ tự phụ thuộc.

## Phạm vi dọn dẹp

Quy trình dưới đây dùng các tên tài nguyên của môi trường workshop làm ví dụ. Trước khi làm theo, hãy xác nhận đúng AWS account, Region và tên tài nguyên của chính mình. Xóa CloudFormation stack chỉ áp dụng cho tài nguyên do stack quản lý. Amplify, CloudWatch alarm/log group, SNS, IAM, AWS Budget và SAM artifact có thể được tạo riêng, nên cần xử lý và kiểm tra theo từng dịch vụ. Không xem thông báo thành công trên một bước riêng lẻ là xác nhận rằng mọi tài nguyên đã được dọn.

Đã kiểm tra billing bước đầu ngày 18/11/2026: Cost Explorer cho khoảng 01–18/11 hiển thị `-$0.00` và không có cột chi phí nhìn thấy được; trang Bills tháng 11 hiển thị `USD 0.00`, trạng thái `Pending`. Đây là ảnh chụp dữ liệu tạm thời, chưa phải hóa đơn cuối cùng. Budget 10 USD chỉ gửi cảnh báo, không giới hạn chi tiêu.

## Tên tài nguyên trong ví dụ

Môi trường ví dụ có stack `dms-dev` với các logical ID như `DmsUserPool`, `DmsUserPoolClient`, `DocumentsApi`, `DocumentsFunction`, `HealthFunction`, các bảng DynamoDB và `DocumentsBucket`. Các ảnh dưới đây là tài liệu tham khảo để nhận diện tên resource trong console; khi làm theo, hãy đối chiếu với danh sách resource thực tế của stack.

![Danh sách resource CloudFormation của stack dms-dev, phần trên; đã che thông tin tài khoản](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-top.png)

![Danh sách resource CloudFormation của stack dms-dev, phần dưới; đã che thông tin tài khoản](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-bottom.png)

## Quy trình dọn dẹp tài nguyên

1. Chỉ thực hiện sau khi đã quay demo và không còn cần ứng dụng hoạt động. Lưu mã nguồn, báo cáo và dữ liệu cần giữ ở máy cá nhân. Xóa stack sẽ làm backend ngừng hoạt động và có thể xóa vĩnh viễn dữ liệu ứng dụng.
2. Mở **CloudFormation → Stacks**, xác nhận đúng AWS account, Region `ap-southeast-1`, rồi chọn stack `dms-dev` và bấm **Delete**. Ở hộp thoại xác nhận, kiểm tra tên stack và tác động trước khi xác nhận.

   ![Hộp thoại xác nhận xóa stack dms-dev trong CloudFormation](/images/5-Workshop/5.12-Cleanup/5.12.2-delete-stack-confirmation.png)

3. Nếu Events báo `DELETE_FAILED` do `DocumentsBucket`, mở bucket tài liệu được liệt kê ở tab **Resources** của stack. Từ trang bucket, chọn thao tác **Empty** để xóa toàn bộ nội dung. Nếu bucket bật versioning, phải xóa cả object versions và delete markers. Không nhầm bucket tài liệu với bucket artifact SAM.

   ![Hộp thoại xác nhận xóa vĩnh viễn object trong prefix users của bucket tài liệu](/images/5-Workshop/5.12-Cleanup/5.12.2-empty-s3-confirmation.png)

4. Chờ thao tác S3 hoàn tất và xác nhận không còn object. Quay lại CloudFormation, chọn **Retry delete** cho `dms-dev`. Nếu bucket đã trống, để tùy chọn retain resource không chọn bucket. Dùng **Force delete this entire stack** chỉ để kết thúc stack đang ở `DELETE_FAILED`; AWS có thể giữ lại tài nguyên vẫn không xóa được, vì vậy phải kiểm tra các tài nguyên còn sót sau đó.
5. Chờ CloudFormation làm mới danh sách và xác nhận `dms-dev` không còn trong các stack đang hoạt động. Kiểm tra Events/Deleted stacks nếu cần xác nhận trạng thái cuối cùng.

   ![Danh sách stack đang hoạt động sau thao tác xóa dms-dev](/images/5-Workshop/5.12-Cleanup/5.12.2-stack-list-after-delete.png)

6. Để xóa frontend được host độc lập, mở **AWS Amplify → All apps**, chọn đúng app `serverless-document-management`.

   ![Danh sách AWS Amplify có app serverless-document-management cần xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-all-apps.png)

7. Mở **App settings → General settings**, kiểm tra tên app rồi chọn **Delete app**. Trong hộp thoại xác nhận, nhập `delete` và xác nhận. Đây là thao tác không thể khôi phục.

   ![Trang General settings của app Amplify với nút Delete app](/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-general-settings.png)

8. Chờ thông báo xóa thành công. AWS có thể báo tài nguyên backend liên kết với các branch đã được đưa vào hàng đợi xóa; kiểm tra lại app không còn trong danh sách Amplify.

   ![Thông báo Amplify app đã xóa và tài nguyên backend các branch được đưa vào hàng đợi xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-deleted.png)

9. Để xóa alarm tạo riêng, mở **CloudWatch → Alarms**, chọn đúng `dms-dev-documents-errors`, rồi chọn **Actions → Delete** và xác nhận.

   ![Chọn CloudWatch alarm dms-dev-documents-errors và mở Actions Delete](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-alarm-delete-menu.png)

   ![Ví dụ thông báo CloudWatch hiển thị sau thao tác xóa alarm](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-alarm-deleted.png)

10. Để xóa SNS topic được tạo cho cảnh báo, mở **Amazon SNS → Topics**, chọn đúng `dms-dev-lambda-errors`, rồi chọn **Delete**. Trong hộp thoại xác nhận, nhập `delete me` và xác nhận. Topic không thể khôi phục; các subscription gắn với topic cũng bị xóa.

   ![Chọn SNS topic dms-dev-lambda-errors để xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-sns-topic-delete-selection.png)

   ![Ví dụ thông báo Amazon SNS hiển thị sau thao tác xóa topic](/images/5-Workshop/5.12-Cleanup/5.12.2-sns-topic-deleted.png)

11. Xóa các log group do ứng dụng tạo riêng: mở **CloudWatch → Logs → Log Management → Log groups**, tìm và chọn đúng `/aws/apigateway/dms-dev-access`, `/aws/lambda/dms-dev-documents` và `/aws/lambda/dms-dev-health`. Chọn **Actions → Delete log group(s)**, kiểm tra lại danh sách rồi xác nhận. Không chọn các log group không thuộc ứng dụng, chẳng hạn `dc-common-lambda-auto-start` và `dc-common-lambda-auto-stop` trong ảnh ví dụ.

   ![Minh họa chọn ba log group của ứng dụng và mở menu xóa trong CloudWatch](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-log-groups-delete-selection.png)

   Ảnh trên minh họa các lựa chọn trong giao diện; hãy đối chiếu tên log group trong account của mình trước khi xác nhận.

   ![Ví dụ giao diện CloudWatch sau thao tác xóa, còn lại hai log group dc-common không được chọn](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-log-groups-deleted-example.png)

   Ảnh trên chỉ minh họa thông báo và danh sách sau thao tác, không xác nhận trạng thái cleanup của account người đọc.

12. Sau khi xác nhận các Lambda function của workshop đã được xóa, mở **IAM → Roles**, tìm và chọn đúng role `dms-lambda-execution`, rồi chọn **Delete**. Kiểm tra tên role trong hộp thoại trước khi xác nhận. Role IAM là tài nguyên dùng chung cấp account; không xóa các role hệ thống hoặc role khác.

   ![Minh họa chọn IAM role dms-lambda-execution để xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-iam-role-delete-selection.png)

   ![Ví dụ thông báo IAM sau khi role dms-lambda-execution được xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-iam-role-deleted-example.png)

   Hai ảnh trên minh họa lựa chọn và thông báo có thể xuất hiện trong console; chúng không xác nhận trạng thái IAM của account người đọc.

13. Để xóa bucket artifact SAM, đối chiếu Physical ID `aws-sam-cli-managed-default-samclisourcebucket-...` với resource `SamCliSourceBucket` trong stack `aws-sam-cli-managed-default`. Sau khi đã Empty bucket, mở **Amazon S3 → Buckets**, chọn đúng bucket này, bấm **Delete**, nhập chính xác tên bucket rồi xác nhận. Tên bucket có hậu tố khác nhau theo account, nên luôn đối chiếu Physical ID thực tế.

   ![Minh họa trang xác nhận xóa bucket artifact SAM sau khi đã nhập tên bucket](/images/5-Workshop/5.12-Cleanup/5.12.2-sam-artifact-bucket-delete-confirmation.png)

   ![Ví dụ giao diện S3 sau thao tác xóa bucket artifact SAM](/images/5-Workshop/5.12-Cleanup/5.12.2-sam-artifact-bucket-deleted-example.png)

   Ảnh chỉ minh họa hộp thoại và thông báo có thể gặp, không xác nhận bucket trong account người đọc đã bị xóa.

14. Quay lại **CloudFormation → Stacks**, chọn stack `aws-sam-cli-managed-default` rồi bấm **Delete stack**. Nhập chính xác tên stack trong hộp thoại xác nhận và bấm **Delete stack** lần nữa.

   ![Minh họa hộp thoại xác nhận xóa stack aws-sam-cli-managed-default](/images/5-Workshop/5.12-Cleanup/5.12.2-sam-stack-delete-confirmation.png)

   Chờ CloudFormation xử lý và kiểm tra **Events** hoặc trạng thái stack. Khi trạng thái là `DELETE_COMPLETE`, stack đã xóa xong. Nếu có lỗi, đọc Events để xác định resource còn lại trước khi retry.

   ![Ví dụ trạng thái DELETE_COMPLETE của stack SAM sau thao tác xóa](/images/5-Workshop/5.12-Cleanup/5.12.2-sam-stack-delete-complete-example.png)

   Ảnh trên minh họa hộp thoại và trạng thái có thể gặp; chúng không xác nhận stack trong account người đọc đã bị xóa.

15. Để xóa budget tạo cho workshop, mở **Billing and Cost Management → Budgets**, chọn `My Monthly Cost Budget` có mức `$10.00`, rồi chọn **Actions → Delete** và xác nhận. Không chọn `My-200$-budget` trong ví dụ nếu chưa xác định budget đó thuộc workshop hay không. Budget chỉ gửi cảnh báo, không giới hạn chi tiêu.

   ![Minh họa chọn My Monthly Cost Budget và mở Actions Delete](/images/5-Workshop/5.12-Cleanup/5.12.2-budget-delete-selection.png)

   ![Ví dụ danh sách Budgets sau thao tác, còn My-200$-budget](/images/5-Workshop/5.12-Cleanup/5.12.2-budget-deleted-example.png)

   Ảnh minh họa cách chọn budget và thông báo/list sau thao tác; chúng không xác nhận budget trong account người đọc đã bị xóa.
16. Sau khi dọn dẹp, xem lại S3 buckets, CloudFormation stacks, CloudWatch log groups/alarms, SNS topics, Amplify apps và Billing/Cost Explorer. Dữ liệu chi phí có thể cập nhật trễ; Bills trong tháng đang mở chưa phải hóa đơn cuối cùng.

## Khắc phục sự cố

- Nếu stack báo `DELETE_FAILED` vì bucket không rỗng, dùng thao tác **Empty** ở đúng bucket tài liệu, bao gồm object versions và delete markers.
- Nếu chọn force delete, một số resource có thể được giữ lại. Tìm trạng thái `DELETE_SKIPPED` trong tài nguyên của deleted stack hoặc kiểm tra trực tiếp dịch vụ tương ứng rồi xóa thủ công khi an toàn.
- Xóa Cognito User Pool có thể xóa vĩnh viễn tài khoản thử nghiệm. Xác minh đúng pool của workshop.

---
title: "Quy trình triển khai Backend bằng AWS SAM"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.1.3 </b> "
---

## Mục tiêu

Triển khai backend UniCloud DMS theo một quy trình có thể lặp lại. AWS SAM chuyển `backend/template.yaml` thành CloudFormation stack; stack tạo Cognito, HTTP API, Lambda, DynamoDB và S3. Người học dùng tài khoản AWS, IAM role, tên stack và Region của mình.

## 1. Chuẩn bị tài khoản và profile

1. Đăng nhập AWS Console bằng tài khoản lab được cấp và chọn một Region xuyên suốt. Dự án mẫu dùng `ap-southeast-1` (Singapore).
2. Mở Billing and Cost Management, xem giá dịch vụ dự kiến và tạo AWS Budget cùng cảnh báo. Budget chỉ gửi thông báo, không tự dừng dịch vụ.
3. Cấu hình AWS CLI bằng IAM Identity Center nếu tổ chức có SSO:

   ```powershell
   aws configure sso --profile unicloud-lab
   aws sso login --profile unicloud-lab
   aws sts get-caller-identity --profile unicloud-lab
   ```

   Nếu môi trường học tập không có SSO, dùng danh tính IAM được quản trị viên phê duyệt với quyền vừa đủ. Không dùng root access key, không chia sẻ access key và không chép credentials vào repository. Lệnh `get-caller-identity` phải trả về đúng tài khoản dự định triển khai trước khi tiếp tục.

## 2. Tạo Lambda execution role

SAM template nhận role ARN qua parameter `LambdaExecutionRoleArn`, vì vậy tạo role trước khi deploy.

1. Mở IAM Console → Roles → Create role. Chọn trusted entity AWS service và use case Lambda.
2. Gắn policy `AWSLambdaBasicExecutionRole` để Lambda có thể ghi CloudWatch Logs.
3. Đặt tên role development dễ nhận biết, ví dụ `unicloud-dms-lambda-dev`, rồi sao chép ARN của role. Ở bước khởi tạo, quyền ghi log là đủ để CloudFormation tạo Lambda; không gắn `AdministratorAccess`.

## 3. Kiểm tra và triển khai CloudFormation stack

1. Mở terminal tại thư mục `backend` của repository. Chạy kiểm tra template và build package Lambda:

   ```powershell
   sam validate --lint
   sam build
   ```

2. Thay `<ACCOUNT_ID>` và `<ROLE_NAME>` trong lệnh dưới đây trước khi chạy. Dùng một tên stack riêng cho lab; SAM sẽ dùng bucket artifact được quản lý để tải gói triển khai lên S3. Tham số frontend origin bên dưới cho phép chạy local trước; bổ sung origin Amplify ở lần cập nhật stack sau khi đã có domain frontend.

   ```powershell
   sam deploy `
     --stack-name unicloud-dms-workshop `
     --region ap-southeast-1 `
     --profile unicloud-lab `
     --resolve-s3 `
     --confirm-changeset `
     --parameter-overrides "ParameterKey=LambdaExecutionRoleArn,ParameterValue=arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME> ParameterKey=AllowedFrontendOrigins,ParameterValue=http://localhost:5173,http://127.0.0.1:5173"
   ```

3. Đọc change set do SAM/CloudFormation hiển thị. Chỉ xác nhận khi stack name, Region, tài nguyên và chi phí dự kiến đúng với lab. Theo dõi trạng thái tại AWS Console → CloudFormation → Stacks; chờ `CREATE_COMPLETE` hoặc `UPDATE_COMPLETE`.
4. Mở tab Outputs của stack và lưu các giá trị `ApiBaseUrl`, `UserPoolId`, `UserPoolClientId`, `DocumentsTableName`, `SharesTableName`, `FavoritesTableName` và `DocumentsBucketName` vào ghi chú riêng. Không lưu token, password hay credentials.

Stack tạo Cognito User Pool và public app client, HTTP API có JWT authorizer, hai Lambda function, ba bảng DynamoDB on-demand, bucket S3 riêng tư mã hóa SSE-S3, cùng các permission/stage liên quan. Artifact bucket của SAM là tài nguyên triển khai riêng, không phải bucket tài liệu của ứng dụng.

## 4. Hoàn thiện quyền runtime của Lambda

Sau khi CloudFormation tạo xong các resource, dùng outputs để điền chính xác account ID, Region, tên ba bảng, Documents bucket và User Pool ID vào `backend/iam/lambda-runtime-policy.template.json`. Gắn policy đã hoàn thiện vào role Lambda trong IAM. Policy chỉ cấp thao tác cần thiết trên các bảng/index của ứng dụng, prefix object `users/*/documents/*` trong bucket và Cognito User Pool này. Đây là bước sau deploy vì bucket và User Pool ID do CloudFormation sinh ra. Sau đó mới tiếp tục kết nối frontend.

## 5. Nối frontend và hosting

1. Tại repository gốc, sao chép `.env.example` thành `.env.local` rồi điền Region, User Pool ID, App Client ID, API URL và Documents bucket từ CloudFormation Outputs. Đây là cấu hình công khai cho frontend, không đặt AWS secret key vào các biến `VITE_*`. Không commit `.env.local`.
2. Chạy `npm ci`, `npm run build`, rồi `npm run dev` để phục vụ frontend local. Các thao tác với Cognito và API dùng được khi `.env.local` chứa đúng Outputs.
3. Kết nối Amplify Hosting với repository ứng dụng và branch cần phát hành. Dùng `npm ci`, `npm run build`, artifact directory `dist`; cấu hình biến `VITE_*` bằng cùng outputs của stack.
4. Sau khi Amplify cấp domain, cập nhật parameter `AllowedFrontendOrigins` trong CloudFormation/SAM để bao gồm chính xác `https://<amplify-domain>`, đồng thời giữ các origin local cần thiết. CORS ở API và S3 phải khớp origin thực tế.

## 6. Xem tài nguyên và chi phí

Trong CloudFormation, kiểm tra tab Resources để biết tài nguyên thuộc stack nào. Xem Amazon CloudWatch log groups của hai Lambda khi cần đọc log vận hành. Theo dõi Billing and Cost Management/Budgets; dữ liệu Bills và Cost Explorer có thể cập nhật trễ, và số liệu ước tính không phải hóa đơn cuối cùng.

## 7. Quy ước dọn dẹp

Khi chủ dự án quyết định kết thúc lab, trước tiên xác nhận account, Region và stack name; lưu lại thông tin cần giữ. Xóa stack ứng dụng bằng CloudFormation để xóa các resource do stack quản lý. Nếu bucket S3 còn object, phải làm trống bucket trước; kiểm tra riêng bucket artifact SAM và Amplify Hosting vì chúng có thể nằm ngoài stack ứng dụng. Sau đó xem lại các tài nguyên độc lập như IAM role và budget alert, chỉ xóa tài nguyên riêng của lab. Chờ độ trễ billing rồi kiểm tra lại chi phí. Các bước này là quy trình hướng dẫn, không có nghĩa resource của hệ thống đang chạy đã bị xóa.

## Tham chiếu

- Mã nguồn SAM: `backend/template.yaml`.
- Lambda runtime policy mẫu: `backend/iam/lambda-runtime-policy.template.json`.
- Cấu hình biến frontend: `.env.example`.
- Các bước chi tiết theo dịch vụ: mục 5.3 đến 5.9; dọn dẹp: mục 5.12.

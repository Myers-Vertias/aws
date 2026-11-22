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

### Bước 1: Xác nhận quyền triển khai và chọn AWS account

Đăng nhập bằng tài khoản lab hoặc role do công ty/trường cấp, rồi chọn một Region cho toàn bộ workshop. Dự án mẫu dùng `ap-southeast-1` (Singapore). Danh tính triển khai cần quyền dùng CloudFormation, Lambda, Cognito, API Gateway, DynamoDB, S3 và CloudWatch, cùng quyền `iam:PassRole` giới hạn vào Lambda execution role của workshop. SAM cũng cần tạo/dùng bucket artifact riêng.

Nếu chưa có các quyền trên, nhờ quản trị viên cấp lab role hoặc permission set phù hợp trước khi làm tiếp. Không dùng root key hoặc cấp `AdministratorAccess` chỉ để vượt lỗi.

### Bước 2: Kiểm tra chi phí dự kiến và tạo cảnh báo

Mở **Billing and Cost Management**, xem giá các dịch vụ sẽ dùng và tạo AWS Budget với cảnh báo actual/forecast phù hợp. Budget chỉ gửi thông báo, không tự dừng tài nguyên hoặc giới hạn chi tiêu.

### Bước 3: Cấu hình và xác nhận AWS CLI profile

Cài AWS CLI v2 trước. Nếu tổ chức dùng IAM Identity Center, mở PowerShell và chạy lần lượt:

```powershell
aws configure sso --profile unicloud-lab
aws sso login --profile unicloud-lab
aws sts get-caller-identity --profile unicloud-lab
```

Khi được hỏi, nhập SSO start URL và SSO Region do quản trị viên cung cấp, rồi chọn account và permission set dành cho workshop. Nếu môi trường không có SSO, dùng profile của danh tính IAM đã được phê duyệt. Lệnh cuối phải trả về đúng account ID dự định triển khai; dừng lại nếu sai tài khoản. Không chia sẻ access key hoặc chép credentials vào repository.

## 2. Tạo Lambda execution role

SAM template nhận role ARN qua parameter `LambdaExecutionRoleArn`, vì vậy tạo role trước khi deploy.


### Bước 1: Tạo role tin cậy dịch vụ Lambda

Trong AWS Console, mở **IAM → Roles → Create role**. Chọn trusted entity **AWS service**, rồi chọn use case **Lambda**. Thao tác này tạo trust relationship để Lambda có thể sử dụng role. Nếu đã được cấp sẵn role riêng cho workshop, dùng role đó và bỏ qua bước tạo mới.

### Bước 2: Cấp quyền ghi CloudWatch Logs

Trong danh sách permissions, tìm và gắn AWS-managed policy `AWSLambdaBasicExecutionRole`. Quyền này cho phép function ghi log; quyền truy cập bảng, bucket và User Pool sẽ được thêm riêng sau khi deploy.

### Bước 3: Đặt tên và sao chép ARN

Đặt tên role riêng cho lab, ví dụ `unicloud-dms-lambda-dev`, tạo role rồi mở trang chi tiết để sao chép **ARN**. Giữ ARN này để dùng ở bước deploy. Không gắn `AdministratorAccess`.

## 3. Kiểm tra và triển khai CloudFormation stack


### Bước 1: Kiểm tra và build backend

Mở PowerShell tại thư mục `backend` của repo ứng dụng `serverless-document-management`. Chạy hai lệnh sau. Lệnh thứ nhất kiểm tra SAM template; lệnh thứ hai build mã Lambda trước khi tải lên AWS:

```powershell
sam validate --lint
sam build
```


### Bước 2: Điền account ID và role vào lệnh deploy

Thay `<ACCOUNT_ID>` bằng account ID vừa xác nhận từ AWS CLI và `<ROLE_NAME>` bằng tên role Lambda đã tạo. Có thể đổi `unicloud-dms-workshop` thành tên stack riêng của mình, nhưng phải dùng nhất quán ở các bước sau. `--resolve-s3` yêu cầu SAM quản lý bucket artifact để tải gói deploy; đây không phải bucket lưu tài liệu. Các origin trong lệnh cho phép frontend chạy trên máy local. Sau khi có domain Amplify, sẽ thêm domain đó ở mục 5.

```powershell
sam deploy `
     --stack-name unicloud-dms-workshop `
     --region ap-southeast-1 `
     --profile unicloud-lab `
     --resolve-s3 `
     --confirm-changeset `
     --parameter-overrides "ParameterKey=LambdaExecutionRoleArn,ParameterValue=arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME> ParameterKey=AllowedFrontendOrigins,ParameterValue=http://localhost:5173,http://127.0.0.1:5173"
```


### Bước 3: Kiểm tra change set và chờ stack hoàn tất

Đọc change set do SAM/CloudFormation hiển thị. So lại account, tên stack, Region, role ARN và các tài nguyên sắp tạo. Chỉ xác nhận nếu đúng lab. Sau đó mở **CloudFormation → Stacks**, chọn stack và chờ `CREATE_COMPLETE`. Nếu thấy `CREATE_FAILED` hoặc `ROLLBACK_COMPLETE`, mở tab **Events**, đọc resource đầu tiên báo lỗi và xử lý trước khi deploy lại.

### Bước 4: Lưu Outputs để dùng cho các phần tiếp theo

Trong trang stack, mở tab **Outputs**. Lưu `ApiBaseUrl`, `UserPoolId`, `UserPoolClientId`, `DocumentsTableName`, `SharesTableName`, `FavoritesTableName` và `DocumentsBucketName` vào ghi chú riêng. Đây là các giá trị riêng của stack mới; không lấy ID/URL từ ảnh minh chứng. Không lưu token, password hay credentials.

Stack tạo Cognito User Pool và public app client, HTTP API có JWT authorizer, hai Lambda function, ba bảng DynamoDB on-demand, bucket S3 riêng tư mã hóa SSE-S3, cùng các permission/stage liên quan. Artifact bucket của SAM là tài nguyên triển khai riêng, không phải bucket tài liệu của ứng dụng.

## 4. Hoàn thiện quyền runtime của Lambda

Sau khi CloudFormation tạo stack, role hiện chỉ có quyền ghi log. Hoàn thiện policy runtime để Lambda có thể truy cập đúng các bảng, object và User Pool của stack. Làm bước này sau deploy vì bucket và User Pool ID được CloudFormation sinh ra.

### Bước 1: Điền thông tin của stack vào policy mẫu

Mở `backend/iam/lambda-runtime-policy.template.json` trong repo ứng dụng. Thay placeholder bằng các giá trị dưới đây:

- `<REGION>`: Region đã chọn, ví dụ `ap-southeast-1`.
- `<ACCOUNT_ID>`: account ID từ `aws sts get-caller-identity`.
- `<STACK_NAME>`: giá trị đã dùng cho `--stack-name`.
- `<BUCKET_NAME>`: Output `DocumentsBucketName`.
- `<USER_POOL_ID>`: Output `UserPoolId`.

Tên bảng theo mẫu `<STACK_NAME>-documents`, `<STACK_NAME>-shares` và `<STACK_NAME>-favorites`. Đối chiếu với Outputs/Resources trước khi lưu, không đoán tên. Lưu policy đã điền thành file riêng và giữ file `.template.json` nguyên trạng.

### Bước 2: Kiểm tra phạm vi quyền trước khi gắn

Mở file policy đã điền, xác nhận không còn chuỗi placeholder dạng `<...>` và JSON vẫn hợp lệ. Policy chỉ nên cho phép các thao tác cần thiết trên ba bảng/index của ứng dụng, prefix `users/*/documents/*` của bucket và đúng User Pool này. Không đổi Resource thành `*`.

### Bước 3: Gắn policy vào Lambda execution role

Trong AWS Console, mở **IAM → Roles → role Lambda của workshop → Add permissions → Create inline policy → JSON**. Dán nội dung policy đã điền, chọn **Next**, đặt tên `DmsDocumentRuntimeAccess`, rồi tạo policy. Quay lại trang role và kiểm tra có cả `AWSLambdaBasicExecutionRole` lẫn `DmsDocumentRuntimeAccess`.

Nếu quản trị viên đã gắn policy này cho role được cấp sẵn, chỉ kiểm tra nội dung và không tạo thêm bản trùng. Sau bước này, Lambda có quyền runtime để xử lý các request tài liệu.

## 5. Nối frontend và hosting


### Bước 1: Tạo file cấu hình frontend local

Tại thư mục gốc repo ứng dụng, sao chép `.env.example` thành `.env.local`. Điền các biến theo CloudFormation Outputs; không để nguyên giá trị `replace-with-...`:

| Biến | Giá trị cần điền |
| --- | --- |
| `VITE_AWS_REGION` | Region của stack |
| `VITE_COGNITO_USER_POOL_ID` | Output `UserPoolId` |
| `VITE_COGNITO_USER_POOL_CLIENT_ID` | Output `UserPoolClientId` |
| `VITE_API_BASE_URL` | Output `ApiBaseUrl` |
| `VITE_S3_BUCKET_NAME` | Output `DocumentsBucketName` |

Các biến `VITE_*` được đóng gói vào trình duyệt nên chỉ điền ID/URL công khai, không thêm AWS secret key. Không commit `.env.local`.

### Bước 2: Cài package và chạy frontend trên máy

Từ thư mục gốc repo ứng dụng, chạy lần lượt `npm ci`, `npm run build` và `npm run dev`. Mở địa chỉ được Vite in ra, thường là `http://localhost:5173`. Sau khi sửa `.env.local`, dừng và khởi động lại Vite. Nếu đăng nhập/API lỗi, kiểm tra lại các giá trị Output trước khi tiếp tục.

### Bước 3: Kết nối repository ứng dụng với Amplify

Trong AWS Console, mở **AWS Amplify → Create new app → Host web app**, kết nối GitHub, chọn repository ứng dụng `serverless-document-management` và branch `main`. Xác nhận build command `npm ci && npm run build` và output directory `dist`. Thêm năm biến `VITE_*` ở bước 1 bằng Outputs của cùng stack. Không chọn repository báo cáo `aws`.

### Bước 4: Thêm domain Amplify vào CORS

Sau khi Amplify cấp domain HTTPS, sao chép origin đầy đủ, chỉ gồm scheme và hostname, ví dụ `https://main.xxxxx.amplifyapp.com`. Deploy lại stack với `AllowedFrontendOrigins` chứa origin chính xác này và giữ origin local nếu còn cần. Không thêm dấu `/` ở cuối. API Gateway và S3 CORS phải cùng cho phép origin đó; CORS không thay thế xác thực hay phân quyền.

## 6. Xem tài nguyên và chi phí

Trong CloudFormation, kiểm tra tab Resources để biết tài nguyên thuộc stack nào. Xem Amazon CloudWatch log groups của hai Lambda khi cần đọc log vận hành. Theo dõi Billing and Cost Management/Budgets; dữ liệu Bills và Cost Explorer có thể cập nhật trễ, và số liệu ước tính không phải hóa đơn cuối cùng.

## 7. Quy ước dọn dẹp

Khi chủ dự án quyết định kết thúc lab, trước tiên xác nhận account, Region và stack name; lưu lại thông tin cần giữ. Xóa stack ứng dụng bằng CloudFormation để xóa các resource do stack quản lý. Nếu bucket S3 còn object, phải làm trống bucket trước; kiểm tra riêng bucket artifact SAM và Amplify Hosting vì chúng có thể nằm ngoài stack ứng dụng. Sau đó xem lại các tài nguyên độc lập như IAM role và budget alert, chỉ xóa tài nguyên riêng của lab. Chờ độ trễ billing rồi kiểm tra lại chi phí. Các bước này là quy trình hướng dẫn, không có nghĩa resource của hệ thống đang chạy đã bị xóa.

## Tham chiếu

- Mã nguồn SAM: `backend/template.yaml`.
- Lambda runtime policy mẫu: `backend/iam/lambda-runtime-policy.template.json`.
- Cấu hình biến frontend: `.env.example`.
- Các bước chi tiết theo dịch vụ: mục 5.3 đến 5.9; dọn dẹp: mục 5.12.

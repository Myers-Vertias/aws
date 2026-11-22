---
title: "Chuẩn bị môi trường phát triển"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.1.1 </b> "
---

## Mục tiêu

Chuẩn bị máy phát triển, chạy thử frontend và xác nhận backend có thể khởi động cục bộ trước khi cấu hình tài nguyên AWS.

## Công cụ cần cài

- [Git for Windows](https://git-scm.com/download/win) để lấy mã nguồn và quản lý thay đổi.
- [Node.js 24 LTS](https://nodejs.org/en/download) cùng npm để cài dependency, chạy frontend React/Vite và khớp major version với Lambda runtime `nodejs24.x`.
- [AWS CLI v2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) để xác thực và thao tác AWS ở các bước triển khai tiếp theo.
- [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html) để kiểm tra, build và chạy thử backend Lambda/API Gateway.
- [Docker Desktop cho Windows](https://docs.docker.com/desktop/setup/install/windows-install/) đang chạy nếu muốn dùng `sam local start-api`.

{{% notice info %}}
Trang này chỉ chuẩn bị và chạy thử trên máy. Chưa cần tạo AWS credentials để mở giao diện frontend hoặc gọi health endpoint local. Không dùng root access key và không lưu access key, mật khẩu, JWT hay presigned URL trong source code.
{{% /notice %}}

## Hướng dẫn thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.

### A. Lấy mã nguồn và chạy frontend


#### Bước 1: Cài Git, Node.js/npm, AWS CLI v2, AWS SAM CLI và Docker

Cài Git, Node.js/npm, AWS CLI v2, AWS SAM CLI và Docker Desktop. Docker Desktop cần chạy khi dùng SAM local.

#### Bước 2: Tải mã nguồn và mở thư mục dự án

Chạy các lệnh sau để tải source và chuyển vào thư mục dự án:

```powershell
git clone https://github.com/Myers-Vertias/serverless-document-management.git
Set-Location serverless-document-management
```


#### Bước 3: Xác nhận thư mục hiện tại có package.json

Xác nhận thư mục hiện tại có `package.json`, `package-lock.json` và `.env.example`; sau đó kiểm tra phiên bản công cụ:

```powershell
node --version
npm --version
git --version
aws --version
sam --version
docker --version
```


#### Bước 4: Cài đúng các package đã khóa trong package-lock.json

Cài đúng các package đã khóa trong `package-lock.json`:

```powershell
npm ci
```


#### Bước 5: Chạy kiểm tra lint và tạo production build

Chạy kiểm tra lint và tạo production build:

```powershell
npm run lint
npm run build
```

Ghi nhận cảnh báo lint hiện có; không nhầm cảnh báo với lỗi build.


#### Bước 6: Tạo file cấu hình local từ mẫu

Tạo file cấu hình local từ mẫu:

```powershell
Copy-Item .env.example .env.local
```

Giữ các giá trị `replace-with-...` cho tới khi đã tạo các dịch vụ AWS. Điền Region, Cognito User Pool ID, App Client ID, API URL và tên S3 bucket theo outputs ở các mục triển khai sau. Không commit `.env.local`.


#### Bước 7: Khởi động Vite

Khởi động Vite:

```powershell
npm run dev
```


#### Bước 8: Mở ứng dụng trên địa chỉ local của Vite

Mở địa chỉ local được Vite in ra, thường là `http://localhost:5173`. Xác nhận trang đăng nhập hiển thị. Đăng nhập Cognito chỉ hoạt động sau khi cập nhật các giá trị thật vào `.env.local`.

### B. Kiểm tra backend local


#### Bước 1: Mở terminal thứ hai tại thư mục backend

Mở terminal thứ hai, chuyển vào thư mục `backend` của repository:

```powershell
Set-Location backend
```


#### Bước 2: Kiểm tra SAM template và build Lambda

Kiểm tra SAM template và build Lambda:

```powershell
sam validate --lint
sam build
```


#### Bước 3: Nếu Docker Desktop đang chạy, khởi động API local

Nếu Docker Desktop đang chạy, khởi động API local:

```powershell
sam local start-api --port 3000
```


#### Bước 4: Mở http://127.0.0.1:3000/health

Mở `http://127.0.0.1:3000/health`. Kết quả mong đợi:

```json
{"status":"ok","service":"dms-api"}
```


#### Bước 5: Nhấn Ctrl+C để dừng các tiến trình local khi hoàn tất

Nhấn `Ctrl+C` để dừng các tiến trình local khi hoàn tất.

Health endpoint trên chỉ xác nhận API local khởi động được. Nó không xác nhận Cognito, DynamoDB, S3 hoặc API đã deploy hoạt động; các phần đó được cấu hình riêng ở các mục tiếp theo.

### C. Chuẩn bị cho các bước AWS


#### Bước 1: Chọn AWS account và Region, rồi cấu hình CLI profile

Sang mục 5.1.2 để chọn AWS account/Region và cấu hình identity dành cho lab. Dùng tài khoản development có quyền phù hợp, không dùng root.

#### Bước 2: Xác nhận danh tính của CLI profile

Sau khi cấu hình profile riêng, xác nhận danh tính bằng `aws sts get-caller-identity --profile <lab-profile>`. Thay `<lab-profile>` bằng tên profile của người học; không dùng profile của người hướng dẫn.

#### Bước 3: Lưu trạng thái mã nguồn trước khi bắt đầu

Tạo branch Git hoặc commit lưu trạng thái trước khi bắt đầu tích hợp backend.

## Baseline ghi nhận ngày 2026-11-10

- `npm ci`: hoàn tất thành công, cài thêm 61 packages. Tóm tắt audit của npm báo một vulnerability mức high; chưa chạy `npm audit` để điều tra chi tiết.
- `npm run lint`: hoàn tất, không có lỗi và có 10 warnings. Các warning có sẵn nằm ở `Profile.jsx`, `Dashboard.jsx`, `Trash.jsx`, `Shared.jsx`, `Documents.jsx`, `Favorites.jsx` và `DocumentDetail.jsx` (quy tắc React purity, state trong effect và dependency của hooks).
- `npm run build`: hoàn tất thành công với Vite 8.3.3; đã tạo production assets trong `dist/`.
- Kiểm tra nền trước khi tích hợp AWS: Vite phục vụ ứng dụng tại `http://127.0.0.1:5173/`; đăng nhập mock tích hợp sẵn mở được `/dashboard` và hiển thị ba tài liệu mẫu. Đây là ghi nhận luồng mock ban đầu. Các luồng dùng AWS backend được ghi ở mục 5.8–5.10.
- Phiên bản môi trường ở lần ghi nhận baseline: Node.js 25.9.0 và npm 11.14.1. Hướng dẫn hiện khuyến nghị Node.js 24 LTS để khớp runtime Lambda `nodejs24.x`; phiên bản baseline cũ được giữ như thông tin lịch sử của lần chạy.
- AWS CLI đăng nhập qua IAM user `dms-dev`; `aws sts get-caller-identity --profile dms-dev` trả về ARN kết thúc bằng `user/dms-dev`.
- AWS SAM CLI đã cài thành công, phiên bản 1.171.0.
- Trong `backend/`, `sam validate --lint` xác nhận `template.yaml` là SAM template hợp lệ; chưa deploy tài nguyên AWS.
- `sam build` hoàn tất thành công cho `HealthFunction` với runtime `nodejs24.x`, architecture `x86_64`; build artifact nằm trong `.aws-sam/build`. Function scaffold chưa cần package dependencies.
- `sam local start-api --port 3000` khởi chạy API cục bộ bằng Docker; gọi `GET http://127.0.0.1:3000/health` nhận được `{"status":"ok","service":"dms-api"}`. Bước này chỉ xác nhận health route cục bộ; chưa deploy tài nguyên lên AWS.

## Vị trí ảnh chụp màn hình

Dashboard mock, transcript lint/build frontend và kết quả `sam --version` đã được ghi ở phần baseline; không lưu ảnh riêng cho các lệnh này. Kết quả SAM validate/build và health local có ảnh minh chứng bên dưới.

![Ảnh 5.1.1: SAM template validation thành công](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.1-sam-template-validation.png)

![Ảnh 5.1.1: SAM build thành công cho HealthFunction](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.1-sam-build-success.png)

![Ảnh 5.1.1: Health endpoint SAM cục bộ trả về status ok](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.1-sam-local-health.png)

![Ảnh 5.1.1: Kết quả SAM validate và SAM build trong cùng một phiên PowerShell](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.1-sam-validation-and-build.png)

## Kết quả mong đợi

Production build và SAM checks ban đầu thành công; lint có 10 warning sẵn có. Mock login/Dashboard và SAM health route local hoạt động. Đây là baseline trước tích hợp; các mục sau ghi nhận luồng Cognito/API/S3/DynamoDB đã triển khai.

## Khắc phục sự cố

- Nếu cài npm thất bại, kiểm tra phiên bản Node.js và thư mục đang làm việc.
- Dùng đúng URL và cổng được Vite in ra.

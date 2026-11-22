---
title: "Cấu hình Môi trường và Xác thực"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.8.1 </b> "
---

{{% notice info %}}
Frontend đã cấu hình Cognito và API đang triển khai. Đăng nhập thật cùng request tài liệu có xác thực được ghi ở mục 5.8.2; không chụp `.env.local` vì chứa cấu hình riêng của môi trường.
{{% /notice %}}

## Mục tiêu

Cung cấp cấu hình công khai và kết nối frontend với xác thực Cognito thật.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Điền cấu hình từ CloudFormation Outputs

Mở **CloudFormation → Stacks → stack workshop → Outputs** và giữ trang này để tra giá trị. Ở thư mục gốc repo ứng dụng, sao chép `.env.example` thành `.env.local`. Điền lần lượt `VITE_AWS_REGION` từ Region của stack, `VITE_COGNITO_USER_POOL_ID` từ `UserPoolId`, `VITE_COGNITO_USER_POOL_CLIENT_ID` từ `UserPoolClientId`, `VITE_API_BASE_URL` từ `ApiBaseUrl` và `VITE_S3_BUCKET_NAME` từ `DocumentsBucketName`. Không chép giá trị từ ảnh cũ vì mỗi stack có ID riêng.

### Bước 2: Cài package và khởi động frontend

Mở terminal tại thư mục gốc repo ứng dụng, chạy `npm ci` rồi `npm run dev`. Mở địa chỉ local mà Vite in ra. Sau mỗi lần sửa `.env.local`, dừng Vite bằng `Ctrl+C` rồi chạy `npm run dev` lại. Không commit file `.env.local`.

### Bước 3: Tạo tài khoản thử nghiệm và đăng nhập Cognito

Trên trang đăng nhập của ứng dụng, chọn đăng ký tài khoản bằng email mà anh/chị có thể mở. Dùng mật khẩu đáp ứng chính sách ở mục 5.3.1. Mở email, lấy mã xác nhận và nhập vào ứng dụng. Sau khi hiện thông báo xác nhận thành công, đăng nhập bằng email và mật khẩu đó. Kết quả cần thấy là dashboard đã đăng nhập; không đưa mã xác nhận hoặc token vào ảnh/báo cáo.

### Bước 4: Xác nhận request API có token

Mở **Developer Tools → Network**, tải lại trang Kho tài liệu rồi chọn request `GET /documents`. Request phải trả `200` và hiển thị danh sách của tài khoản. Nếu trả `401`, đăng xuất rồi đăng nhập lại; nếu API không kết nối được, kiểm tra `VITE_API_BASE_URL` và CORS ở mục 5.7.2.

Biến `VITE_*` được đưa vào frontend khi build nên chỉ chứa ID/URL công khai. Tuyệt đối không đưa AWS secret access key, credentials, password hay token vào các biến này.

## Xác minh

Dashboard local sau đăng nhập và request lấy danh sách tài liệu có xác thực được ghi ở mục 5.8.2. Không lưu ảnh riêng màn hình đăng nhập; báo cáo không chứa mật khẩu, mã xác nhận hoặc token.

## Kết quả mong đợi

Đã xác minh đăng nhập Cognito thật và truy cập API được bảo vệ. `.env.local` chứa cấu hình riêng nên không đưa vào ảnh hoặc Git.

## Khắc phục sự cố

- Khởi động lại Vite sau khi đổi giá trị .env.
- Không log hay commit token session đầy đủ.

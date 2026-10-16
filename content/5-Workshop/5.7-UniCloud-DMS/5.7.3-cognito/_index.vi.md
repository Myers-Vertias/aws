---
title: "Cấu hình Amazon Cognito"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.7.3 </b> "
---

## Mục tiêu

Tạo Cognito User Pool cho tài khoản người dùng thật và chuẩn bị app client cho frontend. User Pool chỉ thay thế xác thực mock sau khi mã tích hợp đã được triển khai và kiểm thử.

## Bước 1 — Tạo User Pool

1. Mở [Amazon Cognito console](https://console.aws.amazon.com/cognito/).
2. Chọn **Create user pool**.
3. Cấu hình đăng nhập bằng email cho workshop này và chọn cách xác minh cũng như khôi phục tài khoản phù hợp với môi trường thử nghiệm.
4. Bật xác minh email trước khi cho phép sử dụng tài khoản nếu phù hợp với luồng đăng ký đã chọn.
5. Đặt tên development rõ ràng, ví dụ **unicloud-dms-dev-users**.
6. Kiểm tra lại lựa chọn và tạo pool.

**[Vị trí ảnh 5.7.3-01 — Cấu hình User Pool và kết quả tạo]**  
Chụp phương thức đăng nhập và thông tin pool sau khi tạo. Che thông tin tài khoản nhạy cảm.

## Bước 2 — Tạo app client

1. Mở User Pool mới và tìm mục **App clients**.
2. Tạo app client dành cho ứng dụng single-page chạy trên trình duyệt.
3. **Không** tạo client secret cho SPA frontend-only. Secret nhúng trong JavaScript không còn bí mật.
4. Ghi User Pool ID, App Client ID và Region vào ghi chú cấu hình cục bộ an toàn. Các ID này là cấu hình, không phải mật khẩu, nhưng vẫn cần quản lý cẩn thận.
5. Nếu dùng trang đăng nhập hosted của Cognito, cấu hình callback URL và sign-out URL cho origin phát triển cục bộ và domain hosting cuối cùng. Chỉ thêm origin thực sự sử dụng.

**[Vị trí ảnh 5.7.3-02 — Cài đặt App Client]**  
Chụp cài đặt app client mà không hiển thị token, secret hay thông tin cá nhân tài khoản.

## Bước 3 — Chuẩn bị tích hợp frontend

Khuyến nghị tích hợp React bằng API xác thực của AWS Amplify JavaScript hoặc một Cognito SDK được hỗ trợ tương đương. Chỉ cài thư viện khi triển khai bước này.

Cấu hình sẽ được truyền qua biến môi trường, ví dụ:

- **VITE_AWS_REGION**
- **VITE_COGNITO_USER_POOL_ID**
- **VITE_COGNITO_CLIENT_ID**

Đây là cấu hình ứng dụng công khai, không phải secret credentials. Không thêm AWS secret access key vào biến Vite; mọi biến có tiền tố VITE đều được đưa vào bundle trình duyệt.

Triển khai đăng ký, xác nhận tài khoản, đăng nhập, đăng xuất và lấy session. Chỉ gửi token Cognito tới API Gateway sau khi cấu hình authorizer ở module sau đã hoàn tất.

## Bước 4 — Xác minh xác thực

Tạo hai tài khoản thử nghiệm qua luồng ứng dụng thật. Kiểm tra đăng ký, xác minh email, đăng nhập và đăng xuất. Sau khi tích hợp frontend hoàn tất, xác nhận session hợp lệ có thể cung cấp token và trình duyệt không ghi log hoặc hiển thị toàn bộ token.

## Kết quả mong đợi

User Pool development và SPA app client đã tồn tại; các giá trị cấu hình không bí mật đã được ghi lại. Chỉ xem là tích hợp hoàn chỉnh sau khi đăng ký/đăng nhập bằng frontend được xác minh.

## Khắc phục sự cố

- **Invalid client:** xác nhận App Client ID thuộc đúng User Pool và Region.
- **Redirect mismatch:** so sánh chính xác callback/sign-out URL, bao gồm scheme, host, port và path.
- **Lỗi client secret:** dùng app client cho browser không có client secret.
- **Session hết hạn hoặc bị thiếu:** đăng nhập lại và kiểm tra phản hồi SDK mà không in token thô.

## Lưu ý cleanup

Chưa xóa User Pool cho đến khi kiểm thử các bước sau hoàn tất. Khi xóa pool, người dùng Cognito và các cài đặt liên quan có thể bị xóa vĩnh viễn.

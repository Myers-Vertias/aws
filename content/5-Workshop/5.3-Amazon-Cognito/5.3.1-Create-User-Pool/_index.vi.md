---
title: "Xác minh Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Kiểm tra User Pool do SAM tạo để xác thực tài khoản UniCloud DMS.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Deploy SAM template theo mục 5.1.3

Deploy SAM template theo mục 5.1.3. Template tạo User Pool, vì vậy không tạo thêm pool thủ công.

### Bước 2: Mở Cognito trong đúng Region và chọn User pool

Trong AWS Console, chọn đúng Region → Amazon Cognito → User pools → mở pool thuộc CloudFormation stack của lab. Đối chiếu tên pool trong tab Resources/Outputs của stack.

### Bước 3: Mở cấu hình đăng nhập và xác nhận email là username

Mở cấu hình đăng nhập và xác nhận email là username attribute, email auto-verified và account recovery dùng email đã xác minh. Template đặt mật khẩu tối thiểu 12 ký tự, bắt buộc chữ hoa, chữ thường, số và ký hiệu.

### Bước 4: Ghi Pool ID và Region vào cấu hình local

Ghi Pool ID và Region vào cấu hình local. Không ghi password, mã xác nhận hay token vào báo cáo.

## Cấu hình đã triển khai và xác minh

Stack `dms-dev` tạo User Pool `ap-southeast-1_hWIAdC6Xu` tại `ap-southeast-1`. SAM template cấu hình email để đăng nhập/xác minh, khôi phục tài khoản qua email đã xác minh và mật khẩu tối thiểu 12 ký tự gồm chữ hoa, chữ thường, số và ký hiệu. Output của pool/client được chụp ở mục 5.9.2. Đã đăng ký, xác nhận và đăng nhập Cognito thật qua ứng dụng; không lưu ảnh riêng màn hình cài đặt pool.

## Kết quả mong đợi

User Pool development đã triển khai tại Singapore và frontend đã dùng Cognito thật. Xem mục 5.3.2 và 5.8.2 về app client và kết quả đăng nhập.

## Khắc phục sự cố

- Nếu sai Region, sửa trước khi tạo resource phụ thuộc.
- Không đưa mã xác minh hoặc credentials vào minh chứng.

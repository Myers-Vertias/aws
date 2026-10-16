---
title: "Dọn dẹp tài nguyên workshop"
date: 2026-10-16
weight: 12
chapter: false
pre: " <b> 5.7.12 </b> "
---

## Mục tiêu

Xóa tài nguyên được tạo riêng cho workshop khi không còn cần dùng, đồng thời tránh xóa nhầm tài nguyên khác trong tài khoản.

{{% notice warning %}}
Cleanup có thể xóa vĩnh viễn tài liệu mẫu đã upload, Cognito test user, bảng dữ liệu và deployment ứng dụng. Trước khi xóa, hãy xác minh AWS account, Region, tên resource và tiền tố development. Không xóa tài nguyên thuộc project khác hoặc môi trường dùng chung.
{{% /notice %}}

## Bước 1 — Lưu minh chứng cần thiết

Trước cleanup, chụp website cuối cùng, bảng kết quả kiểm thử, tổng quan tài nguyên liên quan và log đã làm sạch để đưa vào báo cáo. Không chụp credentials, JWT, presigned URL hoặc nội dung tài liệu riêng tư.

**[Vị trí ảnh 5.7.12-01 — Ứng dụng cuối và tổng kết xác minh]**  
Lưu minh chứng ứng dụng đã kiểm thử trước khi xóa tài nguyên.

## Bước 2 — Xóa hosting và điểm vào API

1. Nếu không còn cần, xóa Amplify app tạo cho workshop.
2. Xóa API Gateway API tạo cho lab này.
3. Xác nhận đây đúng là tài nguyên workshop, không phải API/hosting app được project khác sử dụng.

## Bước 3 — Xóa Lambda và log group

1. Xóa Lambda function của workshop sau khi API không còn phụ thuộc vào chúng.
2. Kiểm tra và xóa CloudWatch log group tương ứng nếu đã lưu đủ minh chứng và không cần lưu log thêm.
3. Giữ nguyên log group không liên quan.

## Bước 4 — Làm trống và xóa S3 bucket

1. Mở đúng bucket S3, xác minh tên và Region.
2. Tải về hoặc giữ lại minh chứng mẫu cần thiết trước khi xóa.
3. Làm trống bucket. Nếu bật versioning, xóa tất cả object version và delete marker.
4. Chỉ xóa bucket sau khi nó đã trống.

Không tắt Block Public Access trong quá trình cleanup. Bucket phải luôn ở chế độ riêng tư trong suốt workshop.

**[Vị trí ảnh 5.7.12-02 — Bucket workshop đã làm trống và xóa]**  
Chụp kết quả cleanup thật, không để lộ bucket không liên quan.

## Bước 5 — Xóa bảng DynamoDB và Cognito test pool

1. Xóa các bảng Documents, Shares và Favorites tạo cho workshop nếu không còn cần dùng.
2. Chỉ xóa Cognito development User Pool sau khi không còn cần test user và minh chứng xác thực.
3. Kiểm tra tên bảng và User Pool trước khi xác nhận xóa.

## Bước 6 — Xóa IAM resource chỉ dùng cho workshop

Xóa policy/role IAM riêng của workshop sau khi Lambda đã bị xóa và không còn tài nguyên nào phụ thuộc. Không xóa role dùng chung, policy cấp tài khoản hoặc role của ứng dụng khác.

## Bước 7 — Kiểm tra tài nguyên và billing lần cuối

- [ ] Đã xóa Amplify app nếu không còn cần.
- [ ] Đã xóa API Gateway API.
- [ ] Đã xóa Lambda function của workshop.
- [ ] Đã xóa log group liên quan hoặc chủ động giữ lại thời hạn cần thiết.
- [ ] Đã làm trống S3 bucket, gồm cả version nếu bật, và xóa bucket nếu không còn cần.
- [ ] Đã xóa bảng DynamoDB của workshop.
- [ ] Đã xóa Cognito development User Pool nếu không còn cần.
- [ ] Đã xóa an toàn IAM role/policy chỉ phục vụ workshop.
- [ ] Đã kiểm tra AWS Billing/Cost Explorer để tìm tài nguyên hoặc mức sử dụng còn lại.

**[Vị trí ảnh 5.7.12-03 — Kiểm tra tài nguyên cuối cùng]**  
Chụp kết quả rà soát tài nguyên và billing sau khi che thông tin tài khoản không liên quan.

## Hoàn tất

Workshop được xem là hoàn tất khi quá trình triển khai và kiểm thử đã được ghi nhận, đồng thời tài nguyên không còn cần dùng đã được dọn dẹp. Giữ repository Git và tài liệu báo cáo; không cần giữ AWS resources hoạt động chỉ để bài hướng dẫn tồn tại.

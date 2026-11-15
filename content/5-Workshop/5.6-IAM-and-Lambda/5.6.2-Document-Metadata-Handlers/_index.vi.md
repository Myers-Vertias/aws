---
title: "Triển khai Handler Metadata Tài liệu"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.6.2 </b> "
---

{{% notice info %}}
Hành vi handler đang triển khai được mô tả dựa trên Lambda implementation và minh chứng API/trình duyệt hiện có. Không chụp riêng thao tác Lambda test event.
{{% /notice %}}

## Mục tiêu

Triển khai thao tác tạo/liệt kê/chi tiết/cập nhật bằng người gọi đã xác thực và mô hình dữ liệu đã duyệt.

## Các bước thực hiện

1. Đọc Cognito `sub` của người gọi từ JWT authorizer claims của API Gateway; trả HTTP `401` nếu thiếu. Owner được xác định từ claim này, không lấy owner ID do trình duyệt gửi.
2. Liệt kê tài liệu sở hữu bằng `OwnerIndex` (`ownerSub`); lấy chi tiết theo `documentId`. Chỉ owner hoặc người nhận có bản ghi share mới được đọc. Tài liệu ngoài quyền truy cập trả `404`.
3. Khi hoàn tất upload, kiểm tra object S3 bằng `HeadObject`, xác minh kích thước và metadata document ID do server tạo, rồi ghi metadata gồm `ownerSub`, thông tin file, danh mục, mô tả, thẻ và timestamp.
4. Cập nhật metadata nhận các trường `description`, `category`, `tags`. Handler kiểm tra kiểu/giới hạn, chuẩn hóa và loại thẻ trùng, đồng thời chỉ cập nhật khi caller là owner và upload đang hoạt động.
5. Trả document DTO và JSON error kèm HTTP status. Key schema và owner index của bảng documents đang triển khai được ghi ở mục 5.4.1.

## Minh chứng kiểm thử

API lấy danh sách tài liệu khi đăng nhập trả HTTP `200`; request từ tài khoản khác tới tài liệu riêng chưa chia sẻ trả HTTP `404`, xem mục 5.7.2. Danh mục/mô tả đã lưu hiển thị ở trang chi tiết; bản ghi tài liệu trong DynamoDB được ghi ở mục 5.8.2 và 5.4.1. Các ảnh hiện có được dùng lại để tránh trùng lặp. Chưa chụp thao tác Lambda console test event riêng.

## Kết quả mong đợi

Handler metadata thao tác đúng bản ghi theo người dùng.

## Khắc phục sự cố

- Kiểm tra tên key phân biệt hoa/thường, cấu hình index và sub lấy từ token.
- Tránh scan toàn bảng cho truy vấn Tài liệu của tôi thông thường.

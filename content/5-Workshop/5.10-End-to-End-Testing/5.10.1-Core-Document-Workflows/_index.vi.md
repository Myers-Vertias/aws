---
title: "Kiểm thử Xác thực và Luồng Tài liệu Chính"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.10.1 </b> "
---

{{% notice info %}}
Ảnh bên dưới ghi nhận một số kết quả đã quan sát. Phân biệt ảnh chụp từ frontend local với production; không suy ra các trường hợp kiểm thử chưa chạy từ ảnh build hoặc danh sách tài liệu.
{{% /notice %}}

## Mục tiêu

Ghi nhận kết quả thực tế cho luồng đăng nhập, metadata, upload, xem và download.

## Các bước thực hiện

1. Tạo/xác minh người dùng thử nghiệm riêng, rồi kiểm thử đăng nhập và đăng xuất.
2. Upload file mẫu an toàn và xác minh object S3 riêng tư cùng metadata DynamoDB.
3. Liệt kê tài liệu, mở chi tiết, cập nhật metadata được phép và tải file xuống.
4. Thử loại/dung lượng file không hợp lệ và xác thực thiếu/hết hạn. Chỉ ghi Pass/Fail sau khi chạy test.

## Vị trí ảnh chụp màn hình

![Ảnh 5.10.1: Tài liệu hoạt động và lịch sử tải xuống trên frontend production](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-document-download-production.png)

![Ảnh 5.10.1: Object đã upload trong bucket S3 riêng tư](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-s3-object-upload.png)

![Ảnh 5.10.1: Metadata đã lưu trên trang chi tiết trong phiên frontend local](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-metadata-update-local.png)

Trường hợp thiếu token được dẫn chứng bằng HTTP `401` ở mục 5.7.2.

![Ảnh 5.10.1: Network panel hiển thị HTTP 401 cho request chưa xác thực](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

Khi chọn file `.md`, frontend từ chối và hiển thị danh sách định dạng được hỗ trợ trước khi tải lên.

![Ảnh 5.10.1: Form upload từ chối file Markdown không được hỗ trợ](/images/5-Workshop/5.10-End-to-End-Testing/5.10.1-invalid-file-extension-rejected.png)

Trong lần thực hiện workshop này chưa kiểm thử file vượt dung lượng và request dùng token hết hạn.

## Kết quả mong đợi

Luồng tài liệu chính có minh chứng lặp lại được và kết quả đã ghi nhận.

## Khắc phục sự cố

- Không đánh dấu đạt khi chưa chạy test chỉ vì code build được.
- Dùng file mẫu tổng hợp thay vì hồ sơ cá nhân.

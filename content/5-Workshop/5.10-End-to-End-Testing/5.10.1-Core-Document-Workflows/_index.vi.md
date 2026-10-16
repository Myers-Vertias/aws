---
title: "Kiểm thử Xác thực và Luồng Tài liệu Chính"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.10.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Ghi nhận kết quả thực tế cho luồng đăng nhập, metadata, upload, xem và download.

## Các bước thực hiện

1. Tạo/xác minh người dùng thử nghiệm riêng, rồi kiểm thử đăng nhập và đăng xuất.
2. Upload file mẫu an toàn và xác minh object S3 riêng tư cùng metadata DynamoDB.
3. Liệt kê tài liệu, mở chi tiết, cập nhật metadata được phép và tải file xuống.
4. Thử loại/dung lượng file không hợp lệ và xác thực thiếu/hết hạn. Chỉ ghi Pass/Fail sau khi chạy test.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.10.1 — Kết quả thật của upload, metadata và download, đã che giá trị nhạy cảm.]**

## Kết quả mong đợi

Luồng tài liệu chính có minh chứng lặp lại được và kết quả đã ghi nhận.

## Khắc phục sự cố

- Không đánh dấu đạt khi chưa chạy test chỉ vì code build được.
- Dùng file mẫu tổng hợp thay vì hồ sơ cá nhân.

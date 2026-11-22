---
title: "Cấu hình CORS và truyền file bằng Presigned URL"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.5.2 </b> "
---

{{% notice info %}}
Cấu hình CORS của bucket đang triển khai được chụp bên dưới. Luồng upload/download bằng presigned URL từ frontend được ghi nhận ở mục 5.8.2.
{{% /notice %}}

## Mục tiêu

Chỉ cho phép frontend origin cần thiết upload/download trực tiếp từ trình duyệt, đồng thời giữ S3 riêng tư.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Khai báo origin chính xác trong AllowedFrontendOrigins

Khai báo origin chính xác trong `AllowedFrontendOrigins` của SAM stack: Vite local và `https://<amplify-domain>`. Cập nhật stack sau khi có domain; template áp dụng danh sách này cho HTTP API và CORS của bucket.

### Bước 2: Trong S3 Console → bucket → Permissions → CORS, đối chiếu

Trong S3 Console → bucket → Permissions → CORS, đối chiếu GET/HEAD/PUT và các header signing/content type cần thiết. Không dùng `*` cho origin production.

### Bước 3: Luồng upload: frontend gửi thông tin file tới Lambda

Luồng upload: frontend gửi thông tin file tới Lambda; Lambda xác minh Cognito caller, tạo key server-side và trả PUT presigned URL ngắn hạn. Browser PUT thẳng lên S3 với Content-Type đã ký, rồi gọi API complete; Lambda dùng HeadObject xác minh trước khi ghi metadata DynamoDB.

### Bước 4: Luồng download: API xác minh owner hoặc share còn hiệu

Luồng download: API xác minh owner hoặc share còn hiệu lực rồi trả GET presigned URL ngắn hạn. Không log hoặc chia sẻ URL.

## Minh chứng

Cấu hình CORS S3 cho phép `GET`, `HEAD`, `PUT` cùng các header authorization, content type và signing cần thiết. Parameter `AllowedFrontendOrigins` đang triển khai, gồm origin Amplify, được chụp ở mục 5.9.2. Kết quả upload/download được ghi nhận tại mục 5.8.2.

![Ảnh 5.5.2: Cấu hình CORS S3 đã lưu](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.5-Amazon-S3/5.5.2-s3-cors-config.png)

## Kết quả mong đợi

Trình duyệt truyền được file thử nghiệm qua presigned URL mà không public bucket.

## Khắc phục sự cố

- Khi lỗi, kiểm tra origin, method, header, thời hạn URL và content type đã ký.
- CORS không phải phân quyền; backend vẫn phải xác minh quyền.

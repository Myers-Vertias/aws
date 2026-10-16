---
title: "Cấu hình lưu trữ Amazon S3 riêng tư"
date: 2026-10-16
weight: 5
chapter: false
pre: " <b> 5.7.5 </b> "
---

## Mục tiêu

Tạo bucket riêng tư để lưu object tài liệu. DynamoDB lưu metadata và S3 object key, không lưu bytes của file.

## Bước 1 — Tạo bucket

1. Mở [Amazon S3 console](https://console.aws.amazon.com/s3/).
2. Chọn **Create bucket**.
3. Đặt tên development duy nhất toàn cầu, ví dụ **unicloud-dms-dev-your-unique-suffix**. Tên bucket là duy nhất trên toàn cầu; thay ví dụ bằng tên còn khả dụng với tài khoản.
4. Chọn cùng Region đã chọn ở mục 5.7.1.
5. Giữ bật **Block all public access**.
6. Giữ bật mã hóa mặc định. Với workshop này, mã hóa do S3 quản lý là đủ, trừ khi thiết kế đã duyệt yêu cầu KMS key do khách hàng quản lý.
7. Chỉ bật versioning nếu implementation và hướng dẫn cleanup đã chuẩn bị xử lý các phiên bản object.
8. Tạo bucket.

**[Vị trí ảnh 5.7.5-01 — Cài đặt bucket riêng tư]**  
Chụp Region của bucket, cài đặt Block Public Access và trạng thái mã hóa.

## Bước 2 — Cấu hình CORS cho upload trực tiếp

Trình duyệt upload file trực tiếp lên S3 bằng presigned PUT URL có thời hạn ngắn. CORS của S3 phải cho phép các origin frontend thực sự sử dụng, chẳng hạn origin Vite cục bộ và domain Amplify cuối cùng.

Trong bucket, mở **Permissions → Cross-origin resource sharing (CORS)** và thêm cấu hình tương tự bên dưới. Thay origin Amplify mẫu bằng origin triển khai thật trước khi lưu; không để origin placeholder trong cấu hình cuối.

~~~json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "HEAD"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://REPLACE-WITH-YOUR-AMPLIFY-DOMAIN"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 300
  }
]
~~~

Dùng danh sách origin hẹp nhất có thể. CORS kiểm soát origin trình duyệt được phép đọc response cross-origin; nó **không** thay thế bảo mật S3 hay kiểm tra phân quyền backend.

**[Vị trí ảnh 5.7.5-02 — Cấu hình CORS đã lưu]**  
Chụp các rule CORS đã lưu và che thông tin tài khoản không liên quan.

## Bước 3 — Quy ước object key an toàn

Dùng key được server tạo để tách tài liệu theo owner và document ID, ví dụ:

~~~text
users/<cognito-sub>/documents/<document-id>/<safe-file-name>
~~~

Lambda phải tạo hoặc xác minh key thực tế. Không cho frontend tự chọn key tùy ý trong bucket. Tránh đưa email thô vào object key.

## Bước 4 — Chuẩn bị luồng presigned URL

- Lambda kiểm tra người dùng đã xác thực, tên file, dung lượng và content type được phép trước khi cấp PUT URL ngắn hạn.
- Frontend gửi file lên URL với content type khớp giá trị dùng khi ký URL.
- Endpoint hoàn tất xác minh object tồn tại trước khi hoàn thiện metadata.
- Endpoint download kiểm tra quyền Owner/share trước khi tạo GET URL ngắn hạn.
- Không đặt bucket hay object đã upload ở chế độ public.

## Kết quả mong đợi

Bucket riêng tư đã được tạo ở Region đã chọn; chặn public access và mã hóa được bật; CORS chỉ chứa các origin frontend cần thiết.

## Khắc phục sự cố

- **Lỗi CORS trên trình duyệt:** kiểm tra origin chính xác, method được phép và request header.
- **Signature mismatch:** đảm bảo PUT request dùng content type và URL Lambda trả về, không chỉnh sửa URL.
- **Access denied:** kiểm tra execution role của Lambda và bucket policy; không bật public access để né lỗi.
- **Tên bucket đã được sử dụng:** thêm suffix ngẫu nhiên mới rồi thử lại.

## Lưu ý cleanup

Nếu bật versioning, khi xóa bucket phải xóa cả các object version và delete marker, không chỉ object hiện tại. Làm theo mục cleanup thay vì cho rằng danh sách object hiện tại trống đồng nghĩa bucket hoàn toàn rỗng.

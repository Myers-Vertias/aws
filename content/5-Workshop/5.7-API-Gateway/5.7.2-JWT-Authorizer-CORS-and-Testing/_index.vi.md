---
title: "Cấu hình JWT Authorizer, CORS và Kiểm thử"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

{{% notice info %}}
Cognito JWT authorizer đã triển khai, cấu hình CORS API Gateway, từ chối request chưa xác thực, cho phép truy vấn tài liệu đã xác thực và chặn truy cập tài liệu khác tài khoản đã được xác minh.
{{% /notice %}}

## Mục tiêu

Yêu cầu token Cognito hợp lệ và kiểm tra CORS nhưng không nhầm CORS với phân quyền.

## Các bước thực hiện

1. Xem `Auth` trong `backend/template.yaml`: JWT issuer được tạo từ Region/User Pool, audience là Cognito web App Client ID. SAM áp dụng authorizer mặc định cho route proxy.
2. Trong API Gateway → API → Authorization/Routes, xác nhận document proxy yêu cầu Cognito JWT. Chỉ `GET /health` và `OPTIONS /{proxy+}` được miễn auth theo thiết kế.
3. Giữ `AllowedFrontendOrigins` giới hạn ở Vite local và domain Amplify thực tế; template cho phép header Authorization/Content-Type và các method ứng dụng dùng.
4. Thay đổi cấu hình ở template rồi deploy bằng SAM; xem route/auth/CORS sau khi stack cập nhật. Không bật CORS credentials nếu frontend không dùng cookie.

## Vị trí ảnh chụp màn hình

![Ảnh 5.7.2: API health endpoint trên AWS trả về status ok](/images/5-Workshop/5.7-API-Gateway/5.7.2-api-health-check.png)

Route `ANY /{proxy+}` đã gắn Cognito JWT authorizer. `GET /health` và `OPTIONS /{proxy+}` không yêu cầu đăng nhập để phục vụ health check và CORS preflight.

![Ảnh 5.7.2: JWT authorizer gắn vào document proxy route](/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-routes.png)

Authorizer lấy bearer token từ header `Authorization`, dùng Cognito User Pool issuer đang triển khai và kiểm tra audience là web app client.

![Ảnh 5.7.2: Issuer và audience của Cognito JWT authorizer](/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-details.png)

API Gateway CORS cho phép các Vite origin local và Amplify origin production, các HTTP method/header cần thiết `authorization`/`content-type`, max-age 600 giây và tắt credentials.

![Ảnh 5.7.2: Cấu hình CORS API Gateway cho origin local và Amplify](/images/5-Workshop/5.7-API-Gateway/5.7.2-api-cors-config.png)

Parameter `AllowedFrontendOrigins` đang triển khai cũng được chụp ở mục 5.9.2.

Request `GET /documents` không xác thực trả về `{"message":"Unauthorized"}`.

![Ảnh 5.7.2: API từ chối request GET documents không có xác thực](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request.png)

Network panel xác nhận request không xác thực trả về HTTP `401`.

![Ảnh 5.7.2: Network panel hiển thị HTTP 401 cho request documents không xác thực](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

Frontend local sau khi đăng nhập gửi hai request `GET /documents?view=owned&status=active`; cả hai đều trả về HTTP `200` và danh sách hiển thị `Konoka.png`.

![Ảnh 5.7.2: Request lấy danh sách tài liệu đã xác thực trả về HTTP 200](/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-documents-200.png)

Đăng nhập bằng tài khoản Cognito còn lại và gửi request trực tiếp tới tài liệu riêng chưa chia sẻ của chủ sở hữu, API trả HTTP `404`; giao diện hiển thị “Document not found.” Network ghi nhận hai lần request, cả hai đều có status `404`. API cố ý trả not-found cho tài liệu ngoài quyền truy cập để không xác nhận tài liệu đó có tồn tại hay không.

![Ảnh 5.7.2: Tài khoản nhận đã đăng nhập nhận HTTP 404 khi gọi tài liệu chưa được chia sẻ](/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-unshared-document-404.png)

## Kết quả mong đợi

Route được bảo vệ từ chối token thiếu/không hợp lệ và Lambda chặn truy cập tài nguyên trái quyền.

## Khắc phục sự cố

- 401 thường liên quan xác thực; 403 thường liên quan phân quyền—hãy kiểm tra response thật.
- CORS cho phép browser gọi cross-origin nhưng không cấp quyền vào tài liệu.

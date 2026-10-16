---
title: "Cấu hình Amazon API Gateway"
date: 2026-10-16
weight: 7
chapter: false
pre: " <b> 5.7.7 </b> "
---

## Mục tiêu

Cung cấp các Lambda handler qua HTTP API có bảo vệ. Workshop này dùng API Gateway **HTTP API với JWT authorizer** để cấu hình các REST-style route gọn hơn. Nếu thiết kế cuối yêu cầu REST API, cần cập nhật đồng bộ hướng dẫn này và cấu hình frontend.

## Bước 1 — Tạo HTTP API

1. Mở [Amazon API Gateway console](https://console.aws.amazon.com/apigateway/).
2. Tạo **HTTP API** và đặt tên development, ví dụ **unicloud-dms-dev-api**.
3. Thêm Lambda integration và route theo mục 5.7.2.
4. Dùng cùng Region với Lambda function và Cognito User Pool.
5. Tạo stage development và bật tự động deploy nếu phù hợp với quy trình đã chọn.
6. Ghi lại invoke URL để sử dụng làm API base URL của frontend.

**[Vị trí ảnh 5.7.7-01 — API route và Lambda integration]**  
Chụp danh sách route và integration đích.

## Bước 2 — Cấu hình JWT authorizer

Tạo JWT authorizer cho Cognito User Pool:

- **Issuer URL:** https://cognito-idp.<region>.amazonaws.com/<user-pool-id>
- **Audience:** Cognito App Client ID mà ứng dụng sử dụng.
- Gắn authorizer vào mọi route truy cập tài liệu người dùng hoặc thay đổi dữ liệu ứng dụng.

Thay placeholder bằng Region và User Pool ID thật. Xác minh loại token frontend gửi và đảm bảo authorizer chấp nhận token đó. Frontend không được gửi token từ pool hoặc client khác.

Giữ route public (nếu có) ở mức tối thiểu. Không dựa vào route guard frontend để bảo vệ API.

**[Vị trí ảnh 5.7.7-02 — JWT authorizer và route được bảo vệ]**  
Chụp issuer, audience và cài đặt route được bảo vệ, không hiển thị JWT thật.

## Bước 3 — Cấu hình CORS

Chỉ cho phép các origin frontend cần thiết, bao gồm origin local và domain Amplify cuối cùng. Cấu hình method và header cần dùng, gồm **Authorization** và **Content-Type**. Tránh wildcard origin khi response có thông tin nhạy cảm hoặc dùng credentials.

CORS là chính sách của trình duyệt, không phải cơ chế phân quyền. Lambda vẫn phải xác minh ownership và share permission cho thao tác tài liệu.

## Bước 4 — Kiểm thử API

Dùng công cụ test của API Gateway khi được hỗ trợ hoặc REST client:

1. Gửi request cần bảo vệ mà không có token; request phải bị từ chối.
2. Gửi lại request với token hợp lệ; request phải tới Lambda.
3. Thử thao tác tài liệu bằng tài khoản không phải owner và không có quyền viewer; request phải bị từ chối.
4. Kiểm tra response dùng JSON nhất quán và HTTP status có ý nghĩa.
5. Xác nhận browser preflight request thành công từ frontend origin được phép.

**[Vị trí ảnh 5.7.7-03 — Kết quả request không xác thực và đã xác thực]**  
Chụp cả hai kết quả bằng dữ liệu thử nghiệm. Không hiển thị token đầy đủ hay presigned URL.

## Kết quả mong đợi

API có invoke URL được ghi nhận, route kết nối đúng handler và các route cần bảo vệ yêu cầu token được JWT authorizer chấp nhận.

## Khắc phục sự cố

- **401:** thiếu token, token hết hạn, phát hành từ pool khác hoặc bị authorizer từ chối.
- **403:** xác thực có thể đã thành công nhưng kiểm tra quyền nghiệp vụ từ chối truy cập.
- **404 hoặc thiếu route:** kiểm tra stage, method, route key và invoke URL.
- **Lỗi CORS:** so sánh origin và preflight header của trình duyệt với cài đặt CORS.
- **500:** kiểm tra CloudWatch log của Lambda tương ứng và cấu hình integration.

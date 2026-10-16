---
title: "Giám sát và khắc phục sự cố"
date: 2026-10-16
weight: 11
chapter: false
pre: " <b> 5.7.11 </b> "
---

## Mục tiêu

Dùng log dịch vụ và kiểm tra cấu hình để chẩn đoán lỗi mà không làm lộ secret hoặc cấp quyền không cần thiết.

## Bước 1 — Xem Lambda log

1. Mở [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch/).
2. Vào **Logs → Log groups**.
3. Mở log group của function đang kiểm thử, thường có tiền tố **/aws/lambda/**.
4. Tìm invocation khớp thời điểm test và request ID.
5. Xem lỗi/stack trace rồi đối chiếu với request API Gateway.

**[Vị trí ảnh 5.7.11-01 — Lambda CloudWatch log của request thử nghiệm]**  
Chụp log lỗi hữu ích hoặc lần chạy thành công, loại bỏ token, presigned URL, dữ liệu cá nhân và nội dung file.

## Bước 2 — Bật và xem API access log khi phù hợp

Nếu đã cấu hình access logging cho API Gateway, dùng định dạng có cấu trúc gồm request ID, route, status code và latency. Không ghi Authorization header, cookie, request body đầy đủ có dữ liệu cá nhân hay signed URL.

Đặt thời gian lưu log hợp lý cho project sinh viên. Lưu lâu hơn có thể tăng chi phí lưu trữ. Không để debug log chi tiết bật trong production.

## Bước 3 — Chẩn đoán theo thứ tự

Khi thao tác trên trình duyệt bị lỗi, kiểm tra lần lượt:

1. Browser Network panel: request URL, method, status, CORS/preflight và response code.
2. API Gateway: route, kết quả authorizer, integration status và request ID.
3. Lambda: exception, biến môi trường, timeout và execution role.
4. DynamoDB: tên bảng/index, key, cấu trúc item và quyền IAM.
5. S3: object key, Region bucket, CORS, header đã ký và quyền role.
6. Cognito: User Pool, App Client, token issuer, expiration và cấu hình client.

Dùng dữ liệu thử nghiệm tổng hợp. Không dán JWT hoặc presigned URL đầy đủ vào ticket, báo cáo hay ảnh chụp.

**[Vị trí ảnh 5.7.11-02 — Bằng chứng chẩn đoán end-to-end đã làm sạch]**  
Chụp request ID và log liên quan sau khi che giá trị nhạy cảm.

## Bước 4 — Kiểm tra giám sát và chi phí

Khi lab đang chạy, xem log group CloudWatch và trang billing. Chỉ giữ metric/log cần để debug và trình bày workshop. CloudWatch alarm, dashboard và log ingestion có thể phát sinh chi phí tùy mức sử dụng.

## Kết quả mong đợi

Người học có thể lần theo một request lỗi từ trình duyệt sang API Gateway và Lambda, rồi đến DynamoDB, S3 hoặc Cognito khi phù hợp, mà không làm lộ credentials.

## Khắc phục sự cố

- **Không thấy log group:** gọi function và xác minh execution role được ghi log.
- **Log thiếu ngữ cảnh:** thêm request ID và field có cấu trúc ngắn gọn nhưng tuyệt đối không ghi secret.
- **Request timeout:** kiểm tra Lambda timeout, lời gọi downstream và xử lý payload lớn.
- **Lỗi phân quyền lặp lại:** so sánh issuer/audience của token và các bước kiểm tra owner/share trong Lambda.

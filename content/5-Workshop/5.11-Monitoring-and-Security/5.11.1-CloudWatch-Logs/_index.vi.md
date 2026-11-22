---
title: "CloudWatch Logs, Metrics và Alarms"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.11.1 </b> "
---

{{% notice info %}}
Log group Lambda `dms-dev-documents` và `dms-dev-health` tại `ap-southeast-1` hiện đặt retention **Never expire**. API Gateway HTTP API đã ghi access log vào log group riêng với retention 7 ngày. Chưa thiết lập liên kết trực tiếp giữa request ID của API Gateway và Lambda runtime log.
{{% /notice %}}

## Mục tiêu

Quan sát API access logs, Lambda runtime logs/metrics và cảnh báo lỗi mà không làm lộ secret.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Trong AWS Console chọn ap-southeast-1 → CloudWatch → Logs

Trong AWS Console chọn `ap-southeast-1` → CloudWatch → Logs → Log groups; mở `/aws/lambda/<stack>-documents` hoặc `/aws/lambda/<stack>-health`.

### Bước 2: Chọn log stream gần thời điểm thao tác và đọc START, END

Chọn log stream gần thời điểm thao tác và đọc `START`, `END`, `REPORT`; dùng thời gian/function request ID để tìm lượt chạy. Không coi Lambda request ID là API Gateway request ID.

### Bước 3: Đặt retention phù hợp ở tab Retention để log không lưu vô

Đặt retention phù hợp ở tab Retention để log không lưu vô thời hạn. Lambda tự xuất metrics `Invocations`, `Errors`, `Duration` trong CloudWatch → Metrics → AWS/Lambda ([tài liệu metrics Lambda](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html)).

### Bước 4: Để có email khi Lambda lỗi, mở CloudWatch → Alarms →

Để có email khi Lambda lỗi, mở CloudWatch → Alarms → Create alarm → chọn `AWS/Lambda` / FunctionName / `Errors`; đặt thống kê Sum, period phù hợp, ngưỡng `>= 1`, rồi chọn hoặc tạo SNS email topic. Người nhận phải xác nhận subscription email trước khi nhận thông báo ([hướng dẫn SNS email alarm](https://docs.aws.amazon.com/sns/latest/dg/sns-monitoring-using-cloudwatch.html)). Alarm có thể phát sinh chi phí.

### Bước 5: Không log authorization header, JWT đầy đủ, presigned

Không log authorization header, JWT đầy đủ, presigned URL, nội dung file hay dữ liệu cá nhân nhạy cảm.

### Bước 6: Với HTTP API, tạo CloudWatch log group riêng, ví dụ

Với HTTP API, tạo CloudWatch log group riêng, ví dụ `/aws/apigateway/<stack>-access`, và đặt retention. Trong API Gateway mở API → Monitor → Logging, chọn stage `$default` → Select → Edit; bật Access logging, nhập ARN của log group và chọn định dạng JSON có `$context.requestId`, `$context.routeKey`, `$context.httpMethod` và `$context.status`. Lưu cấu hình. Sau khi API nhận request, mở log group để xem access log. HTTP API access log ID và Lambda runtime request ID là hai ID khác nhau, không thể coi là cùng một ID.

## Vị trí ảnh chụp màn hình

![Ảnh 5.11.1: Log group CloudWatch của Lambda documents và health, có hiển thị retention](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-log-groups.png)

Log stream của function documents ghi nhận các bản ghi runtime Lambda `START`, `END` và `REPORT`, kèm Lambda request ID cùng thời lượng.

![Ảnh 5.11.1: Các bản ghi runtime Lambda START, END và REPORT trong log stream của function documents](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-runtime-events.png)

API Gateway stage `$default` đã ghi access log dạng JSON vào `/aws/apigateway/dms-dev-access` với retention 7 ngày. Bản ghi CORS preflight `OPTIONS /{proxy+}` có status `204`, request ID và thời gian request; source IP đã được che trong ảnh. Lambda runtime logs và API Gateway access logs được ghi ở các log group riêng.

![Ảnh 5.11.1: CloudWatch ghi access log JSON của API Gateway, gồm method OPTIONS, routeKey và status 204](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-api-access-log-events.png)

Alarm `dms-dev-documents-errors` theo dõi metric `AWS/Lambda Errors` của function `dms-dev-documents`, thống kê Sum trong mỗi 5 phút và chuyển sang ALARM khi có ít nhất một lỗi (`1 of 1 datapoint`). Trạng thái tại thời điểm chụp là **OK**; chưa có lỗi vượt ngưỡng trong datapoint đang hiển thị. Email nhận cảnh báo đã xác nhận đăng ký thành công với SNS topic `dms-dev-lambda-errors`.

![Ảnh 5.11.1: CloudWatch alarm dms-dev-documents-errors đang ở trạng thái OK với ngưỡng Errors >= 1 trong 5 phút](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-errors-alarm-ok.png)

![Ảnh 5.11.1: Xác nhận đăng ký email thành công với Amazon SNS topic dms-dev-lambda-errors](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-sns-subscription-confirmed.png)

## Kết quả mong đợi

Ảnh chụp xác nhận Lambda runtime logs, API Gateway access logs, alarm `dms-dev-documents-errors` ở trạng thái OK, SNS email subscription đã xác nhận và AWS Budget email alert.

## Khắc phục sự cố

- Nếu không có log, xác minh function đã được gọi và role được phép ghi log.
- Lưu/tiếp nhận log có thể tính phí; đặt retention phù hợp.

---
title: "CloudWatch Logs, Metrics và Alarms"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.11.1 </b> "
---

{{% notice info %}}
Log group của Lambda `dms-dev-documents` và `dms-dev-health` tồn tại ở `ap-southeast-1`. Retention hiện là **Never expire**. Chưa đối chiếu được một log event cụ thể với request.
{{% /notice %}}

## Mục tiêu

Đối chiếu API request với lần chạy Lambda mà không làm lộ secret.

## Các bước thực hiện

1. Trong AWS Console chọn `ap-southeast-1` → CloudWatch → Logs → Log groups; mở `/aws/lambda/<stack>-documents` hoặc `/aws/lambda/<stack>-health`.
2. Chọn log stream gần thời điểm thao tác và đọc `START`, `END`, `REPORT`; dùng thời gian/function request ID để tìm lượt chạy. Không coi Lambda request ID là API Gateway request ID.
3. Đặt retention phù hợp ở tab Retention để log không lưu vô thời hạn. Lambda tự xuất metrics `Invocations`, `Errors`, `Duration` trong CloudWatch → Metrics → AWS/Lambda ([tài liệu metrics Lambda](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html)).
4. Để có email khi Lambda lỗi, mở CloudWatch → Alarms → Create alarm → chọn `AWS/Lambda` / FunctionName / `Errors`; đặt thống kê Sum, period phù hợp, ngưỡng `>= 1`, rồi chọn hoặc tạo SNS email topic. Người nhận phải xác nhận subscription email trước khi nhận thông báo ([hướng dẫn SNS email alarm](https://docs.aws.amazon.com/sns/latest/dg/sns-monitoring-using-cloudwatch.html)). Alarm có thể phát sinh chi phí.
5. Không log authorization header, JWT đầy đủ, presigned URL, nội dung file hay dữ liệu cá nhân nhạy cảm.

## Vị trí ảnh chụp màn hình

![Ảnh 5.11.1: Log group CloudWatch của Lambda documents và health, có hiển thị retention](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-log-groups.png)

Log stream mới nhất của function documents có các bản ghi runtime Lambda `START`, `END` và `REPORT`, kèm Lambda request ID cùng thời lượng. Các dòng này không có API route hoặc HTTP status nên chưa thể gắn chắc chắn với một browser request cụ thể.

![Ảnh 5.11.1: Các bản ghi runtime Lambda START, END và REPORT trong log stream của function documents](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-runtime-events.png)

Handler hiện ghi log cho lỗi không dự kiến nhưng chưa ghi route/status của từng API request đã được xử lý; vì vậy request 404 trước đó chưa thể đối chiếu với application log theo route.

## Kết quả mong đợi

Người học xem được Lambda logs/metrics và nhận cảnh báo khi metric vượt ngưỡng đã chọn. Môi trường đã ghi nhận hiện có Lambda runtime log và budget email alert; chưa ghi nhận CloudWatch metric alarm hoặc API Gateway access log.

## Khắc phục sự cố

- Nếu không có log, xác minh function đã được gọi và role được phép ghi log.
- Lưu/tiếp nhận log có thể tính phí; đặt retention phù hợp.

---
title: "Kiểm tra CloudWatch Logs"
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

1. Mở CloudWatch Logs và tìm log group `/aws/lambda/...` của function đang kiểm thử.
2. Gọi function bằng dữ liệu thử nghiệm tổng hợp rồi tìm log theo timestamp/request ID.
3. Đối chiếu kết quả Lambda với response API Gateway. Nếu bật, dùng log có cấu trúc gồm request ID, route, status và latency.
4. Không log authorization header, JWT đầy đủ, presigned URL, nội dung file hay dữ liệu cá nhân nhạy cảm.

## Vị trí ảnh chụp màn hình

![Ảnh 5.11.1: Log group CloudWatch của Lambda documents và health, có hiển thị retention](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-log-groups.png)

Log stream mới nhất của function documents có các bản ghi runtime Lambda `START`, `END` và `REPORT`, kèm Lambda request ID cùng thời lượng. Các dòng này không có API route hoặc HTTP status nên chưa thể gắn chắc chắn với một browser request cụ thể.

![Ảnh 5.11.1: Các bản ghi runtime Lambda START, END và REPORT trong log stream của function documents](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-runtime-events.png)

Handler hiện ghi log cho lỗi không dự kiến nhưng chưa ghi route/status của từng API request đã được xử lý; vì vậy request 404 trước đó chưa thể đối chiếu với application log theo route.

## Kết quả mong đợi

Người học truy vết được thao tác tới backend log tương ứng.

## Khắc phục sự cố

- Nếu không có log, xác minh function đã được gọi và role được phép ghi log.
- Lưu/tiếp nhận log có thể tính phí; đặt retention phù hợp.

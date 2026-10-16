---
title: "Kiểm tra CloudWatch Logs"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.11.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Đối chiếu API request với lần chạy Lambda mà không làm lộ secret.

## Các bước thực hiện

1. Mở CloudWatch Logs và tìm log group `/aws/lambda/...` của function đang kiểm thử.
2. Gọi function bằng dữ liệu thử nghiệm tổng hợp rồi tìm log theo timestamp/request ID.
3. Đối chiếu kết quả Lambda với response API Gateway. Nếu bật, dùng log có cấu trúc gồm request ID, route, status và latency.
4. Không log authorization header, JWT đầy đủ, presigned URL, nội dung file hay dữ liệu cá nhân nhạy cảm.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.11.1 — CloudWatch event đã làm sạch, đối chiếu được với request thử nghiệm.]**

## Kết quả mong đợi

Người học truy vết được thao tác tới backend log tương ứng.

## Khắc phục sự cố

- Nếu không có log, xác minh function đã được gọi và role được phép ghi log.
- Lưu/tiếp nhận log có thể tính phí; đặt retention phù hợp.

---
title: "Chuẩn bị môi trường phát triển"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.1.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Kiểm tra frontend hiện tại và ghi nhận trạng thái ban đầu trước khi kết nối AWS.

## Các bước thực hiện

1. Mở repository UniCloud DMS và xác nhận có package.json cùng package-lock.json.
2. Dùng Node.js LTS còn được hỗ trợ. Chạy `npm ci`, `npm run lint` và `npm run build` tại thư mục gốc; ghi lại warning/lỗi có sẵn.
3. Chạy `npm run dev`, mở URL Vite in ra và xác nhận các trang frontend mock vẫn hoạt động trước khi thay service.
4. Tạo Git branch hoặc commit sao lưu trước khi tích hợp backend. Không đưa credentials hay token vào source control.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.1.1 — Frontend chạy cục bộ và terminal hiển thị kết quả lint/build ban đầu.]**

## Kết quả mong đợi

Frontend chạy được trên máy và trạng thái lint/build ban đầu đã được ghi nhận.

## Khắc phục sự cố

- Nếu cài npm thất bại, kiểm tra phiên bản Node.js và thư mục đang làm việc.
- Dùng đúng URL và cổng được Vite in ra.

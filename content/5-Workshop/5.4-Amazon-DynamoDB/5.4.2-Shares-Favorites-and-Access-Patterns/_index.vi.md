---
title: "Tạo bảng Shares và Favorites"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.4.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Lưu share và favorite dưới dạng quan hệ riêng từng người dùng, rồi xác minh đường truy vấn.

## Các bước thực hiện

1. Tạo Shares với partition key `documentId`, sort key `recipientSub`; thêm recipient GSI cho mục Được chia sẻ với tôi.
2. Tạo Favorites với partition key `userSub`, sort key `documentId`.
3. Dùng bản ghi tổng hợp để kiểm tra người dùng chỉ query favorite/share của mình.
4. Ghi tên bảng/index cuối vào ghi chú cấu hình API và Lambda. Chỉ tạo index phục vụ truy vấn thực tế.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.4.2 — Khóa bảng Shares/Favorites và recipient index với ví dụ tổng hợp.]**

## Kết quả mong đợi

Bảng quan hệ hỗ trợ các màn hình chia sẻ và yêu thích.

## Khắc phục sự cố

- Index thừa có thể tăng chi phí lưu/ghi; cần gắn mỗi index với yêu cầu truy vấn.
- Không dùng email thay Cognito sub trong phân quyền.

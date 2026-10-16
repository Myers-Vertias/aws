---
title: "Cấu hình JWT Authorizer, CORS và Kiểm thử"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Yêu cầu token Cognito hợp lệ và kiểm tra CORS nhưng không nhầm CORS với phân quyền.

## Các bước thực hiện

1. Cấu hình JWT authorizer bằng Cognito issuer URL và app client ID làm audience.
2. Gắn authorizer cho mọi route đọc hoặc thay đổi tài liệu người dùng.
3. Chỉ cho phép frontend origin local/deployed, method và header Authorization/Content-Type cần thiết.
4. Kiểm thử request thiếu token, token hợp lệ và người đã đăng nhập nhưng không có quyền tài liệu. Ghi status code và request ID.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.7.2 — JWT authorizer, cài đặt CORS và ví dụ request bị từ chối/được phép.]**

## Kết quả mong đợi

Route được bảo vệ từ chối token thiếu/không hợp lệ và Lambda chặn truy cập tài nguyên trái quyền.

## Khắc phục sự cố

- 401 thường liên quan xác thực; 403 thường liên quan phân quyền—hãy kiểm tra response thật.
- CORS cho phép browser gọi cross-origin nhưng không cấp quyền vào tài liệu.

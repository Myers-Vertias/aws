---
title: "Kết nối Repository Frontend và Cấu hình Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Kết nối Amplify tới repository chứa UniCloud DMS, không phải repository tài liệu workshop.

## Các bước thực hiện

1. Kết nối Git provider và chọn repository frontend có package.json cùng branch cần triển khai.
2. Cấu hình build dùng lockfile của repository; với npm thường là `npm ci` rồi `npm run build`.
3. Đặt thư mục artifact Vite là `dist` và cấu hình SPA rewrite cho route React Router.
4. Xem lại build setting trước khi lưu.

## Minh chứng triển khai

Nhánh production `main` đã deploy trên Amplify; trạng thái nhánh và frontend được chụp ở mục 5.9.2. Không lưu màn hình chọn repository và build settings ban đầu.

## Kết quả mong đợi

Nhánh ứng dụng `main` đã deploy và phục vụ frontend. Không chụp riêng chi tiết repository/build settings.

## Khắc phục sự cố

- Không chọn nhầm repository tài liệu workshop.
- Với monorepo, đặt đúng thư mục gốc của ứng dụng.

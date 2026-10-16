---
title: "Kết nối Repository Frontend và Cấu hình Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Kết nối Amplify tới repository chứa UniCloud DMS, không phải repository tài liệu workshop.

## Các bước thực hiện

1. Kết nối Git provider và chọn repository frontend có package.json cùng branch cần triển khai.
2. Cấu hình build dùng lockfile của repository; với npm thường là `npm ci` rồi `npm run build`.
3. Đặt thư mục artifact Vite là `dist` và cấu hình SPA rewrite cho route React Router.
4. Xem lại build setting trước khi lưu.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.9.1 — Lựa chọn repository/branch Amplify và build setting frontend.]**

## Kết quả mong đợi

Amplify đã kết nối đúng mã nguồn ứng dụng và nhận diện đầu ra build.

## Khắc phục sự cố

- Không chọn nhầm repository tài liệu workshop.
- Với monorepo, đặt đúng thư mục gốc của ứng dụng.

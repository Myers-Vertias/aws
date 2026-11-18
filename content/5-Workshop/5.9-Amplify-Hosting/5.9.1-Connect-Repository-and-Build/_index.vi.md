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

1. Mở AWS Amplify → Create new app → Host web app, kết nối GitHub và cấp quyền chọn repository. Chọn repository ứng dụng `serverless-document-management`, không chọn repository tài liệu workshop.
2. Chọn branch cần publish (project dùng `main`), xác nhận app root là thư mục có `package.json` và `package-lock.json`.
3. Dùng build command `npm ci && npm run build`, artifact directory `dist`; thêm SPA rewrite để route React Router trả về `index.html` khi refresh.
4. Lưu cấu hình và để Amplify tạo build đầu tiên. Khi cần environment variables, nhập các biến `VITE_*` trong Amplify trước khi redeploy theo mục 5.9.2.

## Minh chứng triển khai

Nhánh production `main` đã deploy trên Amplify; trạng thái nhánh và frontend được chụp ở mục 5.9.2. Không lưu màn hình chọn repository và build settings ban đầu.

## Kết quả mong đợi

Nhánh ứng dụng `main` đã deploy và phục vụ frontend. Không chụp riêng chi tiết repository/build settings.

## Khắc phục sự cố

- Không chọn nhầm repository tài liệu workshop.
- Với monorepo, đặt đúng thư mục gốc của ứng dụng.

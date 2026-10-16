---
title: "Thiết kế mô hình dữ liệu và API contract"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.2.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Xác định khóa dữ liệu, endpoint và quyền Owner/Viewer để frontend/backend dùng chung một contract.

## Các bước thực hiện

1. Dùng Cognito `sub` làm ID người dùng nội bộ ổn định. Email là thuộc tính tài khoản, không phải định danh phân quyền chính.
2. Documents: `documentId`, `ownerSub`, `s3Key`, tên file, loại, dung lượng, category, tags, mô tả, thời gian và `deletedAt` tùy chọn. Query owner index cho Tài liệu của tôi.
3. Shares: partition key `documentId`, sort key `recipientSub`, thêm recipient index cho Được chia sẻ với tôi. Favorites: `userSub` + `documentId` để yêu thích riêng theo người dùng.
4. Xác định route CRUD documents, upload/download URL, favorites, Thùng rác/khôi phục/xóa vĩnh viễn, danh sách share, tạo/thu hồi share.
5. Owner được quản lý tài liệu; Viewer chỉ xem/tải tài liệu đang được chia sẻ. Lambda phải kiểm tra quyền trên mọi thao tác được bảo vệ.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.2.2 — Mô hình dữ liệu, danh sách endpoint và ma trận quyền Owner/Viewer đã được xem xét.]**

## Kết quả mong đợi

Khóa bảng, cách truy vấn, API route và quy tắc phân quyền được ghi nhận trước khi tạo resource.

## Khắc phục sự cố

- Dùng mã độc lập ngôn ngữ như `finance`, `active`; chỉ dịch nhãn UI.
- Không xem route guard hoặc nút ẩn ở frontend là cơ chế bảo mật.

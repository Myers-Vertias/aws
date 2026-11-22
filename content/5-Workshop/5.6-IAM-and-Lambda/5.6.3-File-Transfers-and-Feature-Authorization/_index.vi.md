---
title: "Triển khai Truyền File và Phân quyền Feature"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.6.3 </b> "
---

{{% notice info %}}
Mục này dùng lại minh chứng trình duyệt và API ở các mục 5.7.2, 5.8.2, 5.10.2 và 5.10.3 để tránh chụp lặp ảnh.
{{% /notice %}}

## Mục tiêu

Triển khai upload/download presigned, Favorites, Tags, Thùng rác và sharing kèm kiểm tra phía server.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Tạo S3 key dựa trên Cognito sub của người dùng

Trong `backend/src/index.mjs`, tạo key S3 từ Cognito `sub` và ID do server sinh; xác minh file type/size trước khi trả PUT URL. Khi complete, dùng HeadObject rồi mới ghi metadata.

### Bước 2: Với download, share, favorites và trash, xác định caller

Với download, share, favorites và trash, xác định caller từ claims rồi kiểm tra quyền trước khi cấp URL hoặc thay đổi record. Frontend gọi các route trong `src/services/documentService.js`.

### Bước 3: Favorites dùng quan hệ user/document

Favorites dùng quan hệ user/document; Trash là soft-delete bằng `deletedAt`, restore xóa thuộc tính này. Chỉ thêm hard-delete khi đồng bộ xóa object, metadata, share và favorites.

### Bước 4: Share resolve email sang Cognito sub, chỉ owner được

Share resolve email sang Cognito `sub`, chỉ owner được cấp/revoke quyền. Viewer chỉ được xem/tải file đang chia sẻ; URL đã phát hành còn dùng được đến khi hết hạn.

### Bước 5: Sau khi cập nhật Lambda, build/deploy bằng SAM

Sau khi cập nhật Lambda, build/deploy bằng SAM; frontend sử dụng API URL và Cognito IDs từ Outputs, không gọi DynamoDB/S3 bằng credentials ở trình duyệt.

### Mẫu tạo key và URL upload

Key được tạo từ user `sub` và document ID do server sinh, không nhận key từ trình duyệt:

```javascript
const documentId = randomUUID();
const s3Key = `users/${caller.sub}/documents/${documentId}`;
const uploadUrl = await getSignedUrl(s3, new PutObjectCommand({
  Bucket: process.env.DOCUMENTS_BUCKET,
  Key: s3Key,
  ContentType: contentType,
  Metadata: { "document-id": documentId },
}), { expiresIn: 300 });
```

## Hành vi đã xác nhận và minh chứng

- **Upload/download bằng presigned URL:** Kết quả upload S3 thành công, lưu metadata và luồng tải xuống được ghi nhận ở mục 5.8.2.
- **Phân quyền tài nguyên:** Mục 5.7.2 ghi nhận request chưa xác thực nhận `401` và tài khoản thứ hai đã đăng nhập nhận `404` khi mở tài liệu riêng chưa chia sẻ của chủ sở hữu.
- **Chia sẻ:** Mục 5.10.2 ghi nhận tài liệu xuất hiện trong Được chia sẻ với tôi và thao tác tải xuống; mục 5.8.2 cũng ghi nhận lượt tải trực tiếp của tài khoản nhận.
- **Yêu thích, metadata và Thùng rác/khôi phục:** Kết quả được ghi nhận tại mục 5.8.2 và 5.10.3.

Form upload local đã từ chối file vượt giới hạn 20 MB; một request đã xác thực gửi riêng tới `POST /documents/upload-url` cũng xác minh API đang deploy trả HTTP `400` cho file 20 MiB + 1 byte trước khi cấp S3 upload URL. Xem mục 5.10.1.

## Kết quả mong đợi

Feature tài liệu dùng dữ liệu backend và phân quyền được thực thi tại Lambda.

## Khắc phục sự cố

- Viewer không được sửa, chia sẻ tiếp hay xóa tài liệu của người khác.
- Thao tác qua S3/DynamoDB không tự động nguyên tử; cần mô tả phục hồi khi lỗi một phần.

---
title: "Triển khai Truyền File và Phân quyền Feature"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.6.3 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Triển khai upload/download presigned, Favorites, Tags, Thùng rác và sharing kèm kiểm tra phía server.

## Các bước thực hiện

1. Chỉ tạo S3 key do server kiểm soát và PUT URL ngắn hạn sau khi xác minh danh tính, loại file và dung lượng. Xác minh object trước khi chốt metadata.
2. Khi download, kiểm tra caller là owner hoặc viewer còn hiệu lực trước khi cấp GET URL ngắn hạn.
3. Lưu Favorites theo user và document. Giữ mã category/status ổn định và chuẩn hóa tags nhất quán.
4. Thùng rác là xóa mềm qua `deletedAt`; khôi phục xóa trường này. Xóa vĩnh viễn phải phối hợp xóa object S3 và metadata liên quan.
5. Tạo share phải xác minh người nhận, chặn tự chia sẻ/trùng lặp. Revoke chặn truy cập metadata/URL mới. Presigned URL đã cấp trước đó còn hạn đến khi hết thời gian.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.6.3 — Kiểm thử phân quyền upload/download, Favorites, Thùng rác/khôi phục và share/revoke.]**

## Kết quả mong đợi

Feature tài liệu dùng dữ liệu backend và phân quyền được thực thi tại Lambda.

## Khắc phục sự cố

- Viewer không được sửa, chia sẻ tiếp hay xóa tài liệu của người khác.
- Thao tác qua S3/DynamoDB không tự động nguyên tử; cần mô tả phục hồi khi lỗi một phần.

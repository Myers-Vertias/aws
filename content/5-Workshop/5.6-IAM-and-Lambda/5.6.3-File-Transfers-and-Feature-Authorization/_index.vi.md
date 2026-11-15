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

1. Chỉ tạo S3 key do server kiểm soát và PUT URL ngắn hạn sau khi xác minh danh tính, loại file và dung lượng. Xác minh object trước khi chốt metadata.
2. Khi download, kiểm tra caller là owner hoặc viewer còn hiệu lực trước khi cấp GET URL ngắn hạn.
3. Lưu Favorites theo user và document. Giữ mã category/status ổn định và chuẩn hóa tags nhất quán.
4. Thùng rác là xóa mềm qua `deletedAt`; khôi phục xóa trường này. Xóa vĩnh viễn phải phối hợp xóa object S3 và metadata liên quan.
5. Tạo share phải xác minh người nhận, chặn tự chia sẻ/trùng lặp. Revoke chặn truy cập metadata/URL mới. Presigned URL đã cấp trước đó còn hạn đến khi hết thời gian.

## Hành vi đã xác nhận và minh chứng

- **Upload/download bằng presigned URL:** Kết quả upload S3 thành công, lưu metadata và luồng tải xuống được ghi nhận ở mục 5.8.2.
- **Phân quyền tài nguyên:** Mục 5.7.2 ghi nhận request chưa xác thực nhận `401` và tài khoản thứ hai đã đăng nhập nhận `404` khi mở tài liệu riêng chưa chia sẻ của chủ sở hữu.
- **Chia sẻ:** Mục 5.10.2 ghi nhận tài liệu xuất hiện trong Được chia sẻ với tôi và thao tác tải xuống; mục 5.8.2 cũng ghi nhận lượt tải trực tiếp của tài khoản nhận.
- **Yêu thích, metadata và Thùng rác/khôi phục:** Kết quả được ghi nhận tại mục 5.8.2 và 5.10.3.

Chưa xác minh thu hồi chia sẻ, chặn tự chia sẻ/trùng lặp, hoặc việc viewer bị từ chối khi sửa/xóa tài liệu được chia sẻ. Báo cáo không khẳng định các hành vi này.

## Kết quả mong đợi

Feature tài liệu dùng dữ liệu backend và phân quyền được thực thi tại Lambda.

## Khắc phục sự cố

- Viewer không được sửa, chia sẻ tiếp hay xóa tài liệu của người khác.
- Thao tác qua S3/DynamoDB không tự động nguyên tử; cần mô tả phục hồi khi lỗi một phần.

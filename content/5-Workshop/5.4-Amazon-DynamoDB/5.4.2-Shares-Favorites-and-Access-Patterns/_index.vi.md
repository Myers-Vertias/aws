---
title: "Xác minh bảng Shares và Favorites"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.4.2 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Kiểm tra các bảng quan hệ Shares và Favorites do SAM tạo, rồi xác minh access pattern.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Deploy cùng SAM stack ở mục 5.1.3

Deploy cùng SAM stack ở mục 5.1.3; template tạo hai bảng quan hệ riêng, không tạo thủ công bảng trùng.

### Bước 2: Mở CloudFormation → stack → Resources và chọn

Mở CloudFormation → stack → Resources và chọn `SharesTable`, rồi đối chiếu bảng `<stack-name>-shares`: partition key `recipientSub`, sort key `documentId`, capacity On-demand. Trong Indexes xác nhận `OwnerShareIndex` dùng `ownerSub` và `documentIdRecipientSub`.

### Bước 3: Mở FavoritesTable và đối chiếu <stack-name>-favorites

Mở `FavoritesTable` và đối chiếu `<stack-name>-favorites`: partition `userSub`, sort `documentId`, capacity On-demand. Index `DocumentFavoritesIndex` dùng `documentId` và `documentIdUserSub` để truy vấn favorite theo tài liệu.

### Bước 4: Ghi tên bảng/index từ stack outputs/template vào cấu hình

Ghi tên bảng/index từ stack outputs/template vào cấu hình Lambda. Các khóa Cognito dùng `sub`, không dùng email làm khóa phân quyền.

## Vị trí ảnh chụp màn hình

![Ảnh 5.4.2: Bản ghi yêu thích trong bảng dms-dev-favorites](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-favorites-table-records.png)

![Ảnh 5.4.2: Bản ghi chia sẻ trong bảng dms-dev-shares](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-table-records.png)

![Ảnh 5.4.2: Key schema và trạng thái Active của bảng dms-dev-shares](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-table-key-schema.png)

![Ảnh 5.4.2: OwnerShareIndex đang Active trong bảng dms-dev-shares](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-owner-index.png)

![Ảnh 5.4.2: Key schema và trạng thái Active của bảng dms-dev-favorites](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-favorites-table-key-schema.png)

Bảng Shares đang triển khai có trạng thái Active và dùng capacity On-demand. Khóa chính gồm `recipientSub` (partition key) và `documentId` (sort key). `OwnerShareIndex` đang Active, dùng `ownerSub` làm partition key và `documentIdRecipientSub` làm sort key. Bảng Favorites cũng đang Active, dùng capacity On-demand, với `userSub` là partition key và `documentId` là sort key.

## Kết quả mong đợi

Bảng quan hệ hỗ trợ các màn hình chia sẻ và yêu thích.

## Khắc phục sự cố

- Index thừa có thể tăng chi phí lưu/ghi; cần gắn mỗi index với yêu cầu truy vấn.
- Không dùng email thay Cognito sub trong phân quyền.

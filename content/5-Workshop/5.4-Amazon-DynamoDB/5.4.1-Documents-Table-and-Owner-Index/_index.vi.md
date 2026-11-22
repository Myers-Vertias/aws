---
title: "Xác minh bảng Documents và OwnerIndex"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.4.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Kiểm tra bảng metadata chính và owner index do SAM tạo theo thiết kế đã duyệt.

## Các bước thực hiện

Hoàn thành từng bước theo thứ tự. Đọc thao tác, phần giải thích và kết quả cần xác nhận trước khi chuyển sang bước kế tiếp.


### Bước 1: Deploy SAM template theo mục 5.1.3

Deploy SAM template theo mục 5.1.3; không tạo bảng thủ công trùng với bảng trong stack. Template đặt tên theo `<stack-name>-documents`, bật `PAY_PER_REQUEST` và tạo `OwnerIndex`.

### Bước 2: Mở CloudFormation → stack của lab → Resources, chọn

Mở CloudFormation → stack của lab → Resources, chọn `DocumentsTable` để đi tới DynamoDB. Hoặc mở DynamoDB → Tables và tìm bảng `<stack-name>-documents` trong cùng Region.

### Bước 3: Trong Table details, đối chiếu partition key documentId

Trong Table details, đối chiếu partition key `documentId` kiểu String, không có sort key, capacity On-demand và trạng thái Active. Mở tab Indexes, xác nhận `OwnerIndex` có partition `ownerSub` và sort `createdAtDocumentId`, cả hai kiểu String.

### Bước 4: Ghi lại tên bảng và index cho Lambda

Ghi lại tên bảng và index cho Lambda. Truy vấn danh sách owner dùng Query trên GSI, không Scan toàn bảng.

## Vị trí ảnh chụp màn hình

![Ảnh 5.4.1: Bản ghi tài liệu trong bảng dms-dev-documents](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-documents-table-records.png)

Bảng `dms-dev-documents` đang triển khai dùng partition key `documentId` kiểu String, không có sort key, dùng capacity on-demand và có trạng thái Active.

![Ảnh 5.4.1: Key schema và trạng thái Active của bảng dms-dev-documents](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-documents-table-key-schema.png)

GSI `OwnerIndex` đang **Active**, có partition key `ownerSub` (String) và sort key `createdAtDocumentId` (String).

![Ảnh 5.4.1: OwnerIndex Active với hai khóa ownerSub và createdAtDocumentId](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-owner-index-active.png)

## Kết quả mong đợi

Bảng chính và index truy vấn theo owner sẵn sàng cho dữ liệu thử nghiệm.

## Khắc phục sự cố

- Tên và kiểu khóa DynamoDB phân biệt hoa/thường.
- Kiểm tra trạng thái index trước khi xử lý lỗi query.

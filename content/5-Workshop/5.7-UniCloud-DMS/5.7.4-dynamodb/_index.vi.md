---
title: "Tạo bảng và index DynamoDB"
date: 2026-10-16
weight: 4
chapter: false
pre: " <b> 5.7.4 </b> "
---

## Mục tiêu

Tạo các bảng metadata theo thiết kế đã duyệt. Xác nhận tên khóa và index ở mục 5.7.2 trước khi tiếp tục; nếu thiết kế thay đổi, hãy cập nhật đồng thời hướng dẫn và Lambda.

## Bước 1 — Tạo bảng Documents

1. Mở [Amazon DynamoDB console](https://console.aws.amazon.com/dynamodb/).
2. Chọn **Create table**.
3. Dùng tên development duy nhất, ví dụ **UniCloudDMS-Documents-dev**.
4. Đặt partition key là **documentId**, kiểu **String**. Không thêm sort key cho bảng cơ sở.
5. Chọn on-demand capacity cho workload workshop nhỏ và biến động, trừ khi thiết kế của tài khoản yêu cầu chế độ khác.
6. Giữ bật mã hóa và tạo bảng.
7. Trong mục **Indexes**, thêm GSI tên **ownerSub-updatedAt-index**, có partition key **ownerSub** (String) và sort key **updatedAt** (String). Dùng index này để truy vấn tài liệu của một người dùng thay vì quét toàn bảng.

**[Vị trí ảnh 5.7.4-01 — Khóa bảng Documents và GSI]**  
Chụp key schema và GSI sau khi index ở trạng thái Active.

## Bước 2 — Tạo bảng Shares

Tạo **UniCloudDMS-Shares-dev** với:

- Partition key **documentId** (String)
- Sort key **recipientSub** (String)

Thêm GSI tên **recipientSub-sharedAt-index** có partition key **recipientSub** và sort key **sharedAt**, cả hai kiểu String. Khóa cơ sở hỗ trợ liệt kê/thu hồi share theo tài liệu; GSI hỗ trợ liệt kê tài liệu được chia sẻ với người nhận.

**[Vị trí ảnh 5.7.4-02 — Bảng Shares và recipient index]**  
Chụp cấu hình key schema và GSI.

## Bước 3 — Tạo bảng Favorites

Tạo **UniCloudDMS-Favorites-dev** với partition key **userSub** (String) và sort key **documentId** (String). Một item thể hiện quan hệ yêu thích của một người dùng với một tài liệu. Chỉ lưu createdAt nếu UI hoặc cách sắp xếp cần đến.

## Bước 4 — Xác nhận trạng thái bảng và cách truy vấn

Chờ đến khi tất cả bảng và index ở trạng thái **Active**. Xem lại thiết kế khóa:

- Tài liệu của tôi: query Documents owner index theo sub của chủ sở hữu đã xác thực.
- Được chia sẻ với tôi: query Shares recipient index rồi lấy metadata tài liệu được phép truy cập.
- Yêu thích: query theo userSub rồi lấy các tài liệu tương ứng.
- Thùng rác: query tài liệu của chủ sở hữu và lọc deletedAt ở quy mô project này.
- Category và tags: lọc tập kết quả của chủ sở hữu; không quét toàn bảng cho request thông thường.

Với prototype giảng dạy này, on-demand giúp tránh phải lập kế hoạch capacity nhưng không có nghĩa là sử dụng miễn phí không giới hạn. Kiểm tra bảng giá hiện tại và xóa bảng không còn dùng sau lab.

## Kết quả mong đợi

Ba bảng cùng các GSI được mô tả đã ở trạng thái Active. Chưa đưa dữ liệu sản xuất hoặc dữ liệu cá nhân vào bảng.

## Khắc phục sự cố

- **Index đang tạo:** chờ GSI chuyển sang Active trước khi kiểm thử query.
- **Query không trả item:** kiểm tra chính xác tên và kiểu thuộc tính khóa; tên khóa DynamoDB phân biệt hoa/thường.
- **Access denied:** kiểm tra execution role của Lambda trong phần IAM. Không giải quyết bằng cách cấp wildcard permission cho mọi tài nguyên.

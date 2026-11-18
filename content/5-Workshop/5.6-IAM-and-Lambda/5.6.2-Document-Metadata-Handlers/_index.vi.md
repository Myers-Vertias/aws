---
title: "Triển khai Handler Metadata Tài liệu"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.6.2 </b> "
---

{{% notice info %}}
Hành vi handler đang triển khai được mô tả dựa trên Lambda implementation và minh chứng API/trình duyệt hiện có. Không chụp riêng thao tác Lambda test event.
{{% /notice %}}

## Mục tiêu

Triển khai thao tác tạo/liệt kê/chi tiết/cập nhật bằng người gọi đã xác thực và mô hình dữ liệu đã duyệt.

## Các bước thực hiện

1. Mở `backend/src/index.mjs` và dùng dispatcher của API Gateway để tách method/path; lấy caller `sub` từ JWT claims mà authorizer đã xác minh. Không nhận owner ID từ body frontend.
2. Dùng `OwnerIndex` để Query danh sách của owner và `documentId` để lấy chi tiết; trước khi trả dữ liệu, kiểm tra owner hoặc share record còn hiệu lực.
3. API complete upload dùng `HeadObject` xác nhận object S3 rồi ghi item metadata vào Documents table. Giữ metadata và bytes tách nhau: DynamoDB lưu thông tin file, S3 giữ nội dung.
4. PATCH metadata chỉ nhận `description`, `category`, `tags`; kiểm tra kiểu/giới hạn và chỉ cho owner cập nhật active file. Trả DTO/error JSON kèm HTTP status nhất quán.
5. Sau thay đổi code, từ `backend/` chạy `sam validate --lint`, `sam build` và deploy stack theo mục 5.1.3 sau khi xem change set.

### Mẫu lấy danh tính từ JWT claims

Đoạn trích dưới đây nằm trong `backend/src/index.mjs`; Lambda chỉ dùng `sub` do API Gateway JWT authorizer đã xác minh:

```javascript
const identity = (event) => {
  const claims = event.requestContext?.authorizer?.jwt?.claims;
  if (!claims?.sub) throw new HttpError(401, "Authentication is required");
  return { sub: claims.sub, email: claims.email?.toLowerCase() ?? claims.username ?? claims.sub };
};
```

## Minh chứng kiểm thử

API lấy danh sách tài liệu khi đăng nhập trả HTTP `200`; request từ tài khoản khác tới tài liệu riêng chưa chia sẻ trả HTTP `404`, xem mục 5.7.2. Danh mục/mô tả đã lưu hiển thị ở trang chi tiết; bản ghi tài liệu trong DynamoDB được ghi ở mục 5.8.2 và 5.4.1. Các ảnh hiện có được dùng lại để tránh trùng lặp. Chưa chụp thao tác Lambda console test event riêng.

## Kết quả mong đợi

Handler metadata thao tác đúng bản ghi theo người dùng.

## Khắc phục sự cố

- Kiểm tra tên key phân biệt hoa/thường, cấu hình index và sub lấy từ token.
- Tránh scan toàn bảng cho truy vấn Tài liệu của tôi thông thường.

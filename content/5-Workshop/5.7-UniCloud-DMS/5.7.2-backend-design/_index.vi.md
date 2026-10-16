---
title: "Thiết kế mô hình dữ liệu và API contract"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

## Mục tiêu

Thống nhất mô hình dữ liệu, API route, quy tắc định danh và luồng upload trước khi tạo tài nguyên AWS. Điều này giúp tránh phải sửa đi sửa lại giữa React, Lambda, API Gateway, DynamoDB và S3.

## 1. Định danh và quy tắc phân quyền

Dùng claim **sub** của người dùng Cognito làm định danh ổn định. Email hữu ích để tìm tài khoản người nhận và hiển thị thông tin, nhưng không nên là khóa phân quyền chính.

Mỗi Lambda cần bảo vệ phải xác định người gọi từ token đã được xác thực. Không tin cậy user ID, owner ID, S3 key hoặc quyền do trình duyệt gửi lên nếu backend chưa kiểm tra.

Thống nhất quy tắc trước khi triển khai:

- **Owner:** được quản lý metadata, chia sẻ quyền, chuyển tài liệu vào Thùng rác, khôi phục và xóa vĩnh viễn.
- **Viewer:** được xem và tải xuống tài liệu được chia sẻ rõ ràng và chưa bị đưa vào Thùng rác; không được sửa, chia sẻ tiếp hay xóa.
- **Người không có quyền:** nhận phản hồi 403 hoặc 404 phù hợp và không được cấp presigned URL.
- **Share đã thu hồi:** không còn cấp quyền truy cập metadata hoặc tạo download URL mới.

## 2. Mô hình DynamoDB đề xuất

Dùng ba bảng trong workshop để tách bạch quyền sở hữu, quan hệ chia sẻ và yêu thích theo người dùng. Xác nhận tên bảng và khóa trước khi tạo.

| Bảng | Khóa chính | Mục đích |
|---|---|---|
| Documents | Partition key: **documentId** | Metadata file, ownerSub, s3Key, tên file, content type, dung lượng, category, tags, description, createdAt, updatedAt, deletedAt |
| Shares | Partition key: **documentId**; sort key: **recipientSub** | Quan hệ chia sẻ, ownerSub, permission, sharedAt. Thêm GSI có recipientSub là partition key và sharedAt là sort key để truy vấn Được chia sẻ với tôi. |
| Favorites | Partition key: **userSub**; sort key: **documentId** | Quan hệ yêu thích của người dùng và createdAt tùy chọn. |

Với bảng Documents, thêm GSI có **ownerSub** làm partition key và **updatedAt** làm sort key để truy vấn tài liệu thuộc về một chủ sở hữu. Ở quy mô workshop này, Lambda có thể lọc deletedAt, category và tags trên kết quả truy vấn của riêng chủ sở hữu. Không quét toàn bộ bảng Documents cho các request thông thường.

Các trường metadata đề xuất:

- **documentId**, **ownerSub**, **fileName**, **s3Key**
- **contentType**, **sizeBytes**, **category**, **tags**, **description**
- **createdAt**, **updatedAt** và **deletedAt** tùy chọn
- Mã trạng thái nội bộ như **active**, **processing** hoặc **failed**

Dùng mã category ổn định như **other**, **work**, **finance**, **hr**, **technical** và **meeting**. Chuẩn hóa tags nhất quán, ví dụ chuyển về chữ thường và bỏ khoảng trắng thừa. Chỉ dịch nhãn category/status trên UI.

**[Vị trí ảnh 5.7.2-01 — Mô hình dữ liệu đã thống nhất]**  
Chèn sơ đồ ER hoặc sơ đồ access pattern cuối cùng sau khi xác nhận thiết kế bảng và index.

## 3. API contract đề xuất

Dùng một contract nhất quán giữa frontend và backend. Các route dưới đây là phương án ban đầu; nếu thay đổi, hãy ghi rõ trước khi triển khai.

| Method | Route | Mục đích |
|---|---|---|
| GET | /documents | Liệt kê tài liệu của người gọi |
| POST | /documents/upload-url | Kiểm tra metadata upload và cấp S3 PUT URL có thời hạn ngắn |
| POST | /documents/{id}/complete | Xác minh object đã upload và lưu metadata cuối cùng |
| GET | /documents/{id} | Lấy chi tiết tài liệu khi có quyền |
| PUT | /documents/{id} | Cập nhật metadata được phép |
| GET | /documents/{id}/download-url | Kiểm tra quyền rồi cấp S3 GET URL ngắn hạn |
| POST | /documents/{id}/favorite | Thêm hoặc bỏ yêu thích của người gọi |
| GET | /favorites | Liệt kê tài liệu yêu thích của người gọi |
| GET | /trash | Liệt kê tài liệu trong Thùng rác của người gọi |
| POST | /documents/{id}/restore | Khôi phục tài liệu thuộc sở hữu của người gọi |
| DELETE | /documents/{id} | Chuyển tài liệu của Owner vào Thùng rác |
| DELETE | /documents/{id}/permanent | Xóa vĩnh viễn tài liệu đã vào Thùng rác và object tương ứng |
| GET | /shared | Liệt kê tài liệu được chia sẻ với người gọi |
| GET/POST | /documents/{id}/shares | Liệt kê hoặc tạo share; khi tạo phải kiểm tra người nhận |
| DELETE | /documents/{id}/shares/{recipientSub} | Thu hồi một share hiện có |

Mọi route truy cập tài liệu phải áp dụng cùng quy tắc Owner/Viewer. Xóa vĩnh viễn là thao tác phá hủy dữ liệu nên phải được bảo vệ bằng quyền Owner và xác nhận trên UI.

## 4. Luồng upload file

1. Frontend đã xác thực yêu cầu API cấp upload URL, gửi metadata file chứ không gửi bytes của file.
2. Lambda kiểm tra dung lượng, content type, danh tính và category/tags được phép. Lambda tự tạo document ID và S3 key; trình duyệt không được tự chọn object key tùy ý.
3. Trình duyệt upload trực tiếp lên S3 bằng PUT URL ngắn hạn và content type tương ứng.
4. Frontend gọi route hoàn tất. Lambda xác minh object tồn tại, đọc metadata cần thiết và ghi bản ghi tài liệu cuối cùng.
5. Khi tải xuống, Lambda kiểm tra quyền trước khi cấp GET URL ngắn hạn.

## 5. Quy ước lỗi

Trả về mã lỗi nhất quán, có thể đọc bằng máy, ví dụ **UNAUTHORIZED**, **FORBIDDEN**, **DOCUMENT_NOT_FOUND**, **INVALID_FILE**, **ALREADY_SHARED** và **USER_NOT_FOUND**. UI ánh xạ các mã này sang tiếng Việt hoặc tiếng Anh; phân quyền backend không phụ thuộc ngôn ngữ đã chọn.

**[Vị trí ảnh 5.7.2-02 — Danh sách API và ma trận quyền]**  
Chèn danh sách endpoint đã duyệt hoặc bảng thể hiện mỗi role được gọi route nào.

## Kết quả mong đợi

Có thiết kế bảng/khóa đã được xem xét, danh sách endpoint, luồng upload/download và quy tắc Owner/Viewer rõ ràng. Chưa tạo bảng hay API cho đến khi các quyết định này đã được hiểu và thống nhất.

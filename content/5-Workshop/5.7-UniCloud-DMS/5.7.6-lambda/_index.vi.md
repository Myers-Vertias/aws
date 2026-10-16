---
title: "Cấu hình IAM và triển khai Lambda"
date: 2026-10-16
weight: 6
chapter: false
pre: " <b> 5.7.6 </b> "
---

## Mục tiêu

Tạo quyền thực thi theo nguyên tắc đặc quyền tối thiểu và triển khai các handler backend đã mô tả trong API contract. Không bắt đầu bằng cách cấp toàn quyền AWS cho mọi Lambda.

## Bước 1 — Tạo execution role

1. Mở [IAM console](https://console.aws.amazon.com/iam/).
2. Tạo IAM role dành cho dịch vụ Lambda.
3. Gắn policy cơ bản do AWS quản lý cho Lambda để ghi log CloudWatch.
4. Thêm policy do mình quản lý, giới hạn vào các bảng DynamoDB và bucket/prefix S3 của workshop. Chỉ dùng các hành động đọc/ghi DynamoDB cần thiết và hành động S3 như GetObject, PutObject, DeleteObject khi thực sự cần.
5. Chỉ cho phép truy cập tài nguyên tạo cho workshop. Tránh cấp quyền tài nguyên ***** khi có thể dùng ARN cụ thể.
6. Tạo role riêng khi các handler có nhu cầu quyền khác nhau đáng kể.

**[Vị trí ảnh 5.7.6-01 — Execution role và policy giới hạn]**  
Chụp role và phần tóm tắt policy. Không đưa credentials hoặc giá trị tài khoản nhạy cảm vào ảnh.

## Bước 2 — Triển khai các backend handler

Dùng Node.js runtime còn được AWS hỗ trợ đã chọn cho tài khoản và đóng gói các package AWS SDK v3 cần thiết cùng mã function. Giữ handler và tiện ích validate dùng chung trong repository source.

Triển khai API contract ở mục 5.7.2. Backend tối thiểu phải:

- Lấy Cognito sub của người gọi từ authorizer context đã xác thực.
- Validate tên file, content type, dung lượng, category, tags và description.
- Query DynamoDB theo key/index phù hợp thay vì quét toàn bảng.
- Kiểm tra ownership hoặc share còn hiệu lực trước khi trả metadata hay cho phép thay đổi.
- Tạo S3 key duy nhất do server kiểm soát và presigned URL ngắn hạn.
- Xác minh object đã upload trước khi hoàn tất metadata.
- Chuẩn hóa tags và giữ mã category/status độc lập với ngôn ngữ.
- Trả mã lỗi đọc được bằng máy và HTTP status phù hợp.
- Không ghi log token đầy đủ, signed URL, metadata nhạy cảm hay nội dung file.

**[Vị trí ảnh 5.7.6-02 — Mã handler và kết quả test invocation]**  
Sau khi viết handler, chụp vị trí mã nguồn và kết quả test bằng dữ liệu tổng hợp. Che token và signed URL.

## Bước 3 — Triển khai Trash và xóa vĩnh viễn an toàn

Chuyển tài liệu vào Thùng rác cần cập nhật metadata bằng deletedAt nhưng giữ nguyên object S3. Khôi phục xóa deletedAt đối với owner hợp lệ. Xóa vĩnh viễn phải kiểm tra quyền sở hữu, xác nhận tài liệu đang ở Thùng rác theo quy tắc nghiệp vụ đã chọn, xóa object S3 rồi xóa metadata và các bản ghi share/favorite liên quan theo thiết kế.

Cần xem xét thứ tự lỗi: nếu xóa S3 thành công nhưng thao tác database sau đó thất bại, hệ thống phải có cách phục hồi được mô tả. Không tuyên bố các thao tác qua nhiều dịch vụ là giao dịch nguyên tử.

## Bước 4 — Triển khai phân quyền share

Khi tạo share, xác minh tài khoản người nhận, từ chối chia sẻ cho chính mình và ngăn share trùng. Chỉ owner được tạo hoặc thu hồi share. Mỗi lần đọc hoặc tải xuống phải kiểm tra caller là owner hay viewer có share còn hiệu lực. Thu hồi share chặn API access và việc cấp presigned URL mới; URL đã cấp vẫn dùng được đến khi hết hạn.

## Bước 5 — Kiểm thử từng handler

Dùng Lambda test event với danh tính và bản ghi tài liệu tổng hợp. Bao gồm kiểm thử thành công và thất bại đối với thiếu identity, sai owner, share đã thu hồi, loại file không hợp lệ, file quá lớn, tài liệu không tồn tại và truy vấn object thất bại. Không dùng tài liệu riêng tư của người dùng thật làm dữ liệu test.

## Kết quả mong đợi

Các handler và execution role đã được triển khai theo nguyên tắc quyền hẹp và vượt qua các test riêng. Việc tích hợp API sẽ thực hiện ở module kế tiếp.

## Khắc phục sự cố

- **AccessDenied từ DynamoDB/S3:** kiểm tra role policy, ARN đích, Region và biến môi trường.
- **Thiếu identity claim:** kiểm tra context của authorizer; không chấp nhận ownerSub trong request body làm bằng chứng sở hữu.
- **Upload bằng presigned URL thất bại:** kiểm tra object key, thời hạn, content type và S3 CORS.
- **Lỗi cold start/import:** xác minh runtime và dependency đã đóng gói.

---
title: "Xây dựng UniCloud DMS trên AWS"
date: 2026-10-16
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

# Xây dựng hệ thống quản lý tài liệu Serverless trên AWS

{{% notice info %}}
**Trạng thái workshop:** Đây là khung hướng dẫn triển khai. Tài nguyên AWS, ảnh chụp màn hình và kết quả kiểm thử cần được bổ sung sau khi hoàn thành các bước tương ứng. Không xem kết quả mong đợi là bằng chứng cho một lần triển khai thành công.
{{% /notice %}}

## Tổng quan

UniCloud DMS là ứng dụng quản lý tài liệu có frontend prototype được xây dựng bằng React và Vite. Frontend hiện có các màn hình danh sách và chi tiết tài liệu, Yêu thích, Tags/Danh mục, Thùng rác, quy trình chia sẻ, xác thực giả lập và giao diện tiếng Việt/tiếng Anh. Workshop này hướng dẫn kết nối các quy trình đó với dịch vụ AWS thực tế.

Kiến trúc mục tiêu sử dụng Amazon Cognito để xác thực người dùng, Amazon API Gateway cung cấp REST API, AWS Lambda xử lý nghiệp vụ và phân quyền, Amazon DynamoDB lưu metadata và các quan hệ, còn Amazon S3 lưu file ở chế độ riêng tư. AWS Amplify Hosting phục vụ frontend và Amazon CloudWatch cung cấp log phục vụ xử lý lỗi.

## Mục tiêu học tập

Sau workshop, người học có thể:

- Xác thực người dùng bằng Amazon Cognito.
- Lưu file riêng tư trên S3 và metadata trên DynamoDB.
- Xây dựng API bằng API Gateway và Lambda.
- Upload/download file bằng presigned URL có thời hạn ngắn.
- Kiểm tra quyền sở hữu và quyền Viewer ở backend.
- Kết nối frontend React hiện có với API thật.
- Triển khai frontend và kiểm tra giao diện tiếng Việt/tiếng Anh.
- Xem log và dọn dẹp tài nguyên được tạo cho bài thực hành.

## Kiến trúc mục tiêu

**[Vị trí ảnh 5.7-01 — Sơ đồ kiến trúc UniCloud DMS hoàn chỉnh]**  
Tạo hoặc chèn sơ đồ sau khi thiết kế backend được thống nhất. Sơ đồ cần thể hiện Cognito, frontend React được host bằng Amplify, API Gateway, Lambda, DynamoDB, S3 và CloudWatch, đồng thời chỉ rõ chiều của các request đã xác thực.

Luồng xử lý:

1. Người dùng đăng nhập và nhận token từ Cognito.
2. Frontend gửi token hợp lệ trong các request cần xác thực.
3. API Gateway xác thực token và gọi Lambda phù hợp.
4. Lambda kiểm tra quyền sở hữu hoặc quyền chia sẻ trước khi trả dữ liệu hay cấp presigned URL.
5. DynamoDB lưu metadata và quan hệ truy cập; S3 lưu file thực tế.
6. CloudWatch nhận log từ Lambda và những thành phần đã được cấu hình.

## Các phần của workshop

1. [Điều kiện tiên quyết và kiểm soát chi phí](5.7.1-prerequisites/)
2. [Thiết kế mô hình dữ liệu và API contract](5.7.2-backend-design/)
3. [Cấu hình Amazon Cognito](5.7.3-cognito/)
4. [Tạo bảng và index DynamoDB](5.7.4-dynamodb/)
5. [Cấu hình lưu trữ Amazon S3 riêng tư](5.7.5-s3/)
6. [Cấu hình IAM và triển khai Lambda](5.7.6-lambda/)
7. [Cấu hình Amazon API Gateway](5.7.7-api-gateway/)
8. [Tích hợp frontend React](5.7.8-frontend-integration/)
9. [Triển khai bằng AWS Amplify Hosting](5.7.9-amplify/)
10. [Kiểm thử end-to-end và xác minh bảo mật](5.7.10-testing/)
11. [Giám sát và khắc phục sự cố](5.7.11-monitoring/)
12. [Dọn dẹp tài nguyên workshop](5.7.12-cleanup/)

## Quy ước chèn ảnh

Mỗi mục có mã ảnh riêng. Sau khi hoàn thành bước tương ứng, thay thông báo placeholder bằng ảnh Markdown thật, ví dụ:

```markdown
![Cấu hình App Client của Cognito](/images/5-Workshop/5.7-UniCloud-DMS/5.7.3-02-app-client.png)
```

Lưu ảnh theo quy ước thư mục ảnh hiện có của repository. Không chụp mật khẩu, access key, toàn bộ JWT hoặc thông tin bí mật. Hãy che thông tin cá nhân khi cần thiết.

## Nguyên tắc bảo mật

- Giữ S3 bucket ở chế độ riêng tư; không bật public access để tiện kiểm thử.
- Dùng claim `sub` của Cognito làm định danh người dùng nội bộ ổn định. Email là thuộc tính tài khoản, không phải khóa phân quyền.
- Kiểm tra quyền ở Lambda. Ẩn nút hay bảo vệ route frontend không thay thế được bảo mật backend.
- Giữ mã category và status độc lập với ngôn ngữ; chỉ dịch nhãn hiển thị trên UI.
- Kiểm tra quyền trước khi tạo từng download URL.
- Mỗi Lambda execution role chỉ được cấp các quyền cần thiết cho chức năng đó.
- Dùng tài khoản thử nghiệm và tài liệu mẫu không chứa dữ liệu nhạy cảm.

## Checklist hoàn thành

- [ ] Đăng ký và đăng nhập Cognito hoạt động với danh tính AWS thật.
- [ ] API cần bảo vệ từ chối request không có token hợp lệ.
- [ ] Metadata tài liệu được lưu trong DynamoDB.
- [ ] File được upload/download từ S3 riêng tư bằng presigned URL.
- [ ] Yêu thích, Tags/Danh mục và Thùng rác dùng dữ liệu backend.
- [ ] Chia sẻ giữa hai tài khoản hoạt động và quyền đã thu hồi bị từ chối.
- [ ] Nhãn tiếng Việt và tiếng Anh nhất quán trên các trang.
- [ ] Đã kiểm tra frontend triển khai và log dịch vụ.
- [ ] Đã xem lại và thực hiện các bước cleanup phù hợp.

Trang này là khung hướng dẫn, không phải nhật ký xác nhận triển khai. Bổ sung tên tài nguyên đã kiểm chứng, ảnh chụp thật, đầu ra đã chạy thử và sự cố quan sát được trong quá trình thực hiện.

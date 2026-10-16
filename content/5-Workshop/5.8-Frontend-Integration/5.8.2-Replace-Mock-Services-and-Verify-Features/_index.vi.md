---
title: "Thay Mock Service và Xác minh Feature"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.8.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Chuyển đọc/ghi tài liệu sang backend và kiểm thử upload/download cùng các feature hiện có.

## Các bước thực hiện

1. Cập nhật service layer để gọi API Gateway thay vì dùng localStorage làm nguồn dữ liệu tài liệu chính.
2. Ánh xạ API error code sang translation key VI/EN hiện có; xử lý loading, kết quả rỗng, 401, 403 và lỗi mạng.
3. Upload bằng cách xin URL, PUT file lên S3 rồi gọi endpoint hoàn tất. Chỉ download sau khi backend kiểm tra quyền.
4. Kết nối Favorites, category/tags, Thùng rác/khôi phục/xóa vĩnh viễn và chia sẻ với API endpoint.
5. Xác minh category/status lưu ổn định và lựa chọn ngôn ngữ được giữ qua điều hướng/refresh.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.8.2 — Response API thật và feature tài liệu hoạt động với dữ liệu AWS, đã che giá trị nhạy cảm.]**

## Kết quả mong đợi

Frontend hiện có hoạt động với AWS backend mà không làm hỏng feature đã có.

## Khắc phục sự cố

- Build thành công không chứng minh API runtime hoạt động; kiểm tra Network panel trình duyệt.
- Không public S3 để né lỗi upload/download.

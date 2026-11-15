---
title: "Kiểm thử Yêu thích, Thẻ, Thùng rác và VI/EN"
date: 2026-11-10
weight: 3
chapter: false
pre: " <b> 5.10.3 </b> "
---

## Mục tiêu

Ghi nhận kết quả kiểm thử đầu cuối các chức năng yêu thích, siêu dữ liệu tài liệu, thùng rác và lựa chọn ngôn ngữ của ứng dụng quản lý tài liệu đã triển khai.

## Môi trường

- Giao diện: nhánh production trên AWS Amplify
- Tài khoản kiểm thử: chủ sở hữu tài liệu và một người dùng Cognito thứ hai
- Lưu trữ và API: Amazon S3 cùng backend API Gateway/Lambda đã triển khai
- Bộ ảnh hiện có gồm ảnh giao diện Favorites và lựa chọn ngôn ngữ từ phiên local, ảnh Trash từ production và bản ghi từ các bảng DynamoDB đã triển khai; nguồn ảnh được ghi rõ trong chú thích.

## Kết quả đã xác nhận

1. **Yêu thích:** Chủ sở hữu đánh dấu tài liệu đã tải lên là yêu thích và tài liệu xuất hiện trong trang Yêu thích. Nút bật/tắt yêu thích cũng đã được quan sát ở danh sách và trang chi tiết tài liệu. Yêu thích là thiết lập cá nhân theo từng tài khoản, độc lập với chức năng chia sẻ tài liệu; quy trình này không yêu cầu đồng bộ trạng thái yêu thích giữa các tài khoản.
2. **Siêu dữ liệu:** Danh mục được đổi thành `Công việc`, thẻ thành `ảnh` và mô tả thành `ảnh Konoka`. Các giá trị đã lưu hiển thị trong trang chi tiết tài liệu.
3. **Thùng rác và khôi phục:** Đưa tài liệu vào Thùng rác làm tài liệu biến khỏi danh sách đang hoạt động. Khôi phục đưa tài liệu trở lại danh sách và xóa mục khỏi Thùng rác. Giao diện ghi thời hạn dọn sau 30 ngày là tính năng mô phỏng; chưa xác minh việc tự xóa bằng S3 Lifecycle.
4. **Lựa chọn ngôn ngữ:** Đã chọn cả tiếng Việt và tiếng Anh. Lựa chọn tiếng Việt vẫn được giữ sau khi tải lại trang Kho tài liệu, chuyển sang Dashboard và đăng xuất rồi đăng nhập lại bằng cùng tài khoản. Không thêm ảnh riêng cho lần kiểm tra cuối vì giao diện hiển thị giống trạng thái đã chụp.
5. **Bộ lọc danh mục:** Chọn danh mục `Công việc` thì danh sách tài liệu local trả về `Konoka.png`.
6. **Tìm theo thẻ:** Tìm thẻ tiếng Việt đã lưu `ảnh` thì danh sách tài liệu local trả về `Konoka.png`.

## Minh chứng

![Ảnh 5.10.3-A: Metadata đã lưu trên trang chi tiết trong phiên frontend local](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-metadata-update-local.png)

![Ảnh 5.10.3-B: Konoka.png xuất hiện trong mục Yêu thích ở phiên frontend local](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-favorite-local.png)

![Ảnh 5.10.3-C: Khôi phục thành công và Thùng rác trống trên frontend production](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-trash-restore-production.png)

![Ảnh 5.10.3-D: Bản ghi yêu thích trong bảng DynamoDB đã triển khai](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-favorites-table-records.png)

![Ảnh 5.10.3-E: Giao diện tiếng Anh trên Dashboard local](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-language-english-dashboard-local.png)

![Ảnh 5.10.3-F: Giao diện tiếng Việt trên danh sách tài liệu local](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-language-vietnamese-documents-local.png)

![Ảnh 5.10.3-G: Lựa chọn tiếng Việt vẫn được giữ sau khi tải lại trang Kho tài liệu local](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-vietnamese-language-persists-after-refresh.png)

![Ảnh 5.10.3-H: Bộ lọc danh mục Công việc trả về Konoka.png trong danh sách tài liệu local](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-category-filter-work-local.png)

![Ảnh 5.10.3-I: Tìm theo thẻ ảnh trả về Konoka.png](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-tag-search-anh-local.png)

![Ảnh 5.10.3-J: Dashboard vẫn giữ lựa chọn tiếng Việt sau khi chuyển từ Kho tài liệu](/images/5-Workshop/5.10-End-to-End-Testing/5.10.3-vietnamese-language-persists-dashboard-local.png)

## Các bước còn cần kiểm tra

- Chỉ thử xóa vĩnh viễn với dữ liệu kiểm thử có thể bỏ; chức năng này chưa được xác minh.

## Ghi chú

Chỉ ghi kết quả đã trực tiếp quan sát. Báo cáo chưa khẳng định đã kiểm thử xóa vĩnh viễn hoặc tự hết hạn đối tượng trên S3.

# 5.10.2 Kiểm thử chia sẻ tài liệu giữa hai người dùng

## Mục tiêu

Xác nhận người sở hữu có thể chia sẻ tài liệu với tài khoản khác và tài khoản nhận có thể xem, tải tài liệu được chia sẻ.

## Các bước thực hiện

1. Đăng nhập bằng tài khoản người sở hữu và tải một tài liệu lên hệ thống.
2. Chia sẻ tài liệu với email của tài khoản người nhận.
3. Đăng xuất, đăng nhập bằng tài khoản người nhận.
4. Mở mục **Được chia sẻ với tôi**, xác nhận tài liệu xuất hiện, sau đó tải xuống.

## Kết quả đã xác minh

Tài khoản người nhận nhìn thấy tài liệu trong mục **Được chia sẻ với tôi**. Sau khi chọn tải xuống, giao diện hiển thị thông báo tải xuống giả lập và lịch sử tải xuống của trình duyệt ghi nhận tệp Konoka.png với trạng thái **Done**. Minh chứng này xác nhận luồng hiển thị tài liệu chia sẻ và thao tác tải xuống từ trình duyệt; chưa xác nhận riêng nội dung tệp được tải trực tiếp từ S3.

Ngày 18/11/2026, khi đăng nhập bằng tài khoản khác, một request đã xác thực chỉ đọc `GET /documents/{documentId}` tới tài liệu của chủ sở hữu nhưng chưa chia sẻ cho tài khoản này trả HTTP `404` với thông báo `Document not found`. Request không sửa đổi dữ liệu. Kết quả xác nhận tài khoản được kiểm tra không thể truy xuất tài liệu chưa chia sẻ; API dùng not-found cho tài liệu nằm ngoài quyền truy cập của người gọi.

## Minh chứng

![Ảnh 5.10.2: Tài khoản nhận thấy các tài liệu được chia sẻ trên frontend production](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-shared-with-me-production.png)

![Ảnh 5.10.2: Tài khoản nhận mở tài liệu chia sẻ và lịch sử trình duyệt ghi nhận tải xuống hoàn tất ở phiên local](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-recipient-document-download-local.png)

![Ảnh 5.10.2: Request chỉ đọc đã xác thực tới tài liệu chưa chia sẻ trả HTTP 404](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-unshared-document-access-denied-404.png)

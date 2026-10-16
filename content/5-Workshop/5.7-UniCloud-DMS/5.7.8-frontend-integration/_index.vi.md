---
title: "Tích hợp frontend React"
date: 2026-10-16
weight: 8
chapter: false
pre: " <b> 5.7.8 </b> "
---

## Mục tiêu

Thay luồng xác thực mock và dữ liệu tài liệu giả lập bằng kết nối Cognito và API Gateway đã kiểm chứng, đồng thời giữ nguyên giao diện và nghiệp vụ hiện có.

## Bước 1 — Cấu hình biến môi trường

Tạo file môi trường cục bộ từ file ví dụ đã làm sạch trong repository. Dùng tên biến nhất quán với code ứng dụng, ví dụ:

~~~text
VITE_AWS_REGION=<region>
VITE_COGNITO_USER_POOL_ID=<user-pool-id>
VITE_COGNITO_CLIENT_ID=<app-client-id>
VITE_API_BASE_URL=<api-gateway-invoke-url>
~~~

Thay mọi placeholder bằng giá trị của môi trường development. Không commit file môi trường thật nếu có thông tin nhạy cảm. Lưu ý biến Vite được đưa vào bundle trình duyệt; tuyệt đối không đặt secret key trong đó.

**[Vị trí ảnh 5.7.8-01 — Cấu hình môi trường đã làm sạch]**  
Chỉ hiển thị tên biến và giá trị mẫu/đã che. Không chụp credentials, token hay secret.

## Bước 2 — Tích hợp xác thực Cognito

Sử dụng SDK và cấu hình đã hoàn tất ở mục 5.7.3. Thay hành vi mock sign-in, sign-up, khôi phục session và sign-out bằng thao tác Cognito thật. Xử lý xác minh email và lỗi xác thực mà không hiển thị nguyên đối tượng SDK hoặc token cho người dùng.

Giữ route guard vì trải nghiệm sử dụng, nhưng mọi quyền tài nguyên phải được kiểm tra ở Lambda.

## Bước 3 — Thay mock document service

Cập nhật tầng service hiện tại để gọi API base URL thay vì coi localStorage là nguồn dữ liệu tài liệu chính. Giữ contract giao diện hiện có nếu có thể để hạn chế thay đổi ở các page.

- Đính kèm token session hiện tại vào request cần bảo vệ.
- Xử lý 401 bằng refresh hoặc kết thúc session theo hành vi SDK.
- Xử lý 403 bằng thông báo từ chối truy cập đã dịch.
- Ánh xạ API error code sang dictionary dịch tiếng Việt/tiếng Anh hiện tại.
- Giữ category và status ổn định, độc lập với ngôn ngữ.
- Không lưu bytes của file hoặc response server nhạy cảm trong localStorage.

**[Vị trí ảnh 5.7.8-02 — Request API thật từ frontend]**  
Chụp request/response thành công sau khi che giá trị cá nhân, token và header nhạy cảm.

## Bước 4 — Kết nối upload và download

Với upload:

1. Yêu cầu API cấp upload URL.
2. Upload file trực tiếp lên S3 bằng URL trả về và content type tương ứng.
3. Gọi endpoint hoàn tất để Lambda xác minh object và lưu metadata.
4. Chỉ cập nhật UI sau response hoàn tất thành công; xử lý upload bỏ dở và lỗi.

Với download, yêu cầu URL từ API sau khi backend đã kiểm tra quyền owner/share. Dùng URL trả về mà không chỉnh sửa và để URL hết hạn tự nhiên. Không public bucket.

## Bước 5 — Xác minh các feature hiện có

Kiểm thử lại Documents, Document Detail, Favorites, Tags/Danh mục, Thùng rác, chia sẻ, tìm kiếm/lọc và chuyển đổi ngôn ngữ. Chỉ bỏ giả định mock sau khi route backend tương ứng đã tồn tại và được kiểm thử. Chỉ giữ fallback development nếu được thiết kế có chủ đích và không thể nhầm với dữ liệu production.

## Kết quả mong đợi

Frontend đăng nhập bằng Cognito và đọc/ghi metadata qua API. Upload/download sử dụng object S3 riêng tư cùng presigned URL do server cấp.

## Khắc phục sự cố

- **Thay đổi environment chưa có hiệu lực:** khởi động lại Vite sau khi sửa biến môi trường.
- **API URL không xác định:** kiểm tra tiền tố và tên biến, sau đó restart Vite.
- **401/403:** kiểm tra luồng token, authorizer và logic phân quyền Lambda.
- **Upload S3 lỗi sau khi nhận URL:** kiểm tra CORS và content type có khớp request đã ký không.
- **Page vẫn hiển thị dữ liệu mock:** tìm import mock service hoặc nguồn document từ localStorage còn sót trước khi sửa component không liên quan.

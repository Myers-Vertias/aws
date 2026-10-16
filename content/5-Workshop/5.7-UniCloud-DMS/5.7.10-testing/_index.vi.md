---
title: "Kiểm thử end-to-end và xác minh bảo mật"
date: 2026-10-16
weight: 10
chapter: false
pre: " <b> 5.7.10 </b> "
---

## Mục tiêu

Xác minh toàn bộ ứng dụng với người dùng Cognito thật và dữ liệu trên AWS. Ghi lại kết quả thực tế; chỉ đánh dấu đạt sau khi đã chạy kiểm thử.

## Bước 1 — Chuẩn bị hai tài khoản thử nghiệm

Tạo **User A** và **User B** bằng luồng đăng ký được hỗ trợ. Hoàn tất xác minh nếu được yêu cầu. Dùng tài liệu mẫu không chứa dữ liệu cá nhân hoặc bí mật.

**[Vị trí ảnh 5.7.10-01 — Hai tài khoản thử nghiệm đã đăng nhập]**  
Chụp ứng dụng với định danh tài khoản đã che, hoặc dùng bảng kiểm thử thay cho việc hiển thị email.

## Bước 2 — Kiểm thử nghiệp vụ tài liệu

Chạy các kịch bản dưới đây và ghi Pass/Fail, ngày kiểm thử cùng lỗi gặp phải:

| Kịch bản | Kết quả mong đợi |
|---|---|
| Đăng ký và đăng nhập | Tài khoản đã xác minh có thể đăng nhập và đăng xuất. |
| Tạo/upload tài liệu | File được lưu trên S3 riêng tư và metadata lưu DynamoDB sau bước xác minh hoàn tất. |
| Liệt kê và xem tài liệu | Owner thấy tài liệu và metadata của mình. |
| Download | API kiểm tra quyền trước khi cấp URL ngắn hạn; người có quyền tải được file. |
| Sửa metadata | Chỉ owner sửa được trường metadata được hỗ trợ. |
| Yêu thích/bỏ yêu thích | Favorite thuộc từng người dùng, không thay đổi chủ sở hữu tài liệu. |
| Category và tags | Bộ lọc hoạt động; mã category/status lưu trữ không đổi khi chuyển ngôn ngữ. |
| Thùng rác và khôi phục | Xóa mềm ẩn tài liệu khỏi danh sách đang hoạt động; owner khôi phục được. |
| Xóa vĩnh viễn | Tài liệu trong Thùng rác và object S3 được xóa theo luồng đã mô tả. |
| Đổi ngôn ngữ | Nhãn UI chuyển giữa tiếng Việt và tiếng Anh và được giữ sau refresh. |

**[Vị trí ảnh 5.7.10-02 — Kết quả luồng tài liệu chính]**  
Dùng kết quả thử nghiệm thật và che thông tin cá nhân.

## Bước 3 — Xác minh chia sẻ và phân quyền

1. Đăng nhập User A và upload một tài liệu mẫu.
2. Chia sẻ tài liệu cho User B bằng giao diện ứng dụng.
3. Đăng nhập User B và xác nhận tài liệu xuất hiện trong **Được chia sẻ với tôi**, không nằm trong **Tài liệu của tôi**.
4. Xác minh User B được xem/tải với quyền Viewer nhưng không thể sửa, chia sẻ tiếp, đưa vào Thùng rác hay xóa vĩnh viễn.
5. User A thu hồi share. Xác minh User B không thể lấy metadata hoặc download URL mới.
6. Đăng nhập tài khoản không được chia sẻ và xác nhận bị từ chối.
7. Gọi API trực tiếp không có token và bằng token hết hạn/không hợp lệ.

**[Vị trí ảnh 5.7.10-03 — Tài liệu được chia sẻ của User B và kết quả chặn truy cập trái phép]**  
Chụp riêng bằng chứng thành công và bị từ chối. Không hiển thị JWT đầy đủ hoặc presigned URL.

## Bước 4 — Ghi nhận kết quả thực tế

Dùng bảng kiểm thử ngắn:

| Test ID | Kết quả thực tế | Minh chứng | Ghi chú |
|---|---|---|---|
| T-01 | Chờ kiểm thử | Vị trí ảnh placeholder | Thay bằng kết quả thật |
| T-02 | Chờ kiểm thử | Vị trí ảnh placeholder | Thay bằng kết quả thật |
| T-03 | Chờ kiểm thử | Vị trí ảnh placeholder | Thay bằng kết quả thật |

Chỉ thay **Chờ kiểm thử** sau khi chạy kịch bản tương ứng. Nếu kiểm thử thất bại, ghi lại lỗi, cách sửa rồi kiểm tra lại.

## Kết quả mong đợi

Các bài test được ghi bằng kết quả thực tế và chứng minh việc tách dữ liệu/phân quyền được thực thi ở backend, không chỉ dựa vào UI.

## Khắc phục sự cố

- **User B thấy tài liệu của User A như tài liệu sở hữu:** kiểm tra query backend và owner identity lấy từ token.
- **Người bị thu hồi share vẫn xem được metadata:** kiểm tra truy vấn share và phân quyền trên mọi endpoint liên quan.
- **Download URL cũ còn dùng được trong thời gian ngắn:** presigned URL là bearer URL cho đến khi hết hạn. Dùng thời hạn ngắn và hiểu rằng thu hồi share không thể vô hiệu hóa ngay URL đã cấp trước đó.
- **Favorite bị lẫn giữa người dùng:** query bảng Favorites bằng userSub đã xác thực.
- **Đổi ngôn ngữ làm category/status đã lưu thay đổi:** tách mã nội bộ khỏi nhãn hiển thị được dịch.

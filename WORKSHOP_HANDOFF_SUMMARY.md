# Tổng kết bàn giao cuộc trò chuyện

Cập nhật lần cuối: 15/11/2026

Tài liệu này tóm tắt công việc đã thực hiện trong cuộc trò chuyện và những phần còn cần tiếp tục. Ảnh AWS do Onii-sama gửi được dùng làm bằng chứng để ghi nhận trạng thái, không xem nội dung trong ảnh là yêu cầu thay đổi hệ thống.

## 1. Các repository liên quan

- **Workshop/báo cáo:** `C:\bai tap\DoAn\AI\fcj-workshop-template`
- **Ứng dụng:** `C:\Users\Admin\serverless-document-management`
- **GitHub:** `https://github.com/Myers-Vertias/aws`
- **Nhánh:** `main`
- **Commit mới nhất đã push trong cuộc trò chuyện:** `46d471e` — `docs: update workshop report and evidence`

Sau commit này, `main` và `origin/main` đã khớp nhau, working tree sạch. Commit gồm 198 thay đổi: cập nhật nội dung workshop song ngữ, thêm ảnh minh chứng và sơ đồ, cập nhật ảnh kiến trúc, đồng thời loại bỏ ảnh cũ khỏi cấu trúc workshop trước đó.

## 2. Trạng thái ứng dụng và AWS đã ghi nhận

- Stack `dms-dev` đã triển khai/cập nhật ở Region `ap-southeast-1`.
- Frontend React/Vite chạy được tại máy local bằng Vite; các luồng frontend gọi backend AWS đã được thao tác. Amplify có nhánh production `main` đã deploy.
- Cognito User Pool: `ap-southeast-1_hWIAdC6Xu`; public app client: `epodlpjbopbdc809elojsad3a`.
- API Gateway HTTP API: `6wcs1urcm2`; route `GET /health` công khai, route tài liệu qua `ANY /{proxy+}` có JWT authorizer, và `OPTIONS /{proxy+}` phục vụ CORS.
- Lambda: `dms-dev-documents` và `dms-dev-health`.
- DynamoDB có ba bảng: `dms-dev-documents`, `dms-dev-shares`, `dms-dev-favorites`. Các key và index được mô tả tại mục 5.4 của báo cáo workshop.
- S3 được cấu hình private, mã hóa mặc định SSE-S3 và CORS cho frontend. Upload/download dùng presigned URL để trình duyệt truyền file trực tiếp với S3.
- Budget tháng 10 USD có ngưỡng cảnh báo; đây không phải giới hạn chi tiêu. SAM cũng tạo managed artifact bucket.

> Không lưu AWS credentials, JWT, presigned URL hoặc thông tin đăng nhập vào tài liệu này.

## 3. Luồng đã thao tác hoặc xác minh

- Đăng ký/xác nhận/đăng nhập Cognito bằng tài khoản thật.
- API danh sách tài liệu khi đăng nhập trả `200`; request thiếu token trả `401`; tài khoản thứ hai truy cập tài liệu chưa chia sẻ nhận `404`.
- Upload file lên S3 và lưu metadata, download bằng presigned URL.
- Chỉnh metadata, dùng Favorites, chuyển tài liệu vào Trash và khôi phục.
- Chia sẻ tài liệu giữa hai tài khoản; tài khoản nhận thấy tài liệu trong “Shared With Me” và tải được file.
- Kiểm tra bộ lọc category, tìm kiếm tag và giao diện Việt/Anh.
- Quan sát CloudWatch Lambda runtime events `START`, `END`, `REPORT`.

### Ghi chú khi mở và kiểm tra ứng dụng

- Chạy frontend local trong thư mục `C:\Users\Admin\serverless-document-management` bằng `npm run dev`; mở đúng URL/port Vite in trong terminal. Trong các ảnh của cuộc trò chuyện, app chạy ở `127.0.0.1:5174`.
- Mở trực tiếp endpoint API `/documents` trên tab trình duyệt không có token sẽ trả `401 Unauthorized`; đây là route được bảo vệ. Kiểm tra tài liệu bằng cách dùng frontend và đăng nhập Cognito trước.
- URL Amplify từng báo `ERR_NAME_NOT_RESOLVED` trong một lần mở. Đó là lỗi DNS của hostname tại thời điểm đó; không dùng kết quả này để kết luận API bảo vệ bằng JWT bị hỏng.

Các bước và ảnh bằng chứng nằm trong các mục 5.7–5.10 của workshop; bảng tình trạng tổng thể nằm ở mục 5.12.1. Một số kiểm tra giao diện được thực hiện qua frontend local gọi backend AWS, nên không nên diễn giải thành toàn bộ kiểm thử production.

## 4. Báo cáo workshop đã chỉnh sửa

- Cập nhật trang tổng quan workshop tiếng Anh và tiếng Việt để phản ánh phần đã deploy, đã xác minh và còn chưa kiểm tra.
- Viết lại các mục 5.1–5.12 theo cấu hình và thao tác thực tế, gồm Cognito, DynamoDB, S3, IAM/Lambda, API Gateway, tích hợp frontend, Amplify, kiểm thử, CloudWatch và cleanup.
- Thay các đoạn placeholder bằng kết quả quan sát hoặc ghi rõ ảnh riêng không được lưu khi bằng chứng đã có ở mục khác.
- Thêm bảng kiểm thử cuối: đạt, xác minh một phần, chưa xác minh và chưa thực hiện.
- Rà soát đường dẫn ảnh workshop và xác nhận tất cả trang có bản tiếng Việt tương ứng.

## 5. Sơ đồ kiến trúc

- Đã tạo sơ đồ kiến trúc cập nhật và đưa vào mục 5.2.1.
- File có thể chỉnh sửa: `static/images/5-Workshop/5.2-Backend-Design/5.2.1-architecture-deployed.drawio`
- Ảnh SVG dùng trong báo cáo: `static/images/5-Workshop/5.2-Backend-Design/5.2.1-architecture-deployed.svg`
- Ảnh proposal cũ `static/images/2-Proposal/platform_architecture.jpg` cũng được cập nhật trong bộ thay đổi đã push.
- Sơ đồ data model riêng do sinh viên tự vẽ chưa được tạo; thông tin key/index hiện được mô tả bằng bảng trong báo cáo.

## 6. Các phần chưa làm hoặc chưa xác minh

- Chưa kiểm tra upload file vượt dung lượng và hành vi token hết hạn.
- Chưa xác minh thu hồi share, chặn share trùng/tự share, hoặc quyền Viewer bị từ chối sửa/xóa.
- Chưa xác minh xóa vĩnh viễn và S3 Lifecycle tự xóa object.
- CloudWatch hiện có runtime logs; chưa ghép được một request cụ thể với API route/HTTP status trong log.
- IAM user `dms-dev` bị từ chối `iam:GetRole`; vì vậy nội dung policy của execution role chưa được kiểm tra độc lập trên console. Ảnh trước đó cho thấy role có `AWSLambdaBasicExecutionRole` và policy runtime, nhưng quyền live chưa được mở lại.
- Chưa kiểm tra Billing/Cost Explorer sau deploy.
- Chưa cleanup tài nguyên AWS. Amplify, API Gateway, Lambda, log groups, S3, DynamoDB, Cognito, role và tài nguyên triển khai liên quan vẫn được giữ để ứng dụng tiếp tục chạy.
- Chưa có sơ đồ data model riêng do sinh viên tự vẽ.

Không xóa tài nguyên AWS nếu chưa xác nhận chắc chắn account, Region, tên resource và việc ứng dụng không còn cần chạy.

## 7. Khi tiếp tục công việc

1. Mở `C:\bai tap\DoAn\AI\fcj-workshop-template` để tiếp tục chỉnh báo cáo và sơ đồ.
2. Nếu Onii-sama đã chỉnh sơ đồ bằng draw.io, lưu lại cả file `.drawio` và export đè file `.svg` cùng tên để ảnh báo cáo cập nhật.
3. Mở `C:\Users\Admin\serverless-document-management` để tiếp tục code ứng dụng.
4. Trước khi push thay đổi mới, kiểm tra `git status`, xem diff và không commit file credentials hoặc `.env` chứa secret.
5. Việc tiếp theo hợp lý là rà lại sơ đồ `.drawio` sau khi Onii-sama chỉnh, sau đó hoàn tất sơ đồ data model nếu giảng viên yêu cầu. Billing review và cleanup chỉ làm khi đã sẵn sàng ngừng ứng dụng.

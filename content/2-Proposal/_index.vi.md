---
title: "Bản đề xuất"
date: 2026-09-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Hệ thống quản lý tài liệu Serverless
## Giải pháp AWS Serverless thống nhất để lưu trữ và quản lý tài liệu an toàn

### 1. Tóm tắt điều hành
Hệ thống quản lý tài liệu Serverless (Serverless Document Management System - DMS) là một nền tảng web cho phép nhân viên và thành viên của tổ chức tải lên, lưu trữ, tìm kiếm và tải xuống tài liệu (PDF, DOCX, hình ảnh, v.v.) một cách an toàn từ bất kỳ đâu. Toàn bộ hệ thống được xây dựng trên các dịch vụ serverless của AWS: AWS Amplify host giao diện React/Next.js, Amazon Cognito xử lý xác thực, Amazon API Gateway và AWS Lambda thực hiện logic nghiệp vụ, Amazon S3 lưu file tài liệu, Amazon DynamoDB lưu metadata của tài liệu. Amazon CloudWatch đảm nhiệm giám sát và ghi log. Nền tảng không cần quản lý máy chủ và được thiết kế để vận hành với chi phí hằng tháng rất thấp.

### 2. Vấn đề cần giải quyết
### Vấn đề là gì?
Tài liệu thường nằm rải rác trên máy tính cá nhân, ứng dụng nhắn tin và email, khiến việc tìm kiếm khó khăn và dễ bị thất lạc. Không có một nơi lưu trữ tập trung, không có cơ chế kiểm soát nhất quán ai được xem hoặc chỉnh sửa tệp, và cũng không có bản ghi rõ ràng về chủ sở hữu của từng tài liệu. Các giải pháp truyền thống đòi hỏi phải vận hành và bảo trì một máy chủ riêng, trong khi các nền tảng quản lý tài liệu thương mại thì tốn kém và phức tạp hơn mức một nhóm nhỏ cần.

### Giải pháp
Amazon Cognito xác thực người dùng và cấp JWT token. Mọi request từ web app được host trên AWS Amplify đều đi qua Amazon API Gateway, nơi token được kiểm tra bằng Cognito authorizer trước khi gọi AWS Lambda. Lambda kiểm tra request, quản lý metadata tài liệu trong Amazon DynamoDB và tạo pre-signed URL có thời hạn ngắn để trình duyệt tải file lên hoặc tải xuống trực tiếp từ Amazon S3. Cách này giúp các file lớn không phải đi qua giới hạn payload của API Gateway và Lambda, nhờ đó tăng hiệu năng và giảm chi phí. Lambda chạy với IAM role theo nguyên tắc đặc quyền tối thiểu (least privilege), Amazon CloudWatch thu thập log, metrics và alarms từ API Gateway và Lambda, còn tích hợp GitHub cho phép AWS Amplify tự động deploy frontend mỗi khi có push mới. Các tính năng chính gồm đăng nhập an toàn, tải lên và tải xuống tài liệu, quản lý metadata (chủ sở hữu, tag, tên file, loại file, dung lượng, thời gian tạo và cập nhật) và kiểm soát truy cập.

### Lợi ích và hiệu quả đầu tư
Giải pháp mang lại cho người dùng một nơi tập trung và an toàn để quản lý tài liệu, giảm thời gian tìm kiếm file, đồng thời tạo nền tảng serverless có thể tái sử dụng để mở rộng sau này như tìm kiếm toàn văn (full-text search), quản lý phiên bản tài liệu hoặc phân tích tài liệu bằng AI. Dự án cũng là tài liệu học tập thực tế về các dịch vụ serverless cốt lõi của AWS. Vì mọi dịch vụ đều tính phí theo mức sử dụng và không phải mua phần cứng hay giấy phép, chi phí vận hành ước tính khoảng 0,94 USD mỗi tháng, tức 11,28 USD cho 12 tháng, dựa trên các giả định ở Mục 6, và phần lớn nằm trong hạn mức miễn phí (Free Tier) của AWS. Không cần đầu tư ban đầu, lợi ích đến từ việc tiết kiệm thời gian, tăng tính bảo mật cho tài liệu và khả năng tự động mở rộng khi nhu cầu sử dụng tăng.

### 3. Kiến trúc giải pháp
Nền tảng theo kiến trúc serverless trên AWS. Người dùng truy cập web app được host trên AWS Amplify và đăng nhập qua Amazon Cognito. Các request đi qua Amazon API Gateway đến AWS Lambda, nơi quản lý metadata trong Amazon DynamoDB và cấp pre-signed URL cho các file lưu trong Amazon S3. Amazon CloudWatch giám sát hệ thống, còn GitHub kết hợp AWS Amplify giúp deploy frontend tự động. Kiến trúc chi tiết như sau:

![Kiến trúc hệ thống quản lý tài liệu Serverless](../../static/images/2-Proposal/platform_architecture.jpg)

**Luồng xử lý request**
1. Người dùng mở web app được host trên AWS Amplify qua HTTPS.
2. Người dùng đăng nhập hoặc đăng ký qua Amazon Cognito và nhận JWT token.
3. Web app gửi REST API request kèm JWT đến Amazon API Gateway.
4. API Gateway kiểm tra token bằng Cognito authorizer rồi gọi AWS Lambda.
5. Lambda kiểm tra request và tạo, đọc, cập nhật hoặc xóa metadata trong Amazon DynamoDB.
6. Với thao tác tải lên và tải xuống, Lambda tạo pre-signed URL bằng IAM role của nó và trả về qua API Gateway.
7. Trình duyệt tải file lên hoặc tải xuống trực tiếp với Amazon S3 bằng pre-signed URL đó.
8. API Gateway và Lambda gửi log và metrics về Amazon CloudWatch.

### Các dịch vụ AWS sử dụng
- **AWS Amplify**: Host giao diện web React/Next.js và tự động deploy từ GitHub.
- **Amazon Cognito**: Xử lý đăng ký, đăng nhập và xác thực dựa trên JWT.
- **Amazon API Gateway**: Cung cấp REST API và phân quyền request bằng Cognito authorizer.
- **AWS Lambda**: Chạy logic nghiệp vụ gồm kiểm tra request, quản lý metadata và tạo pre-signed URL.
- **Amazon S3**: Lưu các file tài liệu (PDF, DOCX, hình ảnh, v.v.) trong một bucket riêng tư.
- **Amazon DynamoDB**: Lưu metadata tài liệu như chủ sở hữu, tag, tên file, loại file, dung lượng, thời gian và thông tin kiểm soát truy cập.
- **Amazon CloudWatch**: Thu thập log, metrics và alarms từ API Gateway và Lambda.
- **AWS IAM**: Cung cấp role và policy theo nguyên tắc đặc quyền tối thiểu cho Lambda và các tài nguyên khác.

### Thiết kế thành phần
- **Web Frontend**: Ứng dụng React/Next.js trên AWS Amplify, nơi người dùng đăng nhập, tải lên, duyệt, tìm kiếm và tải xuống tài liệu.
- **Xác thực**: Cognito user pool quản lý người dùng và cấp JWT token; có thể dùng user group để phân quyền theo vai trò.
- **Lớp API**: Amazon API Gateway nhận request từ frontend và từ chối mọi request không có token hợp lệ.
- **Logic nghiệp vụ**: Các hàm AWS Lambda xử lý thao tác với tài liệu và tạo pre-signed URL.
- **Lưu trữ file**: Amazon S3 lưu các file tài liệu; trình duyệt truyền file trực tiếp bằng pre-signed URL.
- **Lưu trữ metadata**: Amazon DynamoDB lưu mỗi tài liệu thành một item để tra cứu nhanh theo chủ sở hữu, tên hoặc tag.
- **Giám sát**: Amazon CloudWatch cung cấp log, metrics và alarms tập trung.
- **CI/CD**: Mỗi lần push lên GitHub sẽ kích hoạt AWS Amplify build và deploy frontend.

### 4. Triển khai kỹ thuật
**Các giai đoạn triển khai**
Dự án gồm bốn giai đoạn:
- Nghiên cứu và thiết kế: Tìm hiểu các dịch vụ serverless của AWS liên quan và vẽ kiến trúc (Tuần 1-2).
- Tính chi phí và kiểm tra tính khả thi: Dùng AWS Pricing Calculator để ước tính chi phí và xác nhận thiết kế đáp ứng yêu cầu (Tuần 3).
- Tinh chỉnh kiến trúc: Điều chỉnh thiết kế về bảo mật, chi phí và tính tiện dụng, ví dụ mô hình dữ liệu DynamoDB và IAM policy (Tuần 4-5).
- Phát triển, kiểm thử và triển khai: Xây dựng backend và frontend, kiểm thử tất cả thao tác với tài liệu và deploy lên môi trường production (Tuần 6-12).

**Yêu cầu kỹ thuật**
- **Frontend**: Ứng dụng React/Next.js host trên AWS Amplify, kết nối với repository GitHub để tự động build và deploy.
- **Xác thực**: Cognito user pool có đăng ký, đăng nhập và Cognito authorizer trên API Gateway.
- **API**: Các REST endpoint do API Gateway và Lambda phục vụ, ví dụ:
    - `POST /documents`: tạo metadata và trả về URL để upload.
    - `GET /documents`: liệt kê hoặc tìm kiếm tài liệu của người dùng.
    - `GET /documents/{id}`: lấy metadata và URL để download.
    - `PUT /documents/{id}`: cập nhật metadata.
    - `DELETE /documents/{id}`: xóa tài liệu và metadata của nó.
- **Mô hình metadata**: Một bảng DynamoDB với mỗi tài liệu là một item (document ID, chủ sở hữu, tag, tên file, loại file, dung lượng, S3 key, thời gian tạo và cập nhật, thuộc tính kiểm soát truy cập).
- **Lưu trữ**: S3 bucket riêng tư bật Block Public Access, mã hóa phía máy chủ và có CORS rule cho phép web app dùng pre-signed URL, với thời hạn URL ngắn (ví dụ 5-15 phút).
- **Bảo mật**: IAM role cho Lambda theo nguyên tắc đặc quyền tối thiểu, chỉ giới hạn các hành động S3 và DynamoDB cần thiết.
- **Giám sát**: CloudWatch log group cho API Gateway và Lambda, cùng các alarm cho lỗi và mức sử dụng bất thường.

### 5. Lộ trình và các mốc quan trọng
**Lộ trình dự án**
- Tuần 1-2: Nghiên cứu các dịch vụ serverless của AWS và thiết kế kiến trúc.
- Tuần 3: Ước tính chi phí bằng AWS Pricing Calculator và kiểm tra tính khả thi.
- Tuần 4-5: Tinh chỉnh kiến trúc (mô hình dữ liệu, bảo mật, chi phí).
- Tuần 6-10: Triển khai xác thực, API, các hàm Lambda, DynamoDB, S3 và frontend; kiểm thử từng tính năng.
- Tuần 11-12: Deploy, thiết lập giám sát, hoàn thiện tài liệu và trình bày demo cuối cùng.

### 6. Ước tính ngân sách
Bạn có thể xem ước tính ngân sách trên [AWS Pricing Calculator](https://calculator.aws/) (chèn link bản ước tính đã lưu của bạn vào đây).

**Giả định:** khoảng 10 người dùng, khoảng 5 GB tài liệu lưu trữ và khoảng 10.000 API request mỗi tháng.

### Chi phí hạ tầng
- Dịch vụ AWS:
    - AWS Lambda: 
    - Amazon API Gateway: 
    - Amazon DynamoDB:
    - Amazon S3 Standard:
    - Amazon Cognito: 
    - AWS Amplify Hosting: 
    - Amazon CloudWatch: 
    - Chuyển dữ liệu (Data Transfer):

Tổng: 

- Phần cứng: 

### 7. Đánh giá rủi ro
#### Ma trận rủi ro
- Truy cập trái phép: Tác động cao, xác suất thấp.
- Xóa nhầm hoặc mất dữ liệu: Tác động cao, xác suất thấp.
- Vượt ngân sách: Tác động trung bình, xác suất thấp.
- Cấu hình sai (CORS, IAM, API Gateway): Tác động trung bình, xác suất trung bình.
- Giới hạn dịch vụ và cold start của Lambda: Tác động thấp, xác suất trung bình.

#### Chiến lược giảm thiểu
- Truy cập: Xác thực bằng Cognito, Cognito authorizer, S3 bucket riêng tư, IAM đặc quyền tối thiểu và pre-signed URL có thời hạn ngắn.
- Mất dữ liệu: Bật S3 versioning và hạn chế quyền xóa.
- Chi phí: Cảnh báo AWS Budgets và alarm CloudWatch; giữ tài nguyên ở mô hình tính phí theo mức sử dụng.
- Cấu hình sai: Kiểm thử từng bước tích hợp và rà soát lại cài đặt IAM và CORS trước khi phát hành.
- Hiệu năng: Dùng pre-signed URL để file lớn không đi qua giới hạn payload của API Gateway và Lambda, đồng thời giữ các hàm nhỏ gọn.

#### Kế hoạch dự phòng
- Rollback frontend về bản build trước trong AWS Amplify nếu deploy bị lỗi.
- Dùng mẫu infrastructure-as-code (như AWS SAM hoặc CloudFormation) để deploy lại hoặc gỡ backend stack nhanh chóng.
- Khôi phục tài liệu từ các phiên bản S3 object trước đó nếu file bị xóa hoặc ghi đè nhầm.

### 8. Kết quả kỳ vọng
#### Cải thiện về kỹ thuật:
Một nền tảng tập trung và an toàn thay thế cho việc lưu tài liệu rải rác, với truy cập được xác thực, metadata có thể tìm kiếm và truyền file trực tiếp hiệu quả.  
Thiết kế serverless tự động mở rộng mà không cần bảo trì máy chủ.
#### Giá trị lâu dài
Nền tảng serverless có thể tái sử dụng và mở rộng với tìm kiếm toàn văn, quản lý phiên bản tài liệu, chia sẻ, thông báo hoặc phân tích bằng AI.  
Một dự án tham khảo thực tế về xây dựng ứng dụng an toàn, chi phí thấp trên AWS.
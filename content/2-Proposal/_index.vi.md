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
Giải pháp mang lại cho người dùng một nơi tập trung và an toàn để quản lý tài liệu, giảm thời gian tìm kiếm file, đồng thời tạo nền tảng serverless có thể tái sử dụng để mở rộng sau này như tìm kiếm toàn văn (full-text search), quản lý phiên bản tài liệu hoặc phân tích tài liệu bằng AI. Dự án cũng là tài liệu học tập thực tế về các dịch vụ serverless cốt lõi của AWS. Các dịch vụ tính phí theo mức sử dụng và không cần mua phần cứng hay giấy phép. Với các giả định ở Mục 6, AWS Pricing Calculator ước tính chi phí trước chiết khấu khoảng 1,19 USD mỗi tháng, tương đương 14,24 USD cho 12 tháng nếu mức sử dụng không đổi. Đây là con số phục vụ lập kế hoạch, không phải hóa đơn; hạn mức miễn phí áp dụng được, chiết khấu, thuế và lưu lượng thực tế có thể làm thay đổi số tiền bị tính. Không cần đầu tư ban đầu, lợi ích đến từ việc tiết kiệm thời gian, tăng tính bảo mật cho tài liệu và khả năng tự động mở rộng khi nhu cầu sử dụng tăng.

### 3. Kiến trúc giải pháp
Nền tảng theo kiến trúc serverless trên AWS. Người dùng truy cập web app được host trên AWS Amplify và đăng nhập qua Amazon Cognito. Các request đi qua Amazon API Gateway đến AWS Lambda, nơi quản lý metadata trong Amazon DynamoDB và cấp pre-signed URL cho các file lưu trong Amazon S3. Amazon CloudWatch giám sát hệ thống, còn GitHub kết hợp AWS Amplify giúp deploy frontend tự động. Kiến trúc chi tiết như sau:

![Kiến trúc hệ thống quản lý tài liệu Serverless](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/2-Proposal/platform_architecture.jpg)

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
Ước tính được xuất từ AWS Pricing Calculator ngày 18/11/2026 cho Region Asia Pacific (Singapore). Đây là ước tính theo tháng, trước chiết khấu và bao gồm các dịch vụ dưới đây. Chi phí năm được tính bằng chi phí tháng nhân 12, với giả định mức sử dụng mỗi tháng không đổi.

### Chi phí hạ tầng
| Dịch vụ AWS | Ước tính mỗi tháng (USD) |
| --- | ---: |
| AWS Lambda | $0.0062 |
| Amazon API Gateway (HTTP API) | $0.0125 |
| Amazon DynamoDB (on-demand) | $0.0020 |
| Amazon S3 Standard | $0.1257 |
| Amazon Cognito (Essentials, 10 MAU) | $0.0550 |
| AWS Amplify Hosting | $0.3500 |
| Amazon CloudWatch | $0.0352 |
| Truyền dữ liệu AWS | $0.6000 |
| **Tổng ước tính** | **$1.1866 (~$1.19)** |

**Tổng ước tính mỗi năm:** $14.24 USD (chi phí tháng × 12).

**Phần cứng:** $0; giải pháp sử dụng các dịch vụ AWS được quản lý.

**Giả định sử dụng mỗi tháng:** 10.000 request API Gateway HTTP API; 10.000 request Lambda và 250 Lambda GB-second; DynamoDB gồm 4.000 read request unit nhất quán cuối cùng, 2.000 write request unit tiêu chuẩn, kích thước trung bình mỗi item 1 KB và 0,1 GB dữ liệu bảng; S3 Standard lưu 5 GB, 100 request ghi/liệt kê và 500 request đọc; 10 người dùng hoạt động hằng tháng trên Cognito; Amplify có 20 phút build, phục vụ 1 GB và lưu 0,002 GB; CloudWatch ingest 0,05 GB standard logs và lưu log trong một tháng; truyền 5 GB dữ liệu ra Internet. Các loại sử dụng tùy chọn khác trong calculator được loại khỏi ước tính. Đây là giả định để lập kế hoạch, không phải số liệu đo từ môi trường production.

Ước tính chưa trừ chiết khấu và không xác định hóa đơn thực tế. Hạn mức miễn phí đủ điều kiện, ưu đãi riêng của tài khoản, thuế và mức sử dụng ngoài giả định có thể làm thay đổi chi phí thực tế. Ước tính giả định log CloudWatch được lưu một tháng; cần cấu hình thời hạn lưu của các log group đã triển khai tương ứng nếu muốn chi phí lưu trữ phản ánh hệ thống đang chạy. Sau khi deploy, hãy theo dõi usage và hóa đơn AWS, đồng thời thiết lập cảnh báo AWS Budgets trước khi tăng lưu lượng hoặc dung lượng lưu trữ.

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

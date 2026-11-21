---
title: "S3 Presigned URL: Tải file thẳng lên S3 có an toàn không?"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# S3 Presigned URL: Tải file thẳng lên S3 có an toàn không?

Một tình huống quen thuộc khi xây dựng ứng dụng quản lý tài liệu: người dùng chọn file, frontend gửi file đến backend, rồi Lambda chuyển tiếp file lên Amazon S3. Cách này hoạt động, nhưng backend phải tiếp nhận và truyền toàn bộ nội dung file.

Có cách nào để trình duyệt tải file trực tiếp lên S3 mà không cần cấp AWS credentials cho người dùng không? Một lựa chọn phổ biến là **presigned URL**. Nhưng URL này cấp quyền gì, có dùng lại được không, và cần lưu ý điều gì nếu URL bị lộ?

## 01 | PRESIGNED URL CẤP QUYỀN GÌ?

Presigned URL là một HTTP request đến S3 được ký bằng credentials của một IAM principal. Ứng dụng có thể gửi URL cho client để thực hiện một thao tác cụ thể trong khoảng thời gian nhất định, chẳng hạn `PUT` để tải lên hoặc `GET` để tải xuống.

Client không cần giữ AWS access key. Request vẫn bị giới hạn bởi quyền của principal đã tạo URL, cùng bucket, object key, HTTP method và thời hạn được ký. Nói cách khác, đây là cách trao quyền tạm thời để thực hiện request đã định trước, không phải một cơ chế phân quyền người dùng độc lập.

AWS xem presigned URL là một **bearer token**: bất kỳ ai có URL đều có thể dùng nó trong thời gian còn hiệu lực. Vì vậy, cần bảo vệ URL như thông tin xác thực. [1]

## 02 | LUỒNG TẢI FILE TRỰC TIẾP LÊN S3

Với ứng dụng quản lý tài liệu, luồng tải lên có thể diễn ra như sau:

1. Người dùng đăng nhập và yêu cầu tải tài liệu lên.
2. Backend xác thực danh tính, kiểm tra quyền nghiệp vụ và tự tạo object key duy nhất, ví dụ `incoming/user-123/uuid.pdf`.
3. Backend dùng IAM role của mình để tạo presigned URL cho đúng bucket, object key, phương thức HTTP và thời hạn.
4. Backend trả URL cho trình duyệt.
5. Trình duyệt dùng request `PUT` gửi file trực tiếp đến S3.
6. Sau khi S3 nhận object, hệ thống có thể kích hoạt bước kiểm tra file và cập nhật trạng thái tài liệu.

Trong luồng này, Lambda xác thực và cấp quyền tạm thời, còn nội dung file đi từ trình duyệt thẳng đến S3. Bucket vẫn có thể ở chế độ private; presigned URL chỉ cho phép request được ký truy cập object đã chỉ định trong thời hạn cho phép. [1][2]

## 03 | URL KHÔNG TỰ ĐỘNG CHỈ DÙNG ĐƯỢC MỘT LẦN

Một điểm dễ bỏ sót: presigned URL có thể được sử dụng nhiều lần cho đến khi hết hạn. Nếu object cùng key đã tồn tại, một lần `PUT` khác có thể thay thế object đó. [1][2]

Vì vậy, không nên để client tùy chọn một key dùng chung như `uploads/report.pdf`. Backend nên tạo key khó đoán và duy nhất cho mỗi lần tải lên, gắn key với người dùng hoặc bản ghi nghiệp vụ, đồng thời chỉ ký đúng thao tác cần thiết.

Nếu cần giới hạn dung lượng trong một khoảng cụ thể, hãy cân nhắc **presigned POST** với policy có điều kiện `content-length-range`. Không nên mặc định rằng presigned `PUT` tự đáp ứng được mọi yêu cầu giới hạn kích thước của ứng dụng. [3]

## 04 | THỜI HẠN URL CÒN PHỤ THUỘC CREDENTIALS KÝ

Presigned URL có thời hạn do ứng dụng cấu hình, nhưng không thể sống lâu hơn credentials dùng để ký. Nếu URL được ký bằng credentials tạm thời từ IAM role, URL có thể mất hiệu lực khi phiên credentials kết thúc, dù thời hạn khai báo dài hơn.

AWS CLI và SDK cho phép đặt thời hạn tối đa đến 7 ngày trong các điều kiện tương ứng. Đây là mức tối đa, không phải cấu hình mặc định phù hợp cho mọi trường hợp. Hãy chọn thời gian vừa đủ cho thao tác và cân nhắc khả năng người dùng cần thử lại. URL sống càng lâu thì cửa sổ sử dụng nếu bị lộ càng dài. [1]

## 05 | CORS GIÚP TRÌNH DUYỆT GỌI S3, KHÔNG XÁC THỰC NGƯỜI DÙNG

Khi frontend và bucket thuộc các origin khác nhau, trình duyệt có thể gửi preflight request. Cấu hình CORS của bucket cần cho phép đúng origin, method như `PUT` và các request header mà ứng dụng thực sự gửi.

Nhưng CORS chỉ quy định cách trình duyệt cho phép JavaScript gọi tài nguyên khác origin. Nó không xác thực người dùng, không thay thế IAM hoặc bucket policy, và không ngăn một HTTP client khác sử dụng URL đã bị lộ. Các quyền và chính sách S3 vẫn tiếp tục được áp dụng. [4]

Một lỗi thường gặp là `SignatureDoesNotMatch`. Request tải lên phải khớp với những gì đã được ký, gồm HTTP method và các header liên quan. Ví dụ, nếu `Content-Type` được đưa vào lúc tạo URL, client cần gửi đúng giá trị đó khi tải file lên. Khi gặp lỗi, hãy kiểm tra URL có bị thay đổi không, đã hết hạn chưa, đúng region chưa và các header có khớp không. [2]

## 06 | TẢI LÊN THÀNH CÔNG CHƯA CÓ NGHĨA FILE ĐÃ AN TOÀN

Presigned URL giúp chuyển file đến S3, nhưng không tự xác nhận file có đúng định dạng nghiệp vụ, có an toàn hay người dùng có quyền xem file sau đó hay không.

Một thiết kế có thể tải file vào prefix như `incoming/`, sau đó chuyển qua bước kiểm tra và cập nhật trạng thái thành `PENDING`, `READY` hoặc `REJECTED`. Khi tải xuống, backend vẫn cần xác thực lại quyền người dùng và trạng thái tài liệu trước khi cấp URL phù hợp.

Nếu dùng S3 Event Notifications để bắt đầu xử lý sau upload, cần tính đến việc sự kiện được phân phối theo mô hình **at-least-once**, có thể đến trễ, bị gửi trùng và không được bảo đảm theo đúng thứ tự phát sinh. Consumer nên chịu được retry và xử lý lặp an toàn, chẳng hạn dựa trên object key kết hợp version hoặc một định danh xử lý đã lưu. Đồng thời, tránh để Lambda ghi object vào chính prefix đang kích hoạt nó nếu chưa có cơ chế ngăn vòng lặp. [5]

Với dữ liệu cần kiểm tra tính toàn vẹn khi truyền, SigV4 presigned upload hỗ trợ checksum header để S3 so sánh checksum với nội dung nhận được. Checksum giúp phát hiện dữ liệu không khớp, nhưng không thay thế bước kiểm tra nội dung hay quét mã độc. [1][6]

## 07 | CHECKLIST TRƯỚC KHI ĐƯA VÀO PRODUCTION

① Backend đã xác thực danh tính và quyền trước khi cấp URL chưa?

② Object key do backend tạo, không dùng chung và nằm trong vùng lưu trữ dự kiến chưa?

③ Bucket vẫn private, IAM role chỉ có quyền cần thiết và URL có thời hạn phù hợp chưa?

④ Đã tính đến URL bị lộ, tải lại cùng key, CORS, header được ký và checksum chưa?

⑤ File có được kiểm tra sau upload, và worker có xử lý an toàn khi nhận sự kiện trùng hoặc đến sai thứ tự không?

## KẾT LUẬN

Presigned URL giúp trình duyệt tải file trực tiếp lên S3 mà không cần giữ AWS credentials và không phải chuyển toàn bộ nội dung file qua Lambda. Đổi lại, URL cần được xem như một quyền truy cập tạm thời: người sở hữu nó có thể thực hiện request đã ký cho đến khi URL hoặc credentials liên quan hết hiệu lực.

Một luồng an toàn vì thế không dừng ở bước “tạo URL”. Backend phải quyết định ai được tải lên, object key nào được phép dùng và URL sống trong bao lâu. Hệ thống cũng cần kiểm tra file sau khi nhận và xử lý sự kiện theo cách chịu được retry.

**Presigned URL giúp trao một request có giới hạn. Quyền nghiệp vụ vẫn phải được kiểm soát ở ứng dụng.**

#AWS #AmazonWebServices #CloudComputing #CloudArchitecture #AmazonS3 #AWSLambda #Serverless #CloudSecurity #PresignedURL #AWSSecurity #AWSStudyGroupVN

### Nguồn tham khảo

1. [Download and upload objects with presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html)
2. [Uploading objects with presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html)
3. [POST Policy](https://docs.aws.amazon.com/AmazonS3/latest/developerguide/sigv4-HTTPPOSTConstructPolicy.html)
4. [Configuring cross-origin resource sharing (CORS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)
5. [Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html)
6. [Checking object integrity in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity-upload.html)

### Thông tin bài đăng

https://www.facebook.com/groups/awsstudygroupfcj/permalink/2345064529591839/?rdid=KmHzDedRJD6NcuJ7#

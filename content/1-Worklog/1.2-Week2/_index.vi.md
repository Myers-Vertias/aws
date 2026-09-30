---

title: "Nhật ký tuần 2"

date: 2026-09-16

weight: 1

chapter: false

pre: " <b> 1.2. </b> "

---

### Mục tiêu tuần 2:

* Củng cố kiến thức thực hành về các dịch vụ AWS và quản lý tài nguyên Cloud thông qua các workshop thực hành.

* Hiểu cách ứng dụng truy cập an toàn đến các dịch vụ AWS thông qua IAM Role thay vì sử dụng Access Key và Secret Access Key trực tiếp trong ứng dụng.

* Làm quen với AWS Cloud9 như một môi trường phát triển trên trình duyệt và thực hành các thao tác cơ bản với AWS CLI.

* Tìm hiểu Amazon S3 về lưu trữ object, hosting website tĩnh, kiểm soát quyền truy cập, versioning và phân phối nội dung.

* Hiểu Amazon RDS là một dịch vụ cơ sở dữ liệu quan hệ được AWS quản lý và thực hành kết nối ứng dụng chạy trên EC2 với cơ sở dữ liệu RDS.

* Có kinh nghiệm thực hành triển khai, kiểm thử, giám sát, sao lưu và dọn dẹp tài nguyên AWS.

### Các công việc thực hiện trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| Thứ 4 | **IAM Role & AWS Cloud9**<br>• Tìm hiểu cách ứng dụng truy cập các dịch vụ AWS thông qua Access Key và Secret Access Key.<br>• Hiểu các rủi ro bảo mật khi nhúng long-term credentials trực tiếp vào ứng dụng.<br>• Tìm hiểu IAM Role dành cho EC2 và cách cung cấp quyền tạm thời cho ứng dụng thông qua Instance Role.<br>• **Thực hành:** Tạo EC2 instance, tạo IAM Role, gán các quyền cần thiết và liên kết Role với EC2.<br>• Tìm hiểu AWS Cloud9 như một môi trường phát triển tích hợp trên trình duyệt.<br>• Thực hành các thao tác cơ bản trên terminal, quản lý file và sử dụng các lệnh AWS CLI trong Cloud9. | 09/16/2026 | 09/16/2026 | [AWS Cloud Journey](https://cloudjourney.awsstudygroup.com/vi/) |
| Thứ 6 | **Amazon S3 & Amazon RDS**<br>• Tìm hiểu các khái niệm về Amazon S3: Bucket, Object, kiểm soát quyền truy cập và quản lý lưu trữ.<br>• **Thực hành:** Tạo S3 Bucket, upload object, cấu hình Static Website Hosting, kiểm tra Block Public Access và các thiết lập truy cập public, đồng thời tìm hiểu Versioning.<br>• Tìm hiểu cách Amazon S3 kết hợp với CloudFront để phân phối nội dung.<br>• Tìm hiểu Amazon RDS là dịch vụ cơ sở dữ liệu quan hệ được AWS quản lý và các khái niệm về database engine, connectivity, security, backup và Multi-AZ.<br>• **Thực hành:** Tạo VPC, Security Group, DB Subnet Group và MySQL RDS instance.<br>• Tạo EC2 instance và kết nối bằng SSH.<br>• Triển khai ứng dụng Node.js trên EC2 và cấu hình ứng dụng kết nối tới cơ sở dữ liệu RDS.<br>• Thực hành kiểm tra kết nối RDS, triển khai ứng dụng, sao lưu/phục hồi cơ sở dữ liệu và dọn dẹp tài nguyên. | 09/18/2026 | 09/18/2026 | [AWS Cloud Journey](https://cloudjourney.awsstudygroup.com/vi/) |

### Kết quả đạt được trong tuần 2:

* Củng cố kiến thức thực hành về quản lý các dịch vụ AWS thông qua các workshop.

* Tìm hiểu cách sử dụng IAM Role để cung cấp quyền cho các ứng dụng chạy trên EC2 truy cập các dịch vụ AWS mà không cần lưu trữ long-term credentials trong ứng dụng.

* Hiểu sự khác biệt giữa Access Key và IAM Role, đồng thời nhận thức được tầm quan trọng của việc quản lý credential an toàn.

* Thực hành tạo và gắn IAM Role cho EC2 instance.

* Làm quen với AWS Cloud9 như một môi trường phát triển trên trình duyệt.

* Thực hành sử dụng terminal của Cloud9 và thực hiện các thao tác quản lý file cơ bản.

* Thực hành các thao tác cơ bản với AWS CLI từ môi trường phát triển trên Cloud9.

* Nắm được các khái niệm cơ bản của Amazon S3, bao gồm:

  * Bucket
  * Object
  * Object Storage
  * Access Control
  * Block Public Access

* Thực hành tạo S3 Bucket và upload object.

* Tìm hiểu cách sử dụng Amazon S3 để hosting website tĩnh.

* Tìm hiểu cấu hình bảo mật và các thiết lập public access của S3.

* Tìm hiểu S3 Versioning và vai trò của nó trong việc bảo vệ dữ liệu object và duy trì các phiên bản trước đó.

* Tìm hiểu mối liên hệ cơ bản giữa Amazon S3 và CloudFront trong việc phân phối nội dung.

* Nắm được kiến thức nền tảng về Amazon RDS với vai trò là một dịch vụ cơ sở dữ liệu quan hệ được AWS quản lý.

* Tìm hiểu các khái niệm quan trọng của RDS, bao gồm:

  * Database Engines
  * DB Instances
  * Endpoints và Ports
  * DB Subnet Groups
  * Security Groups
  * Multi-AZ
  * Read Replicas
  * Automated Backups
  * DB Snapshots

* Thực hành tạo MySQL RDS database instance trong môi trường VPC tùy chỉnh.

* Thực hành cấu hình các thành phần networking cần thiết cho RDS, bao gồm VPC, Subnet, DB Subnet Group và Security Group.

* Thực hành tạo EC2 instance và kết nối tới EC2 bằng SSH.

* Triển khai ứng dụng Node.js trên EC2 và cấu hình ứng dụng kết nối tới cơ sở dữ liệu MySQL trên RDS.

* Thực hành xử lý các vấn đề liên quan đến kết nối ứng dụng và cơ sở dữ liệu, bao gồm:

  * Cấu hình sai Database Host
  * Các vấn đề kết nối tới RDS
  * Lỗi cấu hình Security Group
  * Process ứng dụng bị lỗi
  * Lỗi kết nối tới database thông qua port 3306

* Tìm hiểu cách giám sát tài nguyên RDS và các tùy chọn sao lưu, phục hồi cơ sở dữ liệu.

* Có kinh nghiệm thực hành quản lý tài nguyên AWS từ quá trình triển khai, kiểm thử, xử lý sự cố đến sao lưu và dọn dẹp.

* Hiểu rõ hơn cách IAM, Compute, Storage, Development Environment và Managed Database kết hợp với nhau để hỗ trợ triển khai các ứng dụng trên môi trường Cloud.
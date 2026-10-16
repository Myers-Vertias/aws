---
title: "Điều kiện tiên quyết và kiểm soát chi phí"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

## Mục tiêu

Chuẩn bị môi trường phát triển và tài khoản AWS trước khi tạo tài nguyên. Bước này không tạo tài nguyên AWS nào.

## Công cụ và quyền cần có

- Tài khoản AWS có quyền tạo các dịch vụ trong workshop. Dùng IAM identity hoặc role cho công việc hằng ngày; không tạo hay sử dụng root access key.
- Quyền truy cập AWS Billing. Xem giá hiện hành và cân nhắc tạo cảnh báo ngân sách trước khi triển khai.
- Một bản Node.js LTS còn được hỗ trợ, npm, Git, trình soạn thảo mã nguồn và trình duyệt hiện đại.
- Quyền truy cập repository mã nguồn UniCloud DMS và quyền tạo branch làm việc.
- Chọn một AWS Region. Dùng cùng Region cho Cognito, DynamoDB, Lambda, API Gateway và S3, trừ khi có lý do cụ thể cần khác.

{{% notice warning %}}
Một số tài nguyên và thao tác có thể phát sinh chi phí, bao gồm truyền dữ liệu, lưu trữ object, request, tiếp nhận log và hosting. Điều kiện Free Tier tùy tài khoản và dịch vụ. Hãy kiểm tra bảng giá AWS hiện hành và trạng thái billing trước khi tạo tài nguyên.
{{% /notice %}}

## Bước 1 — Kiểm tra frontend trên máy

Mở terminal tại thư mục repository UniCloud DMS và chạy:

~~~powershell
npm ci
npm run lint
npm run build
npm run dev
~~~

Mở URL cục bộ mà Vite in ra để xem frontend hiện tại. Xác nhận mock prototype chạy được trước khi kết nối AWS. Không dán credentials hoặc token vào file mã nguồn.

**[Vị trí ảnh 5.7.1-01 — Frontend chạy cục bộ]**  
Chụp màn hình ứng dụng đang chạy với một trang đại diện. Thay thông báo này bằng ảnh thật sau khi kiểm thử.

## Bước 2 — Chuẩn bị cách quản lý cấu hình an toàn

- Dùng môi trường development riêng và tiền tố tên tài nguyên rõ ràng, ví dụ **unicloud-dms-dev**.
- Lưu cấu hình frontend trong biến môi trường như file cấu hình Vite; chỉ commit file ví dụ đã loại bỏ thông tin nhạy cảm.
- Không đưa AWS secret access key, session token, credentials riêng tư hoặc JWT đầy đủ vào mã React hay Git.
- Ghi lại Region đã chọn và tên tài nguyên trong ghi chú lab cục bộ không chứa secret.

**[Vị trí ảnh 5.7.1-02 — Region và cấu hình không nhạy cảm]**  
Chụp màn hình Region đã chọn hoặc ví dụ cấu hình đã làm sạch. Không để lộ thông tin tài khoản nhạy cảm nếu không cần thiết.

## Kết quả mong đợi

Frontend build được trên máy, các công cụ sẵn sàng, Region AWS đã được chọn và người học hiểu rằng tài nguyên có thể phát sinh chi phí.

## Khắc phục sự cố

- Nếu lệnh npm thất bại, kiểm tra phiên bản Node.js, thư mục repository và việc cài dependency.
- Nếu website chạy ở cổng khác 5173, hãy dùng URL do Vite hiển thị.
- Nếu AWS trả về AccessDenied, yêu cầu quản trị viên cấp đúng quyền cần cho bước hiện tại thay vì gắn quyền quản trị toàn phần.

## Tổng kết

Bước này không tạo tài nguyên AWS. Hãy chuyển sang thiết kế backend trước khi tạo bảng, bucket hoặc function.

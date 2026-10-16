---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Tổng quan Workshop

Chương này tập hợp các bài thực hành AWS. Các bài lab về truy cập S3 riêng tư thông qua VPC Endpoint được giữ nguyên, đồng thời bổ sung workshop xây dựng **UniCloud DMS** — ứng dụng quản lý tài liệu theo kiến trúc serverless.

Workshop UniCloud DMS hướng dẫn người học phát triển từ frontend React/Vite hiện có thành ứng dụng sử dụng dịch vụ AWS cho xác thực, lưu trữ đối tượng riêng tư, REST API và xử lý nghiệp vụ serverless.

{{% notice warning %}}
**Trạng thái triển khai:** Các hướng dẫn UniCloud DMS là kế hoạch thực hành cho đến khi từng bước AWS được thực hiện và kiểm chứng. Những vị trí đánh dấu ảnh chụp màn hình là có chủ đích; hãy thay bằng ảnh thật từ môi trường của người học sau khi hoàn thành bước tương ứng.
{{% /notice %}}

#### Nội dung workshop

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Điều kiện tiên quyết](5.2-Prerequiste/)
3. [Truy cập S3 từ VPC](5.3-S3-vpc/)
4. [Truy cập S3 từ môi trường on-premises](5.4-S3-onprem/)
5. [VPC Endpoint Policy (mở rộng)](5.5-Policy/)
6. [Dọn dẹp lab S3 hybrid](5.6-Cleanup/)
7. [Xây dựng UniCloud DMS trên AWS](5.7-UniCloud-DMS/)

Workshop UniCloud DMS bao gồm xác thực, metadata trên DynamoDB, lưu trữ S3 riêng tư, Lambda, API Gateway, tích hợp frontend, hosting, kiểm thử, giám sát và dọn dẹp. Hãy thực hiện các bước theo thứ tự và sử dụng cùng một AWS Region cho toàn bộ tài nguyên, trừ khi hướng dẫn yêu cầu khác.

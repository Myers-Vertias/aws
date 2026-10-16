---
title: "Cấu hình CORS và truyền file bằng Presigned URL"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.5.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Chỉ cho phép frontend origin cần thiết upload/download trực tiếp từ trình duyệt, đồng thời giữ S3 riêng tư.

## Các bước thực hiện

1. Cấu hình S3 CORS cho đúng Vite origin cục bộ và Amplify origin triển khai; chỉ cho phép method/header cần thiết.
2. Lambda xác minh danh tính, dung lượng, loại file và metadata trước khi trả PUT URL ngắn hạn với key do server tạo.
3. Trình duyệt upload với content type đã ký; endpoint hoàn tất xác minh object trước khi chốt metadata.
4. Khi download, Lambda kiểm tra quyền owner/share còn hiệu lực rồi cấp GET URL ngắn hạn. Không log hay công khai URL.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.5.2 — CORS đã lưu và kiểm thử upload/download thành công, đã che signed URL.]**

## Kết quả mong đợi

Trình duyệt truyền được file thử nghiệm qua presigned URL mà không public bucket.

## Khắc phục sự cố

- Khi lỗi, kiểm tra origin, method, header, thời hạn URL và content type đã ký.
- CORS không phải phân quyền; backend vẫn phải xác minh quyền.

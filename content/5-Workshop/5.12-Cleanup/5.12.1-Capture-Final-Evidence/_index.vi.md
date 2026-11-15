---
title: "Lưu Minh chứng Cuối cùng"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.12.1 </b> "
---

{{% notice info %}}
Trang này ghi nhận cấu hình đã triển khai và kết quả đã quan sát; hành vi chưa xác minh được nêu rõ.
{{% /notice %}}

## Mục tiêu

Thu thập ảnh thật và bản ghi kiểm thử trước khi xóa tài nguyên.

## Minh chứng đã thu thập

Báo cáo dùng lại ảnh chụp từ các bước đã làm thay vì lặp nhiều ảnh gần giống nhau. Minh chứng cấu hình nằm ở mục 5.2–5.9; giao diện ứng dụng và các luồng tài khoản thật nằm ở mục 5.8 và 5.10; quan sát CloudWatch ở mục 5.11. Một số thông tin tài khoản, role ARN và chi tiết tài liệu đã được che trong bản báo cáo khi cần.

## Bảng tổng kết xác minh

| Hạng mục | Kết quả | Minh chứng / giới hạn |
|---|---|---|
| SAM validate/build và stack đã deploy | Đã xác minh | Mục 5.1.1, 5.2.2 và 5.9.2 |
| Cognito đăng ký, xác nhận email và đăng nhập | Đã xác minh | Mục 5.3.1, 5.3.2 và 5.8.2 |
| API authorization | Đã xác minh | List có đăng nhập `200`, thiếu token `401`, tài liệu của tài khoản khác chưa share trả `404`; mục 5.7.2 |
| Upload, download, sửa metadata, chia sẻ, Favorites, Trash/khôi phục | Đã xác minh | Mục 5.8.2 và 5.10.1–5.10.3 |
| VI/EN, lọc danh mục và tìm tag | Đã xác minh | Mục 5.10.3; frontend local được nêu rõ ở các bước tương ứng |
| CloudWatch runtime log | Xác minh một phần | Đã thấy `START`/`END`/`REPORT`; chưa đối chiếu được route/status; mục 5.11.1 |
| Upload quá giới hạn, token hết hạn, thu hồi share/share trùng/tự share, Viewer bị chặn ghi | Chưa xác minh | Không ghi nhận kết quả; xem mục 5.6.3 và ghi chú test liên quan |
| Xóa vĩnh viễn và S3 lifecycle expiration | Chưa xác minh | Chưa thực hiện hành vi xóa/đợi hết hạn; không khẳng định kết quả |
| Kiểm tra billing sau deploy và cleanup resource | Chưa làm | Resource vẫn được giữ; xem mục 5.12.2 |

Không chụp một ảnh tổng hợp cuối hoặc dashboard riêng. Các ảnh ở những mục được dẫn trên là hồ sơ minh chứng cuối cùng của lần thực hiện này.

## Kết quả mong đợi

Báo cáo phân biệt phần đã triển khai, kết quả đã quan sát, quan sát một phần và phần chưa kiểm thử.

## Khắc phục sự cố

- Không tạo ảnh minh họa giả hoặc ghi kết quả mong đợi thành kết quả thật.
- Giữ ảnh báo cáo và mã nguồn sau khi xóa cloud resource.

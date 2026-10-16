---
title: "Kiểm thử Chia sẻ và Phân quyền với Hai Người dùng"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.10.2 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Xác minh quyền Owner/Viewer, tách Tài liệu của tôi khỏi Được chia sẻ với tôi và thu hồi share.

## Các bước thực hiện

1. User A upload tài liệu mẫu và chia sẻ cho User B.
2. Đăng nhập User B, xác nhận tài liệu nằm trong Được chia sẻ với tôi, không nằm trong Tài liệu của tôi; xem/tải được nhưng sửa/chia sẻ/xóa bị chặn.
3. User A thu hồi quyền. Xác nhận User B không lấy được metadata hoặc download URL mới.
4. Gọi API trực tiếp khi thiếu token và bằng người dùng thứ ba. Ghi response thực tế.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.10.2 — Danh sách được chia sẻ của User B và thao tác bị từ chối sau khi thu hồi share.]**

## Kết quả mong đợi

Chỉ owner và viewer còn quyền truy cập tài liệu theo đúng quy tắc.

## Khắc phục sự cố

- Presigned URL đã cấp trước đó còn dùng được đến khi hết hạn; hãy đặt thời hạn ngắn.
- Đảm bảo Lambda thực thi phân quyền, không chỉ React.

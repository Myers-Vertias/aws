---
title: "Kiểm thử Favorites, Tags, Thùng rác và VI/EN"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.10.3 </b> "
---

{{% notice info %}}
Đây là khung hướng dẫn. Chỉ thay placeholder bằng minh chứng sau khi đã thực hiện và xác minh bước tương ứng.
{{% /notice %}}

## Mục tiêu

Xác minh feature theo người dùng, mã lưu trữ ổn định, xóa mềm và ghi nhớ ngôn ngữ.

## Các bước thực hiện

1. Kiểm thử favorite/bỏ favorite bằng hai người dùng và xác nhận trạng thái riêng theo user.
2. Đặt category/tags, tìm kiếm/lọc và xác minh giá trị lưu không đổi khi đổi ngôn ngữ UI.
3. Đưa tài liệu sở hữu vào Thùng rác, khôi phục và chỉ thử xóa vĩnh viễn với dữ liệu có thể bỏ.
4. Chuyển tiếng Việt/English, refresh và đi qua mọi trang; ghi lại nhãn còn trộn ngôn ngữ.

## Vị trí ảnh chụp màn hình

**[Vị trí ảnh 5.10.3 — Kiểm thử Favorites/Thùng rác và cùng trang UI bằng tiếng Việt, tiếng Anh.]**

## Kết quả mong đợi

Hành vi feature, tính ổn định dữ liệu và ghi nhớ ngôn ngữ có kết quả test thực tế.

## Khắc phục sự cố

- Không dùng file quan trọng để test xóa vĩnh viễn.
- Giá trị category/status đã lưu không được biến thành chuỗi hiển thị đã dịch.

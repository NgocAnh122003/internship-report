---
title: "Khởi tạo DynamoDB"
date: 2026-09-22
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
aliases:
  - /5-workshop/5.2-cognito-dynamodb/
---

# 5.2. Khởi tạo Cơ sở dữ liệu Amazon DynamoDB

Trong chương này, chúng ta sẽ tiến hành cấu hình cơ sở dữ liệu NoSQL hiệu năng cao trên **Amazon DynamoDB** để lưu trữ nội dung các ghi chú cho ứng dụng.

### Mục tiêu

- Tạo một bảng DynamoDB hoàn toàn phi máy chủ (Serverless).
- Thiết lập khóa chính (Partition Key) tối ưu hóa tốc độ truy vấn theo định danh duy nhất.
- Kiểm tra trạng thái hoạt động sẵn sàng của cơ sở dữ liệu để kết nối với AWS Lambda.

---

### Các bước thực hiện trên AWS Console

#### Bước 1: Truy cập giao diện DynamoDB

1. Tại ô tìm kiếm của [AWS Management Console](https://console.aws.amazon.com/), gõ **DynamoDB** và chọn dịch vụ **Amazon DynamoDB**.
2. Tại bảng điều khiển bên trái, chọn **Tables** và nhấn nút **Create table** ở góc trên bên phải.

#### Bước 2: Cấu hình thông số bảng dữ liệu

Nhập chính xác các thông số kỹ thuật cho bảng ghi chú như sau:

- **Table name (Tên bảng)**: `NotesTable`
- **Partition key (Khóa phân vùng)**: `id` và chọn kiểu dữ liệu là **String** (Chuỗi ký tự). Khóa này sẽ lưu mã định danh duy nhất (UUID) cho mỗi ghi chú được tạo ra.
- **Sort key (Khóa sắp xếp)**: Bỏ trống (Leave empty) vì chúng ta thiết kế theo mô hình bảng đơn giản.

> [!NOTE]
> **Giải thích kỹ thuật**: Mô hình NoSQL của DynamoDB giúp hệ thống không cần định nghĩa trước các cột dữ liệu phức tạp. Việc dùng `id` làm Partition Key đảm bảo tốc độ đọc/ghi luôn đạt mức mili-giây ngay cả khi số lượng bản ghi lên tới hàng triệu.

#### Bước 3: Thiết lập bảng và Hoàn tất

1. Tại mục **Table settings**, giữ nguyên tùy chọn mặc định **Default settings** (Sử dụng chế độ On-demand capacity mode để tự động điều chỉnh tài nguyên theo lưu lượng thực tế, tối ưu chi phí).
2. Cuộn xuống cuối trang và nhấn nút **Create table**.

#### Bước 4: Kiểm tra trạng thái bảng

1. Chờ khoảng 5 đến 10 giây để AWS khởi tạo tài nguyên.
2. Khi trạng thái (Status) của bảng chuyển từ _Creating_ sang **Active**, nghĩa là bảng `NotesTable` đã sẵn sàng hoạt động.

<p align="center">
  <img src="/images/5-Workshop/img_A/1.png" width="850" />
  <br>
  <i>Hình 5.2: Khởi tạo thành công bảng NotesTable trên DynamoDB với trạng thái Active</i>
</p>

---

### Bước tiếp theo

Cơ sở dữ liệu NoSQL đã được khởi tạo thành công. Bây giờ chúng ta sẽ chuyển sang **Mục 5.3** để xây dựng tầng xử lý logic Backend với **AWS Lambda** và cấu hình cổng giao tiếp **Amazon API Gateway**.

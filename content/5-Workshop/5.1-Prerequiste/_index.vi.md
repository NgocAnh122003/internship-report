---
title: "Chuẩn bị & Chọn Region"
date: 2026-09-22
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
aliases:
  - /5-workshop/5.1-prerequiste/
---

# 5.1. Chuẩn bị môi trường & Chọn Region

Trong phần này, chúng ta sẽ thiết lập các điều kiện cần thiết và cấu hình vùng hạ tầng (Region) trên AWS để bắt đầu xây dựng ứng dụng **Cloud Note App**.

### Yêu cầu tiên quyết (Prerequisites)

1. **Tài khoản AWS (AWS Account)**: Có quyền quản trị (Administrator Access) hoặc tài khoản IAM User được cấp quyền đầy đủ để thao tác với các dịch vụ: Amazon S3, Amazon API Gateway, AWS Lambda, Amazon DynamoDB và AWS IAM.
2. **Trình duyệt web hiện đại**: Google Chrome, Mozilla Firefox, Microsoft Edge hoặc Safari phiên bản mới nhất để truy cập AWS Management Console.
3. **Môi trường phát triển & Mã nguồn**:
   - Đã cài đặt trình soạn thảo mã nguồn (như Visual Studio Code).
   - Đã khởi tạo kho lưu trữ trên **GitHub** để quản lý mã nguồn frontend và thiết lập luồng tự động hóa CI/CD.
   - Công cụ kiểm thử API (tùy chọn): Postman hoặc cURL để kiểm tra nhanh điểm cuối API.

---

### Bước 1: Đăng nhập AWS Console và Chọn Region

1. Truy cập trang chủ quản trị [AWS Management Console](https://console.aws.amazon.com/) và tiến hành đăng nhập bằng tài khoản của bạn.
2. Tại thanh điều hướng ở góc trên bên phải màn hình, hãy nhấp vào menu chọn Region (Vùng địa lý).
3. Lựa chọn một Region tiêu chuẩn hỗ trợ đầy đủ các dịch vụ Serverless với độ trễ thấp, ví dụ:
   - **US East (N. Virginia) - us-east-1** (Khuyên dùng vì tính ổn định cao và cập nhật tính năng sớm nhất).
   - Hoặc **Asia Pacific (Singapore) - ap-southeast-1**.

> [!IMPORTANT]
> **Lưu ý quan trọng**: Bạn phải giữ nguyên một Region cố định trong suốt quá trình thực hiện workshop từ đầu đến cuối. Việc thay đổi Region giữa chừng sẽ làm mất liên kết giữa các tài nguyên như API Gateway, Lambda và DynamoDB.

<p align="center">
  <img src="/images/5-Workshop/img_A/0.png" width="300" />
  <br>
  <i>Hình: Giao diện chọn Region trên AWS Console</i>
</p>

### Bước tiếp theo

Sau khi đã thiết lập xong vùng làm việc, chúng ta sẽ chuyển sang **Mục 5.2** để tiến hành khởi tạo cơ sở dữ liệu NoSQL với **Amazon DynamoDB**.

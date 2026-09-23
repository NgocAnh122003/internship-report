---
title: "Dọn dẹp tài nguyên"
date: 2026-09-22
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
aliases:
  - /5-workshop/5.5-cleanup/
---

# 5.5. Dọn dẹp tài nguyên (Resource Clean-up)

Sau khi hoàn thành việc xây dựng, kiểm thử và vận hành hệ thống Serverless, để tuân thủ thực tiễn tốt nhất (Best Practices) về tối ưu hóa chi phí và quản lý vòng đời tài nguyên trên AWS, chúng ta thực hiện các bước dọn dẹp hệ thống.

### Các bước thực hiện dọn dẹp

#### 1. Xóa Amazon S3 Bucket

1. Truy cập dịch vụ **Amazon S3** trên AWS Console để kiểm tra danh sách bucket.
2. Chọn bucket `cloud-note-app-ngocanh-2026`. Do bucket đang chứa tệp tin tĩnh, hệ thống AWS sẽ hiển thị cảnh báo bảo vệ an toàn `This bucket is not empty` yêu cầu làm trống dữ liệu trước khi xóa.

<p align="center">
  <img src="/images/5-Workshop/img_A/40.png" width="850" />
  <br>
  <i>Hình 5.5a: Tổng quan danh sách Amazon S3 Buckets</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/41.png" width="850" />
  <br>
  <i>Hình 5.5b: Chọn S3 bucket cần dọn dẹp</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/42.png" width="850" />
  <br>
  <i>Hình 5.5c: Cảnh báo bảo vệ dữ liệu khi bucket chưa trống</i>
</p>

#### 2. Xóa AWS Lambda Function

1. Truy cập dịch vụ **AWS Lambda**, kiểm tra danh sách hàm xử lý.
2. Chọn hàm `NoteHandler` và thực hiện lệnh **Delete function**. Nhập từ khóa xác nhận `confirm` để hoàn tất yêu cầu xóa.

<p align="center">
  <img src="/images/5-Workshop/img_A/43.png" width="850" />
  <br>
  <i>Hình 5.5d: Danh sách AWS Lambda Functions</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/44.png" width="850" />
  <br>
  <i>Hình 5.5e: Giao diện xác nhận xóa hàm Lambda NoteHandler</i>
</p>

#### 3. Xóa Amazon API Gateway

1. Truy cập dịch vụ **Amazon API Gateway**, chọn HTTP API mang tên `NoteAPI`.
2. Mở tùy chọn xóa API và nhập từ khóa xác nhận `confirm` vào ô trống.

<p align="center">
  <img src="/images/5-Workshop/img_A/45.png" width="850" />
  <br>
  <i>Hình 5.5f: Giao diện xác nhận xóa HTTP API NoteAPI</i>
</p>

#### 4. Xóa Amazon DynamoDB Table

1. Truy cập dịch vụ **DynamoDB**, chọn mục **Tables**.
2. Chọn bảng `NotesTable`, nhấn **Delete** và nhập xác nhận `confirm` để xóa bảng cùng dữ liệu.

<p align="center">
  <img src="/images/5-Workshop/img_A/46.png" width="850" />
  <br>
  <i>Hình 5.5g: Giao diện xác nhận xóa bảng DynamoDB NotesTable</i>
</p>

#### 5. Xóa IAM User phục vụ CI/CD

1. Truy cập dịch vụ **IAM**, chọn mục **Users** để quản lý các tài khoản định danh.
2. Chọn user `github-actions-bot`, nhấn **Delete user** và nhập xác nhận `confirm` để thu hồi quyền truy cập GitHub Actions.

<p align="center">
  <img src="/images/5-Workshop/img_A/48.png" width="850" />
  <br>
  <i>Hình 5.5h: Danh sách IAM Users sau khi kiểm tra quản trị</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/47.png" width="850" />
  <br>
  <i>Hình 5.5i: Giao diện xác nhận xóa IAM User github-actions-bot</i>
</p>

---

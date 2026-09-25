---
title: "S3 Hosting & GitOps CI/CD"
date: 2026-09-22
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
aliases:
  - /5-workshop/5.4-s3-hosting-gitops/
---

# 5.4. Triển khai Giao diện lên Amazon S3 và Tự động hóa với GitOps

Trong chương này, chúng ta sẽ biến một kho lưu trữ **Amazon S3** thành một máy chủ web tĩnh (Static Website Hosting) để phục vụ giao diện Frontend. Đồng thời, cấu hình quy trình **GitOps CI/CD** qua GitHub Actions để mỗi khi có thay đổi mã nguồn, giao diện sẽ tự động cập nhật lên S3.

### Mục tiêu

- Cấu hình Amazon S3 Bucket với chính sách bảo mật (Bucket Policy) cho phép truy cập công khai.
- Triển khai mã nguồn HTML/JS của ứng dụng.
- Khởi tạo GitHub Repository và thiết lập luồng CI/CD tự động bằng GitHub Actions.

---

### Các bước thực hiện chi tiết

#### Bước 1: Khởi tạo Amazon S3 Bucket

1. Truy cập dịch vụ **Amazon S3** trên AWS Console và nhấn **Create bucket**.
2. Thiết lập thông số cơ bản:
   - **Bucket name**: `cloud-note-app-ngocanh-2026` (Tên bucket phải là duy nhất trên toàn cầu).
   - **AWS Region**: Chọn `us-east-1` (N. Virginia).
3. Tại phần **Block Public Access settings for this bucket**, bỏ tích chọn _Block all public access_ để cho phép người dùng bên ngoài Internet có thể truy cập trang web. Xác nhận rủi ro khi được yêu cầu.
4. Nhấn **Create bucket**.

<p align="center">
  <img src="/images/5-Workshop/img_A/21.png" width="850" />
  <br>
  <i>Hình 5.4a: Khởi tạo thành công S3 Bucket mang tên cloud-note-app-ngocanh-2026</i>
</p>

#### Bước 2: Cấu hình Static Website Hosting và Bucket Policy

1. Truy cập vào bucket vừa tạo, chọn tab **Properties**. Cuộn xuống dưới cùng tới mục **Static website hosting**, nhấn **Edit** và chọn **Enable**. Đặt _Index document_ là `index.html`.
2. Chuyển sang tab **Permissions**, cuộn xuống mục **Bucket policy** và nhấn **Edit**. Dán đoạn mã JSON sau để cấp quyền đọc công khai (PublicReadGetObject) cho mọi tệp tin trong bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::cloud-note-app-ngocanh-2026/*"
    }
  ]
}
```

#### Bước 3: Đưa tệp tin HTML lên S3 và Kiểm tra Hosting

1. Chọn tab **Objects**, nhấn **Upload** và tải tệp tin `index.html` (đã được cấu hình Invoke URL của API Gateway ở bước trước) lên bucket.

<p align="center">
  <img src="/images/5-Workshop/img_A/24.png" width="850" />
  <br>
  <i>Hình 5.4c: Tải tệp tin giao diện (index.html) thành công lên S3 Bucket</i>
</p>

2. Quay lại tab **Properties**, cuộn xuống mục **Static website hosting** và nhấp vào đường dẫn **Bucket website endpoint**.
3. Trình duyệt sẽ mở ra giao diện ứng dụng quản lý ghi chú. Lúc này, trang web đã chính thức được public trên Internet!

<p align="center">
  <img src="/images/5-Workshop/img_A/58.png" width="850" />
  <br>
  <i>Hình 5.4d: Website Cloud Note App phong cách Modern Minimalist hoạt động thành công trên Amazon S3</i>
</p>

{{% notice info %}}
**Tối ưu hóa Trải nghiệm:**
Giao diện ứng dụng được thiết kế theo phong cách hiện đại, tối giản. Các thao tác Thêm/Xóa đều gọi trực tiếp qua API Gateway đến Serverless Backend, mang lại tốc độ phản hồi gần như tức thời.
{{% /notice %}}

#### Bước 4: Thiết lập GitHub Repository cho quy trình GitOps

Để tự động hóa việc đưa code lên S3 mỗi lần có cập nhật, ta sử dụng GitHub.

1. Truy cập trang cá nhân GitHub (tài khoản `NgocAnh122003`) và nhấn **New** để tạo Repository mới.
2. Đặt **Repository name** là `cloud-note-app`, chọn chế độ **Public** và nhấn **Create repository**.

<p align="center">
  <img src="/images/5-Workshop/img_A/29.png" width="850" />
  <br>
  <i>Hình 5.4e: Khởi tạo Repository cloud-note-app trên nền tảng GitHub</i>
</p>

3. Tải tệp tin `index.html` của bạn lên repository này (Commit changes).

<p align="center">
  <img src="/images/5-Workshop/img_A/28.png" width="850" />
  <br>
  <i>Hình 5.4f: Đẩy mã nguồn giao diện lên GitHub Repository</i>
</p>

---

#### Bước 5: Cấu hình IAM User và Lấy Access Key

Để GitHub Actions có quyền đẩy code lên S3, ta cần tạo một tài khoản IAM chuyên dụng.

1. Truy cập dịch vụ **IAM** trên AWS Console => chọn **Users** => nhấn **Create user**.
2. Đặt tên user là `github-actions-bot`.

<p align="center">
  <img src="/images/5-Workshop/img_A/31.png" width="850" />
  <br>
  <i>Hình 5.4g: Khởi tạo IAM user mang tên github-actions-bot</i>
</p>

3. Tại bước _Set permissions_, chọn **Attach policies directly** và tìm gán chính sách `AmazonS3FullAccess`.

<p align="center">
  <img src="/images/5-Workshop/img_A/32.png" width="850" />
  <br>
  <i>Hình 5.4h: Cấp quyền AmazonS3FullAccess cho IAM user</i>
</p>

4. Hoàn tất tạo user, sau đó chọn user vừa tạo => tab **Security credentials** => nhấn **Create access key**.
5. Chọn mục **Third-party service**, xác nhận điều khoản và lấy cặp **Access key** cùng **Secret access key**.

<p align="center">
  <img src="/images/5-Workshop/img_A/34.png" width="850" />
  <br>
  <i>Hình 5.4i: Lựa chọn use case để tạo Access Key cho dịch vụ bên thứ ba</i>
</p>

#### Bước 6: Cấu hình GitHub Secrets bảo mật

1. Quay lại repository `cloud-note-app` trên GitHub (`NgocAnh122003`) -> chọn tab **Settings** -> **Secrets and variables** -> **Actions**.

2. Nhấn **New repository secret** lần lượt thêm 2 biến bảo mật:
   - Name: `AWS_ACCESS_KEY_ID` | Value: _(Dán mã Access Key của bạn)_
   - Name: `AWS_SECRET_ACCESS_KEY` | Value: _(Dán mã Secret Key của bạn)_

<p align="center">
  <img src="/images/5-Workshop/img_A/36.png" width="850" />
  <br>
  <i>Hình 5.4j: Thiết lập thành công các biến AWS Secrets trên kho lưu trữ GitHub</i>
</p>

#### Bước 7: Cấu hình quy trình tự động hóa GitHub Actions (CI/CD)

1. Tại kho lưu trữ GitHub, tạo thư mục `.github/workflows/` và tạo tệp tin `main.yml`.
2. Dán cấu hình luồng tự động hóa để đồng bộ code lên S3 mỗi khi có lệnh `push` vào nhánh `main`:

```yaml
name: Deploy Frontend to Amazon S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 1. Checkout Code
        uses: actions/checkout@v3

      - name: 2. Cấu hình xác thực AWS
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: 3. Tự động triển khai lên S3 Bucket
        run: |
          aws s3 cp index.html s3://cloud-note-app-ngocanh-2026/index.html
```

<p align="center">
  <img src="/images/5-Workshop/img_A/37.png" width="850" />
  <br>
  <i>Hình 5.4k: Nội dung tệp cấu hình main.yml của GitHub Actions</i>
</p>

3. Nhấn **Commit changes** để lưu lại. Hệ thống GitHub Actions sẽ tự động kích hoạt tiến trình chạy.
4. Chuyển sang tab **Actions** trên GitHub để theo dõi trạng thái triển khai. Khi xuất hiện dấu tích màu xanh (`Success`), quá trình tự động hóa GitOps đã hoàn tất thành công!

<p align="center">
  <img src="/images/5-Workshop/img_A/38.png" width="850" />
  <br>
  <i>Hình 5.4l: Tiến trình GitHub Actions chạy thành công (Success)</i>
</p>

{{% notice success %}}
**Sức mạnh của CI/CD:**
Thay vì triển khai thủ công qua giao diện AWS Console (ClickOps) nhiều rủi ro, hệ thống giờ đây đã hoàn toàn tự động hóa. Bất kỳ kỹ sư nào đẩy mã nguồn mới lên nhánh `main`, giao diện ứng dụng sẽ được cập nhật lên S3 trong chưa đầy 1 phút.
{{% /notice %}}

---

### Bước tiếp theo

Việc triển khai ứng dụng kiến trúc Serverless 3 tầng (3-Tier) và luồng CI/CD tự động hóa đã hoàn thiện. Ở phần cuối cùng (**Mục 5.5**), chúng ta sẽ tiến hành dọn dẹp tài nguyên để tối ưu chi phí hạ tầng Cloud.

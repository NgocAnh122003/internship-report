---
title: "Xây dựng Lambda & API Gateway"
date: 2026-09-22
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
aliases:
  - /5-workshop/5.3-matchmaker-api/
---

# 5.3. Xây dựng AWS Lambda Backend & Amazon API Gateway

Trong chương này, chúng ta sẽ xây dựng tầng xử lý logic nghiệp vụ bằng **AWS Lambda** và mở điểm cuối (Endpoint) giao tiếp thông qua **Amazon API Gateway** để kết nối Frontend với Database.

### Mục tiêu

- Khởi tạo hàm AWS Lambda xử lý các tác vụ CRUD cho ghi chú.
- Triển khai mã nguồn Python và cấp quyền IAM Policy an toàn để thao tác trực tiếp với bảng `NotesTable`.
- Triển khai Amazon API Gateway (HTTP API) và định tuyến kết nối tự động đến hàm Lambda.
- Tích hợp hệ thống giám sát CloudWatch (Dashboard & Alarms) để theo dõi sức khỏe và cảnh báo lỗi tự động.

---

### Các bước thực hiện chi tiết

#### Bước 1: Khởi tạo hàm AWS Lambda Backend

1. Truy cập dịch vụ **AWS Lambda** trên AWS Console và chọn **Create a function**.
2. Chọn **Author from scratch** và cấu hình các thông số:
   - **Function name**: `NoteHandler`
   - **Runtime**: Chọn `Python 3.12` (hoặc phiên bản tương đương).
3. Nhấn **Create function**.

<p align="center">
  <img src="/images/5-Workshop/img_A/2.png" width="850" />
  <br>
  <i>Hình 5.3a: Khởi tạo thành công hàm AWS Lambda với tên NoteHandler</i>
</p>

#### Bước 2: Phát triển mã nguồn xử lý logic (Lambda Code)

1. Tại giao diện hàm vừa tạo, cuộn xuống phần **Code source**.
2. Mở file `lambda_function.py`, dán đoạn mã nguồn Python sau (lưu ý tên bảng là `NotesTable` đã được cấu hình bên trong mã):

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('NotesTable')

def lambda_handler(event, context):
    try:
        # Lấy toàn bộ danh sách ghi chú từ DynamoDB
        response = table.scan()
        items = response.get('Items', [])

        return {
            'statusCode': 200,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': 'Content-Type',
                'Access-Control-Allow-Methods': 'OPTIONS,POST,GET,DELETE'
            },
            'body': json.dumps(items)
        }
    except Exception as e:
        return {
            'statusCode': 500,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': 'Content-Type'
            },
            'body': json.dumps({'error': str(e)})
        }
```

#### Bước 3: Cấp quyền IAM cho Lambda (Least Privilege)

Để Lambda có quyền đọc/ghi vào DynamoDB, ta cần cấp quyền cho Execution Role của nó.

1. Chuyển sang tab **Configuration** => chọn **Permissions**.
2. Nhấp vào đường dẫn tên của Role (ví dụ: `NoteHandler-role-xxxx`) để mở giao diện quản lý IAM.
3. Trong giao diện IAM Role, nhấn **Add permissions** => chọn **Attach policies**.
4. Tìm kiếm và chọn chính sách `AmazonDynamoDBFullAccess`, sau đó nhấn **Add permissions**.

<p align="center">
  <img src="/images/5-Workshop/img_A/7.png" width="850" />
  <br>
  <i>Hình 5.3c: Cấp quyền truy cập DynamoDB thành công cho Role của Lambda</i>
</p>

#### Bước 4: Tạo và cấu hình Amazon API Gateway (HTTP API)

1. Truy cập dịch vụ **Amazon API Gateway** và nhấn **Create API**.
2. Tại thẻ **HTTP API**, nhấn **Build**.
3. Tại bước _Configure API_, thiết lập các thông số sau:
   - **API name**: `NoteAPI`
   - **Integrations**: Chọn _Lambda_, trỏ tới vùng Region hiện tại và chọn hàm `NoteHandler`.
4. Nhấn **Next** để chuyển sang bước _Configure routes_. Tại đây, giữ nguyên hoặc cấu hình phương thức `ANY` cho resource path `/{proxy+}`.
5. Tiếp tục nhấn **Next** qua các bước cấu hình Stage (để mặc định `$default`) và nhấn **Review and create** => **Create**.

<p align="center">
  <img src="/images/5-Workshop/img_A/13.png" width="850" />
  <br>
  <i>Hình 5.3d: Cấu hình Integration định tuyến mọi request tới hàm Lambda</i>
</p>

6. Sau khi API Gateway thông báo tạo thành công, điều hướng đến mục **Stages** ở menu bên trái. Sao chép đường dẫn **Invoke URL** được cung cấp. Bạn sẽ dùng URL này cấu hình vào biến môi trường của Frontend ở bước sau.

<p align="center">
  <img src="/images/5-Workshop/img_A/15.png" width="850" />
  <br>
  <i>Hình 5.3e: Triển khai API Gateway thành công và lấy đường dẫn Invoke URL</i>
</p>

#### Bước 5: Kiểm thử luồng dữ liệu (End-to-End Test)

Trước khi triển khai Frontend, ta cần đảm bảo Backend đã hoạt động.

1. Sử dụng ứng dụng web Frontend (chạy trên local hoặc S3), nhập URL của API Gateway vào cấu hình.
2. Thử nhập một đoạn ghi chú và nhấn **Lưu Ghi Chú**.
3. Nếu ứng dụng hiển thị danh sách ghi chú thành công, chứng tỏ luồng `Frontend -> API Gateway -> Lambda -> DynamoDB` đã hoàn toàn thông suốt.

<p align="center">
  <img src="/images/5-Workshop/img_A/16.png" width="850" />
  <br>
  <i>Hình 5.3f: Giao diện Frontend hiển thị ghi chú được tải thành công từ Backend</i>
</p>

---

#### Bước 6: Thiết lập Giám sát & Cảnh báo (Monitoring & Operations)

Thay vì cấu hình xong rồi để đó, một hệ thống Operations chủ động đã được tích hợp để theo dõi sức khỏe API.

**1. Giám sát Hiệu suất (CloudWatch Dashboard)**

Một bảng điều khiển tập trung được thiết kế để theo dõi các chỉ số sinh tử theo thời gian thực:

- **Lưu lượng & Độ trễ:** Số lần gọi hàm (`Invocations`) và Thời gian thực thi (`Duration`) của Lambda.
- **Công suất CSDL:** Lượng đơn vị đọc/ghi (`ConsumedReadCapacityUnits`, `ConsumedWriteCapacityUnits`) của DynamoDB.

<p align="center">
  <img src="/images/5-Workshop/img_A/53.png" width="850" />
  <br>
  <i>Hình 5.3g: Bảng điều khiển CloudWatch giám sát Backend</i>
</p>

**2. Hệ thống Cảnh báo (CloudWatch Alarms)**

Để ngăn chặn tình trạng lỗi hệ thống không được phát hiện, một cơ chế cảnh báo tự động đã được kích hoạt.

<p align="center">
  <img src="/images/5-Workshop/img_A/56.png" width="850" />
  <br>
  <i>Hình 5.3h: Cảnh báo tự động khi hàm Lambda phát sinh lỗi</i>
</p>

{{% notice info %}}
**DevOps Best Practice:**
Cảnh báo `NoteHandler-Error-Alarm` được cấu hình để theo dõi chỉ số `Errors`. Nếu có bất kỳ lỗi nào phát sinh từ Backend (ví dụ do code lỗi hoặc DynamoDB quá tải) liên tục trong 5 phút, hệ thống sẽ lập tức bắn email thông báo qua Amazon SNS cho đội ngũ vận hành.
{{% /notice %}}

---

### Bước tiếp theo

Backend xử lý và cổng API đã hoàn tất an toàn cùng với hệ thống giám sát. Bây giờ chúng ta chuyển sang **Mục 5.4** để triển khai giao diện lên **Amazon S3** và thiết lập luồng tự động hóa GitOps CI/CD với GitHub Actions.

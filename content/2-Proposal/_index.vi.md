---
title: "Bản đề xuất"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Ứng dụng Quản lý Ghi chú Đám mây (Cloud Note App) trên AWS

## Kiến trúc Web Serverless tối ưu chi phí và vận hành tự động (Zero-Ops)

### 1. Tóm tắt điều hành

Đề xuất này trình bày giải pháp kiến trúc **Ứng dụng Web Single-Page Application (SPA)** chạy hoàn toàn trên hạ tầng đám mây AWS. Thay vì triển khai theo mô hình máy chủ ảo (EC2) truyền thống đòi hỏi chi phí duy trì 24/7 và công sức quản trị hệ điều hành, hệ thống áp dụng triệt để nguyên tắc **Serverless (Không máy chủ)**.

Toàn bộ giao diện người dùng (Frontend) được phân phối với tốc độ cao thông qua **Amazon S3**. Luồng xử lý nghiệp vụ (Backend) và thao tác cơ sở dữ liệu được kích hoạt hoàn toàn tự động thông qua **Amazon API Gateway** và **AWS Lambda**. Kiến trúc này đảm bảo hệ thống có khả năng tự động mở rộng (Auto-scaling) ngay lập tức khi lượng người dùng tăng đột biến, đồng thời tối ưu hóa ngân sách nhờ mô hình chỉ trả tiền trên mỗi mili-giây xử lý mã lệnh thực tế. Đặc biệt, quy trình phát hành mã nguồn được tự động hóa hoàn toàn (CI/CD) thông qua **GitHub Actions**, mang lại trải nghiệm vận hành Zero-Ops đúng nghĩa cho nhà phát triển.

---

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại

- **Chi phí lãng phí lớn (Idle Cost)**: Các ứng dụng web truyền thống thường phải duy trì máy chủ ảo (EC2/Virtual Machine) chạy liên tục 24/7 để sẵn sàng nhận request, gây lãng phí tài nguyên lớn vào các khung giờ thấp điểm.
- **Điểm nghẽn cổ chai (Single Point of Failure)**: Nếu chỉ triển khai trên một máy chủ duy nhất, hệ thống dễ bị quá tải hoặc sập hoàn toàn khi lưu lượng truy cập tăng vọt, đòi hỏi phải thiết lập Auto Scaling và Load Balancer phức tạp.
- **Gánh nặng quản trị (Operational Overhead)**: Phải liên tục cập nhật hệ điều hành, vá lỗi bảo mật (patching) và quản lý cơ sở dữ liệu vật lý. Tốn thời gian triển khai (deploy) mã nguồn thủ công mỗi khi có bản cập nhật mới.

#### Giải pháp đề xuất

Hệ thống được thiết kế theo nguyên tắc cốt lõi: **Serverless & GitOps toàn diện**. Tách biệt hoàn toàn tầng Giao diện (Frontend) và tầng Xử lý logic (Backend) thành các dịch vụ độc lập, được quản lý hoàn toàn bởi AWS (Fully Managed).

Kiến trúc bao gồm 4 luồng xử lý độc lập:

1.  **Flow F (Frontend Delivery)**: Mã nguồn giao diện tĩnh (HTML/CSS/JS) được lưu trữ và phân phối trực tiếp từ Amazon S3, loại bỏ hoàn toàn nhu cầu sử dụng Web Server (như Apache/Nginx).
2.  **Flow A (API & Compute)**: Trình duyệt của người dùng gọi trực tiếp tới Amazon API Gateway. Gateway đóng vai trò làm cửa ngõ định tuyến, kích hoạt các hàm AWS Lambda (viết bằng Python/Node.js) để xử lý logic như tạo mới, đọc, hoặc xóa ghi chú.
3.  **Flow D (Data Storage)**: AWS Lambda giao tiếp với Amazon DynamoDB (Cơ sở dữ liệu NoSQL) để lưu trữ nội dung ghi chú. DynamoDB tự động phân tán dữ liệu trên nhiều Availability Zones (AZs) để đảm bảo tính sẵn sàng cao.
4.  **Flow C (CI/CD Pipeline)**: Quá trình triển khai giao diện được tự động hóa. Khi có thay đổi mã nguồn trên nhánh `main` của GitHub, hệ thống GitHub Actions sẽ tự động xác thực và đồng bộ file mới lên S3 Bucket, loại bỏ hoàn toàn thao tác upload thủ công.

#### Lợi ích và Hoàn vốn đầu tư (ROI)

- **Tối ưu chi phí tối đa (lên đến 95%)**: Không có máy chủ nhàn rỗi. Tận dụng triệt để gói AWS Free Tier cho S3, Lambda (1 triệu request/tháng) và DynamoDB (25GB lưu trữ) trong giai đoạn phát triển.
- **Vận hành không chạm (Zero-Ops)**: AWS chịu trách nhiệm hoàn toàn về bảo trì hạ tầng, vá lỗi hệ điều hành và cập nhật phần mềm, giúp tập trung 100% vào việc phát triển tính năng ứng dụng.
- **Mở rộng quy mô tức thì**: Khả năng phục vụ từ 1 người dùng đến hàng chục nghìn người dùng cùng lúc mà không cần can thiệp cấu hình thủ công.

---

### 3. Kiến trúc giải pháp

#### Sơ đồ kiến trúc tổng thể

![Serverless Note App Architecture](/images/2-Proposal/a.png)

#### Chi tiết các luồng xử lý chính trong kiến trúc:

##### 1. Tầng Giao diện & Lưu trữ tĩnh (Static Web Hosting)

- Mã nguồn ứng dụng (gồm file `index.html`, style CSS và logic JavaScript) được lưu trữ trên **Amazon S3 Bucket**.
- Bucket được cấu hình tính năng Static Website Hosting và thiết lập Bucket Policy cho phép Public Read để người dùng có thể truy cập qua URL của S3.

##### 2. Tầng Điều phối & Xử lý (API & Compute Layer)

- **Amazon API Gateway** cung cấp một điểm cuối (REST API Endpoint) an toàn. API Gateway được cấu hình CORS (Cross-Origin Resource Sharing) để cho phép các domain bên ngoài (S3) được phép gọi HTTP POST/GET.
- Mỗi khi có request gửi ghi chú mới, API Gateway sẽ kích hoạt hàm **AWS Lambda**. Hàm Lambda đảm nhiệm việc parse dữ liệu JSON từ Frontend, tạo ID định danh duy nhất (UUID) cho ghi chú và chuẩn bị payload dữ liệu.

##### 3. Tầng Cơ sở dữ liệu (Database Layer)

- Hàm Lambda sử dụng AWS SDK (Boto3 đối với Python) để gọi phương thức `put_item` và ghi dữ liệu trực tiếp vào bảng **Amazon DynamoDB**.
- Bảng DynamoDB sử dụng thiết kế Single-Table với Partition Key là `id` (String), đảm bảo tốc độ truy xuất dữ liệu luôn đạt mức mili-giây ở bất kỳ quy mô nào.

#### Dịch vụ & Công cụ sử dụng

- **Amazon S3**: Lưu trữ và phân phối giao diện web tĩnh.
- **Amazon API Gateway**: Cổng giao tiếp REST API, quản lý CORS và định tuyến luồng truy cập.
- **AWS Lambda**: Thực thi mã nguồn xử lý logic Backend phi máy chủ.
- **Amazon DynamoDB**: Cơ sở dữ liệu NoSQL lưu trữ văn bản ghi chú nhanh và linh hoạt.
- **AWS IAM**: Quản lý phân quyền chặt chẽ, cấp quyền (Role) cho phép Lambda truy cập DynamoDB và tạo Access Keys cho phép GitHub Actions tương tác với S3.
- **Amazon CloudWatch**: Ghi nhận log thực thi của Lambda để phục vụ việc giám sát và gỡ lỗi (debugging).
- **GitHub Actions**: Nền tảng tự động hóa CI/CD, thực thi kịch bản (workflow) để tự động đẩy mã nguồn từ kho lưu trữ lên môi trường Cloud.

---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai dự án (Thực hiện trong Tuần 8)

1.  **Giai đoạn 1: Thiết kế Cơ sở dữ liệu & Phân quyền**
    - Tạo bảng DynamoDB `NotesTable` với khóa chính `id`.
    - Tạo IAM Execution Role cấp quyền `AmazonDynamoDBFullAccess` và `AWSLambdaBasicExecutionRole` cho hàm xử lý.
2.  **Giai đoạn 2: Xây dựng Backend Logic**
    - Khởi tạo hàm AWS Lambda (`SaveNoteFunction`).
    - Phát triển mã nguồn Python/Boto3 để xử lý luồng nhận dữ liệu từ sự kiện (event) và ghi vào cơ sở dữ liệu.
3.  **Giai đoạn 3: Tích hợp API Gateway**
    - Tạo REST API công khai, liên kết phương thức POST với hàm Lambda vừa tạo.
    - Kích hoạt và cấu hình CORS để tránh lỗi bảo mật từ trình duyệt, sau đó triển khai API ra môi trường thực tế (Deploy Stage).
4.  **Giai đoạn 4: Lưu trữ S3 & Tự động hóa CI/CD (GitOps)**
    - Tạo Amazon S3 Bucket, kích hoạt tính năng Public Static Website Hosting và thiết lập Bucket Policy.
    - Xây dựng giao diện HTML/JS, gắn URL của API Gateway vào mã nguồn JavaScript (Fetch API).
    - Khởi tạo kịch bản **GitHub Actions** (`main.yml`). Thiết lập các biến môi trường an toàn (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`) trong GitHub Secrets để hệ thống tự động triển khai (Auto-Deploy) mã nguồn Frontend lên S3 mỗi khi có mã mới được đẩy (Commit/Push).
5.  **Giai đoạn 5: Kiểm thử & Đánh giá**
    - Kiểm thử luồng CI/CD Pipeline.
    - Kiểm thử luồng dữ liệu End-to-End (Từ giao diện web -> API -> Lambda -> Database).
    - Theo dõi và phân tích log thông qua CloudWatch.

#### Yêu cầu kỹ thuật & Bảo mật

- **Quyền hạn tối thiểu (Least Privilege)**: Tách bạch quyền hạn thông qua AWS IAM. Hàm Lambda chỉ có quyền ghi vào duy nhất bảng DynamoDB của dự án. Khóa IAM cấp cho GitHub Actions cũng chỉ được giới hạn quyền thao tác trên đúng S3 Bucket được chỉ định.
- **Quản trị tài nguyên mạng**: Bảo mật điểm cuối API Gateway bằng cách kiểm soát chặt chẽ Access-Control-Allow-Origin (CORS).
- **Bảo vệ dữ liệu**: Dữ liệu truyền tải giữa Frontend và API Gateway được tự động mã hóa qua giao thức HTTPS được AWS cung cấp sẵn.

---

### 5. Lộ trình & Mốc triển khai

```text
+-----------------------------------------------------------------------------------+
| Giai đoạn 1: Khởi tạo Database & Viết logic Backend                               |
|   - Tạo bảng Amazon DynamoDB.                                                     |
|   - Viết mã nguồn AWS Lambda xử lý dữ liệu và cấp quyền IAM.                      |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Giai đoạn 2: Định tuyến API & Tích hợp luồng dữ liệu                              |
|   - Thiết lập Amazon API Gateway & cấu hình Integration Request với Lambda.       |
|   - Kích hoạt CORS và Deploy API để lấy End-point URL.                            |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Giai đoạn 3: Triển khai Frontend, Lưu trữ S3 & Tự động hóa CI/CD                  |
|   - Cập nhật API URL vào mã nguồn JavaScript của Frontend.                        |
|   - Tạo Amazon S3 Bucket, cấp quyền Public Read và host Static Website.           |
|   - Thiết lập GitHub Actions Pipeline để tự động hóa Deploy mã nguồn lên S3.      |
+-----------------------------------------------------------------------------------+
```

### 6. Ước tính ngân sách

Dù sở hữu khả năng mở rộng mạnh mẽ, kiến trúc Serverless vẫn mang lại hiệu quả chi phí vượt trội nhờ mô hình Pay-as-you-go (Chỉ trả tiền cho tài nguyên thực sự sử dụng). Dưới đây là bảng dự toán chi phí hàng tháng giả định hệ thống hoạt động ở môi trường Sản xuất (Production) với lưu lượng khoảng **5 triệu lượt tương tác/tháng**:

| Dịch vụ AWS               | Quy mô giả định (Production)                     | Chi phí ước tính / Tháng (USD) |
| :------------------------ | :----------------------------------------------- | :----------------------------- |
| **Amazon API Gateway**    | 5,000,000 REST API requests                      | ~$5.00                         |
| **AWS Lambda**            | 5,000,000 requests, 128MB RAM, 200ms/req         | ~$1.20                         |
| **Amazon DynamoDB**       | On-Demand Mode, 10GB dữ liệu, 5 triệu Write/Read | ~$3.50                         |
| **Amazon S3**             | 20GB lưu trữ tài nguyên tĩnh, 50GB Bandwidth     | ~$4.50                         |
| **Amazon Route 53**       | Quản lý DNS Host Zone (1 Domain)                 | ~$0.50                         |
| **Amazon CloudWatch**     | Lưu trữ 5GB Log dữ liệu thực thi                 | ~$2.50                         |
| **Tổng chi phí ước tính** | **Hệ thống Serverless phục vụ 5 triệu request**  | **~$17.20 USD / Tháng**        |

> [!TIP]
> **Điểm tối ưu chi phí vượt trội**:
> Nếu triển khai theo mô hình cũ (cần ít nhất 2 máy chủ ảo EC2 và 1 RDS Database chạy liên tục 24/7 để gánh được mức tải 5 triệu request), chi phí cố định sẽ rơi vào khoảng **$45 - $60/tháng**. Mô hình Serverless giúp dự án tiết kiệm tới **70% ngân sách** trong khi vẫn loại bỏ hoàn toàn gánh nặng quản trị hệ điều hành và tự động chống sập web khi quá tải.

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro & Chiến lược giảm thiểu

| Rủi ro tiềm ẩn                              | Mức độ ảnh hưởng |  Xác suất  | Chiến lược giảm thiểu                                                                                                                                                       |
| :------------------------------------------ | :--------------: | :--------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Lỗi Cross-Origin (CORS) từ trình duyệt**  |       Cao        |    Cao     | Kiểm tra cấu hình Enable CORS trên API Gateway. Đảm bảo hàm Lambda trả về đúng cấu trúc header `Access-Control-Allow-Origin` trong mã JSON phản hồi.                        |
| **Lỗi Lambda không thể ghi dữ liệu vào DB** |       Cao        | Trung bình | Rà soát IAM Role của Lambda, đảm bảo policy `AmazonDynamoDBFullAccess` đã được đính kèm đúng cách.                                                                          |
| **Lộ lọt thông tin Endpoint API**           |    Trung bình    |    Thấp    | API Gateway mặc định có cơ chế chống dội bom request (throttling). Có thể thiết lập thêm Usage Plan và API Key nếu muốn giới hạn truy cập gắt gao hơn ở môi trường thực tế. |

---

### 8. Kết quả kỳ vọng

- **Làm chủ công nghệ Đám mây hiện đại**: Xây dựng thành công một sản phẩm web hoàn chỉnh từ con số 0, ứng dụng thuần thục 4 dịch vụ cốt lõi của AWS (S3, API Gateway, Lambda, DynamoDB).
- **Khẳng định tính ưu việt của Serverless**: Chứng minh thực tế mô hình Pay-as-you-go, loại bỏ hoàn toàn gánh nặng quản lý hạ tầng vật lý, giúp ứng dụng tự động co giãn theo nhu cầu thực tế.
- **Tư duy vận hành DevOps/GitOps**: Tích hợp thành công luồng CI/CD, biến một dự án học thuật thành một hệ thống có quy trình phát hành chuyên nghiệp như môi trường doanh nghiệp.
- **Nền tảng vững chắc cho tương lai**: Dự án demo này là một bản mẫu kiến trúc (Architectural Blueprint) chuẩn xác, có thể dễ dàng mở rộng thêm các tính năng phức tạp (như đăng nhập bằng Amazon Cognito, gửi email bằng Amazon SES) cho các hệ thống phần mềm thương mại sau này.

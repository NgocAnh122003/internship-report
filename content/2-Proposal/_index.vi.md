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

Toàn bộ giao diện người dùng (Frontend) được phân phối với tốc độ cao thông qua **Amazon S3**. Luồng xử lý nghiệp vụ (Backend) và thao tác cơ sở dữ liệu được kích hoạt hoàn toàn tự động thông qua **Amazon API Gateway** và **AWS Lambda**. Kiến trúc này đảm bảo hệ thống có khả năng tự động mở rộng (Auto-scaling) ngay lập tức khi lượng người dùng tăng đột biến, đồng thời tối ưu hóa ngân sách nhờ mô hình chỉ trả tiền trên mỗi mili-giây xử lý mã lệnh thực tế. Đặc biệt, hệ thống tích hợp chuẩn mực **FinOps** (AWS Budgets) và **GitOps** (GitHub Actions), mang lại trải nghiệm vận hành an toàn và Zero-Ops đúng nghĩa.

---

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại

- **Chi phí lãng phí lớn (Idle Cost)**: Các ứng dụng web truyền thống thường phải duy trì máy chủ ảo (EC2/Virtual Machine) chạy liên tục 24/7 để sẵn sàng nhận request, gây lãng phí tài nguyên lớn vào các khung giờ thấp điểm.
- **Điểm nghẽn cổ chai (Single Point of Failure)**: Nếu chỉ triển khai trên một máy chủ duy nhất, hệ thống dễ bị quá tải hoặc sập hoàn toàn khi lưu lượng truy cập tăng vọt, đòi hỏi phải thiết lập Auto Scaling và Load Balancer phức tạp.
- **Gánh nặng quản trị (Operational Overhead)**: Phải liên tục cập nhật hệ điều hành, vá lỗi bảo mật (patching), theo dõi hiệu suất thủ công và tốn thời gian triển khai (deploy) mã nguồn mỗi khi có bản cập nhật mới.

#### Giải pháp đề xuất

Hệ thống được thiết kế theo nguyên tắc cốt lõi: **Serverless & GitOps toàn diện**. Tách biệt hoàn toàn tầng Giao diện (Frontend) và tầng Xử lý logic (Backend) thành các dịch vụ độc lập, được quản lý hoàn toàn bởi AWS (Fully Managed).

Kiến trúc bao gồm 4 luồng xử lý độc lập:

1.  **Flow F (Frontend Delivery)**: Mã nguồn giao diện tĩnh (HTML/CSS/JS) được lưu trữ và phân phối trực tiếp từ Amazon S3, loại bỏ hoàn toàn nhu cầu sử dụng Web Server (như Apache/Nginx).
2.  **Flow A (API & Compute)**: Trình duyệt của người dùng gọi trực tiếp tới Amazon API Gateway. Gateway đóng vai trò làm cửa ngõ định tuyến, kích hoạt các hàm AWS Lambda để xử lý logic như tạo mới, đọc, hoặc xóa ghi chú.
3.  **Flow D (Data Storage)**: AWS Lambda giao tiếp với Amazon DynamoDB (Cơ sở dữ liệu NoSQL) để lưu trữ nội dung ghi chú. DynamoDB tự động phân tán dữ liệu trên nhiều Availability Zones (AZs) để đảm bảo tính sẵn sàng cao.
4.  **Flow C (CI/CD Pipeline)**: Quá trình triển khai giao diện được tự động hóa bằng GitHub Actions, tự động đồng bộ file mới lên S3 Bucket mỗi khi có thay đổi mã nguồn.

#### Lợi ích và Hoàn vốn đầu tư (ROI)

- **Tối ưu chi phí tối đa (lên đến 95%)**: Không có máy chủ nhàn rỗi. Tận dụng triệt để gói AWS Free Tier cho S3, Lambda và DynamoDB. Tích hợp sẵn chính sách FinOps cảnh báo ngân sách tự động.
- **Vận hành không chạm (Zero-Ops)**: AWS chịu trách nhiệm hoàn toàn về bảo trì hạ tầng. Tích hợp sẵn hệ thống giám sát và cảnh báo lỗi chủ động qua CloudWatch.
- **Mở rộng quy mô tức thì**: Khả năng phục vụ từ 1 người dùng đến hàng chục nghìn người dùng cùng lúc mà không cần can thiệp cấu hình thủ công.

---

### 3. Kiến trúc giải pháp

#### Sơ đồ kiến trúc tổng thể

![Serverless Note App Architecture](/images/2-Proposal/a.png)

#### Chi tiết các luồng xử lý chính trong kiến trúc:

##### 1. Tầng Giao diện & Lưu trữ tĩnh (Static Web Hosting)

- Mã nguồn ứng dụng (gồm file `index.html`, style CSS và logic JavaScript) được lưu trữ trên **Amazon S3 Bucket**.
- Bucket được cấu hình tính năng Static Website Hosting và thiết lập Bucket Policy cho phép Public Read.

##### 2. Tầng Điều phối & Xử lý (API & Compute Layer)

- **Amazon API Gateway** cung cấp một điểm cuối (REST API Endpoint) an toàn, được cấu hình CORS cho phép domain bên ngoài gọi HTTP POST/GET/DELETE.
- API Gateway kích hoạt hàm **AWS Lambda** đảm nhiệm việc parse dữ liệu JSON, tạo ID định danh duy nhất (UUID) và chuẩn bị payload.

##### 3. Tầng Cơ sở dữ liệu (Database Layer)

- Hàm Lambda sử dụng AWS SDK để gọi các phương thức tương tác trực tiếp vào bảng **Amazon DynamoDB**.
- Bảng DynamoDB sử dụng thiết kế Single-Table với Partition Key là `id` (String).

#### Dịch vụ & Công cụ sử dụng

- **Amazon S3**: Lưu trữ và phân phối giao diện web tĩnh.
- **Amazon API Gateway**: Cổng giao tiếp REST API, quản lý CORS.
- **AWS Lambda**: Thực thi mã nguồn xử lý logic Backend.
- **Amazon DynamoDB**: Cơ sở dữ liệu NoSQL tốc độ cao.
- **AWS IAM**: Quản lý phân quyền chặt chẽ theo nguyên tắc đặc quyền tối thiểu (Least Privilege).
- **Amazon CloudWatch & SNS**: Hệ thống giám sát (Dashboard) và phát cảnh báo (Alarms) tự động qua email khi hệ thống phát sinh lỗi.
- **AWS Budgets**: Thiết lập rào chắn tài chính (FinOps) cảnh báo ngân sách.
- **GitHub Actions**: Nền tảng tự động hóa triển khai CI/CD.

---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai dự án

1.  **Giai đoạn 1: Chuẩn bị Môi trường & Database**
    - Thiết lập **AWS Budgets** để quản trị rủi ro chi phí.
    - Tạo bảng DynamoDB `NotesTable` với khóa chính `id`.
    - Tạo IAM Execution Role với Custom Policy giới hạn quyền trên đúng bảng `NotesTable`.
2.  **Giai đoạn 2: Xây dựng Backend Logic**
    - Khởi tạo hàm AWS Lambda (`NoteHandler`) đóng vai trò làm Backend trung tâm xử lý dữ liệu (CRUD).
3.  **Giai đoạn 3: Tích hợp API Gateway & Giám sát**
    - Tạo REST API/HTTP API công khai, liên kết với hàm Lambda và cấu hình CORS.
    - Xây dựng **CloudWatch Dashboard** giám sát lưu lượng và thiết lập **CloudWatch Alarms** kết hợp SNS để báo động nếu API gặp sự cố.
4.  **Giai đoạn 4: Lưu trữ S3 & Tự động hóa CI/CD (GitOps)**
    - Tạo Amazon S3 Bucket, kích hoạt Public Static Website Hosting.
    - Khởi tạo kịch bản **GitHub Actions** (`main.yml`) với các biến bảo mật IAM User để tự động triển khai mã nguồn Frontend lên S3.
5.  **Giai đoạn 5: Kiểm thử, Đánh giá & Dọn dẹp**
    - Kiểm thử End-to-End toàn bộ hệ thống. Xóa tài nguyên (Clean-up) để bảo vệ ngân sách.

#### Yêu cầu kỹ thuật & Bảo mật

- **Quyền hạn tối thiểu (Least Privilege)**: Tách bạch quyền hạn thông qua AWS IAM.
- **Quản trị tài nguyên mạng**: Bảo mật điểm cuối API Gateway bằng kiểm soát CORS.
- **Bảo vệ dữ liệu**: Mã hóa tự động qua giao thức HTTPS.

---

### 5. Lộ trình & Mốc triển khai

```text
+-----------------------------------------------------------------------------------+
| Giai đoạn 1: Cấu hình FinOps & Khởi tạo Database                                  |
|   - Thiết lập AWS Budgets. Tạo bảng Amazon DynamoDB.                              |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Giai đoạn 2 & 3: Xây dựng Backend, API Gateway & Monitoring                       |
|   - Viết logic AWS Lambda và định tuyến qua Amazon API Gateway.                   |
|   - Thiết lập CloudWatch Dashboard & Alarms cảnh báo chủ động.                    |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Giai đoạn 4: Triển khai Frontend, Lưu trữ S3 & Tự động hóa CI/CD                  |
|   - Tạo S3 Bucket tĩnh. Cấu hình GitHub Actions tự động hóa luồng GitOps.         |
+-----------------------------------------------------------------------------------+
```

### 6. Ước tính ngân sách

Dưới đây là bảng dự toán chi phí hàng tháng giả định hệ thống hoạt động ở môi trường Sản xuất (Production) với lưu lượng khoảng **5 triệu lượt tương tác/tháng**:

| Dịch vụ AWS               | Quy mô giả định (Production)                     | Chi phí ước tính / Tháng (USD) |
| ------------------------- | ------------------------------------------------ | ------------------------------ |
| **Amazon API Gateway**    | 5,000,000 REST API requests                      | ~$5.00                         |
| **AWS Lambda**            | 5,000,000 requests, 128MB RAM, 200ms/req         | ~$1.20                         |
| **Amazon DynamoDB**       | On-Demand Mode, 10GB dữ liệu, 5 triệu Write/Read | ~$3.50                         |
| **Amazon S3**             | 20GB lưu trữ tài nguyên tĩnh, 50GB Bandwidth     | ~$4.50                         |
| **Amazon Route 53**       | Quản lý DNS Host Zone (1 Domain)                 | ~$0.50                         |
| **Amazon CloudWatch**     | Lưu trữ log & duy trì Dashboard/Alarms           | ~$2.50                         |
| **Tổng chi phí ước tính** | **Hệ thống Serverless phục vụ 5 triệu request**  | **~$17.20 USD / Tháng**        |

> **💡 TIP - Điểm tối ưu chi phí vượt trội:**
> Nếu triển khai theo mô hình cũ (cần ít nhất 2 máy chủ ảo EC2 và 1 RDS Database chạy liên tục 24/7), chi phí cố định sẽ rơi vào khoảng **$45 - $60/tháng**. Mô hình Serverless tiết kiệm tới **70% ngân sách**. Dự án cũng được bảo vệ tuyệt đối bằng cơ chế cảnh báo của **AWS Budgets**.

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro & Chiến lược giảm thiểu

| Rủi ro tiềm ẩn                             | Mức độ ảnh hưởng | Xác suất   | Chiến lược giảm thiểu                                                                                                                            |
| ------------------------------------------ | ---------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Chi phí phát sinh ngoài ý muốn**         | Cao              | Thấp       | Đã thiết lập AWS Budgets gửi email cảnh báo ngay lập tức nếu chi phí chạm ngưỡng dự báo.                                                         |
| **Lỗi Cross-Origin (CORS) từ trình duyệt** | Cao              | Cao        | Kiểm tra cấu hình Enable CORS trên API Gateway và Header phản hồi của Lambda.                                                                    |
| **Hệ thống lỗi không được phát hiện kịp**  | Cao              | Trung bình | Đã thiết lập CloudWatch Alarms theo dõi chỉ số Errors của Lambda, kết hợp SNS gửi email báo động cho đội ngũ vận hành trong vòng 5 phút.         |
| **Lộ lọt thông tin Endpoint API**          | Trung bình       | Thấp       | API Gateway mặc định có cơ chế chống dội bom request (throttling). Có thể thiết lập thêm Usage Plan và API Key để giới hạn truy cập gắt gao hơn. |

---

### 8. Kết quả kỳ vọng

- **Làm chủ công nghệ Đám mây hiện đại**: Ứng dụng thuần thục 4 dịch vụ cốt lõi của AWS (S3, API Gateway, Lambda, DynamoDB).
- **Vận hành Tiêu chuẩn Doanh nghiệp (Enterprise-Grade)**: Tích hợp thành công luồng CI/CD tự động (GitOps), kết hợp tư duy kiểm soát tài chính (FinOps) và giám sát hệ thống chủ động (Monitoring & Alerting).
- **Khẳng định tính ưu việt của Serverless**: Loại bỏ hoàn toàn gánh nặng quản lý hạ tầng vật lý, giúp ứng dụng tự động co giãn theo nhu cầu thực tế với chi phí tối ưu nhất.

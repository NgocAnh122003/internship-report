---
title: "Blog 1"
date: 2026-09-22
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AWS Lambda – Chạy code mượt mà không cần bận tâm chuyện quản lý máy chủ

Chào mọi người,

Trong hành trình tìm hiểu và làm quen với hệ sinh thái Amazon Web Services (AWS), mình đã khá ấn tượng với một dịch vụ mang tên AWS Lambda. Trước đây, cứ mặc định trong đầu là hễ muốn đưa một ứng dụng hay trang web lên mạng chạy thì kiểu gì cũng phải dựng một con máy chủ EC2, loay hoay cài đặt hệ điều hành rồi cấu hình web server đủ kiểu. Thế nhưng, khi tiếp cận với khái niệm Serverless thông qua Lambda, tư duy đó đã hoàn toàn thay đổi.

Hôm nay, mình xin tóm tắt lại những điểm cốt lõi và góc nhìn cá nhân sau khi vọc vạch về dịch vụ này nhé!

### 1. AWS Lambda thực chất là gì?

Đơn giản thì đây là dịch vụ tính toán phi máy chủ (Serverless Computing). Thay vì bắt tay vào việc tự quản lý hạ tầng phần cứng, lo vá lỗi hệ điều hành hay cấu hình mạng phức tạp, công việc của bạn chỉ là viết code, đóng gói rồi đẩy lên AWS. Toàn bộ khâu vận hành phía sau đã có Lambda đảm nhận.

Nhờ vậy, lập trình viên được giải phóng khỏi gánh nặng hạ tầng để tập trung tối đa vào phần logic sản phẩm.

### 2. Cơ chế vận hành theo sự kiện (Event-Driven)

Khác với các server truyền thống phải chạy ngầm 24/7, Lambda chỉ thức dậy hoạt động khi có một sự kiện (Event) kích hoạt nó. Ví dụ như:

- Người dùng vừa tải một bức ảnh lên Amazon S3.
- Một HTTP Request được bắn đến API Gateway.
- Có một bản ghi mới vừa được ghi nhận vào DynamoDB.
- Hoặc lịch trình chạy tự động được định nghĩa sẵn trên Amazon EventBridge.

Ngay khi xử lý xong yêu cầu, đoạn code sẽ trả về kết quả và toàn bộ tài nguyên tính toán sẽ được thu hồi ngay lập tức.

### 3. Những điểm "ăn tiền" của AWS Lambda

- **Giải tỏa nỗi lo quản lý hạ tầng:** Không cần dựng máy chủ, không cần cài đặt môi trường hay lo cấu hình mở rộng thủ công.
- **Tự động co giãn (Auto-Scaling) linh hoạt:** Dù là 1 hay hàng nghìn request dồn dập cùng lúc, Lambda tự động cấp phát các phiên chạy tương ứng để xử lý trơn tru, sau đó tự thu hẹp lại khi vắng khách mà không cần cấu hình phức tạp.
- **Tối ưu chi phí tuyệt đối (Pay-as-you-go):** Hệ thống chỉ tính phí dựa trên thời gian code thực thi tính bằng mili-giây. Khi không có request nào gọi đến, chi phí phát sinh gần như bằng không.

### 4. Ứng dụng thực tế phổ biến

Trong các hệ sinh thái hiện đại, Lambda thường xuyên được "chọn mặt gửi vàng" cho các bài toán như:

- Xử lý file, định dạng ảnh ngay sau khi upload lên S3.
- Làm tầng backend xử lý logic REST API kết hợp với API Gateway.
- Tự động hóa các tác vụ quản trị, gửi email thông báo định kỳ.
- Xử lý luồng log và sự kiện phát sinh từ CloudWatch.

### Lời kết

Đối với mình, AWS Lambda là một mảnh ghép tuyệt vời giúp thể hiện rõ ưu thế của điện toán đám mây hiện đại. Nó giúp rút ngắn đáng kể thời gian đưa sản phẩm ra mắt và giảm tải áp lực quản trị hạ tầng cho đội ngũ phát triển.

Cảm ơn mọi người đã dành thời gian đọc bài!

---

- **Link bài viết Facebook**: https://www.facebook.com/groups/awsstudygroupfcj/posts/2284411392323820

#AWS #Serverless #AWSLambda #CloudComputing #TechSharing

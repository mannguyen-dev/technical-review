# System Design Interview — Tổng quan theo CV Nguyen Ngoc Man

## Mục tiêu tài liệu

Bộ câu hỏi này được cá nhân hóa theo các project trong CV của bạn, tập trung vào vị trí **Java Backend / Java SE / Senior Backend Engineer**. Nội dung không phải system design chung chung, mà bám vào các hệ thống bạn đã ghi trong CV:

1. **User Notification System Modernization** — AWS serverless, event-driven, 1M+ events/day.
2. **Banking Teller Platform Development** — Java Spring Boot, DDD, Kafka, OpenShift, banking operations.
3. **Batch System Modernization** — legacy modernization, Spring Boot microservices, SQL Server, MongoDB, Kubernetes.

## Cách interviewer có thể khai thác CV của bạn

Vì CV của bạn có nhiều keyword senior như microservices, DDD, event-driven, AWS, Kafka, CI/CD, monitoring, code quality, interviewer có thể không hỏi “design Twitter” hay “design URL shortener” mà hỏi trực tiếp:

- “Bạn design notification system xử lý 1M events/day như thế nào?”
- “Trong banking project, bạn đảm bảo audit event không bị mất bằng cách nào?”
- “Batch system 100K records/day nếu fail giữa chừng thì rerun thế nào?”
- “SQS/EventBridge/Kafka khác nhau thế nào? Vì sao chọn cái này?”
- “Nếu API hoặc consumer bị duplicate request/event thì xử lý thế nào?”
- “Bạn monitor production và phát hiện incident bằng metric nào?”

## 3 project chính trong CV

### 1. User Notification System Modernization

**Domain/chức năng chính:**

- Nhận event từ nhiều producer.
- Route event theo rule/type.
- Queue và xử lý async.
- Gửi notification hoặc xử lý notification workflow.
- Lưu trạng thái event/notification.
- Retry/DLQ khi lỗi.
- Monitoring/alerting production.

**Công nghệ trong CV:**

- AWS Lambda.
- SQS.
- EventBridge.
- DynamoDB.
- S3.
- AWS CDK.
- GitHub Actions.
- CloudWatch.
- Slack alert.
- SonarQube.

**Thành phần system design có thể bị hỏi:**

- Event ingestion API hoặc event producer.
- Event routing.
- Queue design.
- Worker/Lambda processing.
- DynamoDB data model.
- Idempotency.
- Retry/DLQ.
- Rate limiting/throttling.
- Observability.
- CI/CD và IaC.
- Security/code quality.

### 2. Banking Teller Platform Development

**Domain/chức năng chính:**

- Hỗ trợ giao dịch/nghiệp vụ teller tại nhiều chi nhánh.
- Xử lý cash flow, ATM events, audit events.
- Backend services cho daily banking operations.
- Integration giữa microservices.
- Production deployment và monitoring.

**Công nghệ trong CV:**

- Java.
- Spring Boot.
- DDD.
- Kafka.
- OpenShift.
- Jenkins CI/CD.
- Kibana.

**Thành phần system design có thể bị hỏi:**

- REST API cho teller operations.
- Service boundary theo DDD.
- Relational database design.
- Transaction consistency.
- Audit logging.
- Kafka event design.
- Idempotent consumer.
- Authentication/authorization.
- Deployment strategy trên OpenShift.
- Logging/tracing qua Kibana.

### 3. Batch System Modernization

**Domain/chức năng chính:**

- Modernize legacy batch jobs từ VB6/Lotus Notes sang Java và MongoDB.
- Xử lý batch records/reporting/survey/business data.
- Hỗ trợ 100K+ batch records/day.
- Giảm manual operations.
- Release ổn định bằng Jenkins/Kubernetes.

**Công nghệ trong CV:**

- Spring Boot.
- SQL Server.
- MongoDB.
- Jenkins.
- Kubernetes.
- Legacy VB6/Lotus Notes.

**Thành phần system design có thể bị hỏi:**

- Batch orchestration.
- Chunk processing.
- Job status table.
- Retry failed records.
- Idempotent rerun.
- Data reconciliation.
- SQL Server vs MongoDB split.
- Migration strategy.
- Backward compatibility.
- Deployment và rollback.

## Mức độ câu hỏi

### Easy

Kiểm tra bạn hiểu component và flow cơ bản:

- API nào cần có?
- Database lưu gì?
- Queue dùng để làm gì?
- Vì sao cần logging/monitoring?

### Medium

Yêu cầu giải thích thiết kế có logic:

- Request/event flow end-to-end.
- Database schema/key design.
- Transaction boundary.
- Retry/idempotency.
- Auth/security cơ bản.
- Deployment flow.

### Hard

Kiểm tra tư duy senior/distributed systems:

- Scale 1M+ events/day.
- Fault tolerance.
- Event duplication/loss.
- Ordering.
- Hot partition.
- Backpressure.
- Caching strategy.
- Consistency trong microservices.
- Incident detection và recovery.

## Cách sử dụng bộ tài liệu

1. Đọc [01-project-questions.md](01-project-questions.md) để luyện câu hỏi theo từng project.
2. Đọc [02-api-database-design.md](02-api-database-design.md) để chuẩn bị API/schema/transaction.
3. Đọc [03-scalability-performance.md](03-scalability-performance.md) để trả lời câu hỏi senior.
4. Dùng [04-answer-frameworks.md](04-answer-frameworks.md) để luyện cách trình bày câu trả lời.
5. Trước phỏng vấn, mở [05-quick-review-checklist.md](05-quick-review-checklist.md).


## Bổ sung theo tên khách hàng

Xem thêm [06-customer-context-system-design.md](06-customer-context-system-design.md) để ôn system design theo customer/domain cụ thể: Cox Automotive, OCBC Bank và Zurich Insurance.

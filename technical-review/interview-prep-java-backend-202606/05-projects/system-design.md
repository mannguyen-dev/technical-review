# System Design / Architecture Basics cho CV Java Backend

## Mức độ ưu tiên

**Rất cao cho senior backend**. CV của bạn có cloud-native, event-driven, microservices, DDD, Clean Architecture.

## Framework trả lời system design

1. Clarify requirements.
2. Define APIs/events.
3. Estimate scale.
4. High-level architecture.
5. Data model.
6. Reliability/scalability/security.
7. Trade-offs.

## Design: Notification Platform

### Requirements giả định

Functional:

- Receive notification events.
- Route by type/channel.
- Send email/SMS/push/in-app.
- Track status.
- Retry failed notifications.

Non-functional:

- 1M+ events/day.
- High reliability.
- Idempotency.
- Monitoring/alerting.
- Cost efficient.

### Architecture gợi ý

```text
Producer Services
      ↓
  EventBridge
      ↓
     SQS  → DLQ
      ↓
   Lambda Workers
      ↓
Notification Provider
      ↓
 DynamoDB status store
      ↓
CloudWatch + Slack Alert
```

### Điểm cần nhấn mạnh

- SQS decouple và buffer.
- DLQ để không mất failed messages.
- Lambda scale theo event volume.
- DynamoDB lưu status/idempotency.
- CloudWatch metrics: queue depth, error rate, DLQ count, latency.

## Design: Banking Teller Microservices

### Requirements giả định

- Teller operations across branches.
- Cash transaction.
- ATM/cash flow/audit events.
- Strong audit and consistency.
- Secure access.

### Architecture gợi ý

```text
Frontend/Teller App
      ↓
API Gateway / Backend APIs
      ↓
Spring Boot Services
      ↓              ↓
Relational DB      Kafka Topics
      ↓              ↓
Audit Service     Downstream Consumers
```

### Điểm cần nhấn mạnh

- Transaction boundary trong service.
- Audit event không được mất.
- Idempotent consumer.
- OpenShift deployment, Jenkins CI/CD.
- Kibana logging/troubleshooting.

## Design: Batch Modernization

### Architecture gợi ý

```text
Scheduler
   ↓
Batch Orchestrator
   ↓
Chunk/Record Processor
   ↓
SQL Server / MongoDB
   ↓
Reconciliation Report + Monitoring
```

### Điểm cần nhấn mạnh

- Chunk processing.
- Retry failed records.
- Idempotent rerun.
- Reconciliation old vs new.
- Parallel processing có kiểm soát.

## Câu hỏi system design

### Easy

1. Microservices có lợi ích gì?
2. Event-driven architecture là gì?
3. Cache dùng để làm gì?

### Medium

1. Khi nào dùng async messaging thay vì REST synchronous?
2. Làm sao thiết kế retry không gây duplicate?
3. Redis cache invalidation xử lý thế nào?

### Hard

1. Design notification system support 1M events/day.
2. Design audit logging for banking transactions.
3. Design batch processing system 100K records/day with rerun support.
4. Compare Kafka vs SQS for distributed system.
5. How to handle eventual consistency in microservices?

## Trade-off cần nói được

### Kafka vs SQS

- Kafka: high throughput streaming, replay, ordering per partition, consumer groups.
- SQS: managed queue, simple decoupling, retry/DLQ, serverless integration.

### SQL vs DynamoDB

- SQL: transaction/query relational mạnh.
- DynamoDB: scale managed, key-value access pattern rõ, low ops overhead.

### Lambda vs Container Service

- Lambda: scale tự động, pay-per-use, ít vận hành.
- Container: kiểm soát runtime tốt hơn, phù hợp long-running/complex workloads.

## Câu trả lời mẫu: “Design notification system 1M events/day”

> Đầu tiên em clarify requirement: loại notification, latency expectation, ordering có cần không, provider nào, retry policy và retention. Với 1M events/day, trung bình khoảng 12 events/second nhưng cần tính peak traffic cao hơn nhiều.
>
> Em sẽ thiết kế event-driven architecture: producer publish event vào EventBridge, route theo event type sang SQS queue tương ứng. Lambda consumer đọc từ SQS, validate, check idempotency trong DynamoDB, gọi notification provider và update status. Failed message retry theo SQS policy, sau số lần nhất định vào DLQ. CloudWatch monitor queue depth, error rate, Lambda duration/throttle và DLQ count, alert qua Slack.
>
> Điểm quan trọng là idempotency vì SQS thường at-least-once. Em dùng eventId/idempotency key và conditional write trong DynamoDB để tránh process trùng. Nếu cần ordering theo user, có thể cân nhắc SQS FIFO hoặc partitioning strategy khác, nhưng phải đánh đổi throughput/cost.

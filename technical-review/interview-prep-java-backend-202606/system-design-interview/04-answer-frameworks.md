# Answer Frameworks — Cách trả lời System Design theo CV

File này giúp bạn trả lời có cấu trúc, tránh lan man và thể hiện level senior.

---

# 1. Framework chung cho câu hỏi system design

Khi được hỏi “Design X”, trả lời theo 7 bước:

```text
1. Clarify requirements
2. Define scale/SLA
3. High-level architecture
4. Data model/API/event contract
5. Failure handling/reliability
6. Observability/deployment
7. Trade-offs and alternatives
```

## Ví dụ mở đầu tốt

> Trước khi đi vào design, em muốn clarify một vài requirement: hệ thống cần xử lý bao nhiêu request/event, latency expectation là gì, có yêu cầu ordering không, consistency cần strong hay eventual, và có những external providers/downstream systems nào?

## Lỗi cần tránh

- Nhảy ngay vào công nghệ.
- Không hỏi requirement.
- Không nói trade-off.
- Không nói failure scenarios.

---

# 2. Framework cho Notification System

## Câu hỏi thường gặp

> Design notification system support 1M events/day.

## Câu trả lời mẫu có cấu trúc

### 1. Clarify

> Em cần clarify: notification có những channel nào, latency SLA bao nhiêu, có cần ordering không, event đến từ những producer nào, provider có rate limit không, và retention/status tracking cần bao lâu.

### 2. Estimate

> 1M events/day trung bình khoảng 12 events/sec, nhưng em sẽ thiết kế theo peak traffic, ví dụ 10x hoặc cao hơn nếu có campaign. Bottleneck có thể nằm ở provider, queue backlog hoặc DynamoDB partition.

### 3. Architecture

```text
Producer Services
  → EventBridge
  → SQS queues by channel/type
  → Lambda workers
  → Notification providers
  → DynamoDB status/idempotency store
  → CloudWatch/Slack alerts
```

### 4. Data model

- Event có eventId, type, channel, recipient, payloadRef, createdAt, correlationId.
- DynamoDB lưu status/idempotency.
- TTL cho idempotency nếu phù hợp.

### 5. Reliability

- SQS retry + DLQ.
- Idempotency key + conditional write.
- Timeout/backoff khi gọi provider.
- DLQ replay process.

### 6. Observability

- Queue depth, age of oldest message.
- Lambda error/duration/throttle.
- DLQ count.
- Provider latency/error.
- Business sent/failed count.

### 7. Trade-off

> SQS/EventBridge phù hợp với AWS serverless và queue-based async processing. Nếu cần streaming replay lâu dài hoặc ordering theo partition phức tạp, Kafka có thể phù hợp hơn nhưng operational complexity cao hơn.

---

# 3. Framework cho Banking Teller Platform

## Câu hỏi thường gặp

> Design backend service for teller cash transaction.

## Câu trả lời mẫu có cấu trúc

### 1. Clarify

> Em cần biết transaction type là deposit/withdrawal/transfer, có cần supervisor approval không, consistency requirement, audit requirement, và integration với core banking system như thế nào.

### 2. API

```http
POST /api/v1/teller/transactions
Idempotency-Key: <uuid>
```

Request gồm branchId, tellerId, accountId, amount, currency, type.

### 3. Service flow

```text
Controller
  → validate request/authz
  → TransactionService @Transactional
  → write transaction DB
  → write audit/outbox event
  → return transaction status
Outbox publisher
  → Kafka audit/cash-flow event
```

### 4. Database

- transaction table.
- audit table.
- idempotency table.
- outbox table.
- index theo account/time, branch/time.

### 5. Consistency

- Local DB transaction cho business data + outbox.
- Idempotency key chống duplicate submit.
- Locking/version nếu concurrent update.
- Kafka consumer idempotent.

### 6. Security

- AuthN/AuthZ theo role/branch.
- Audit all critical actions.
- Mask sensitive data in logs/events.

### 7. Observability/deployment

- CorrelationId xuyên suốt API/Kafka.
- Kibana logs.
- OpenShift replicas, readiness/liveness, rolling deploy.

---

# 4. Framework cho Batch Modernization

## Câu hỏi thường gặp

> Design batch processing system for 100K records/day and safe rerun.

## Câu trả lời mẫu có cấu trúc

### 1. Clarify

> Em cần biết batch chạy theo schedule hay trigger, input source là DB/file/API, mỗi record xử lý độc lập không, có yêu cầu ordering không, và SLA hoàn tất trong bao lâu.

### 2. Architecture

```text
Scheduler
  → Batch Orchestrator
  → Chunk Processor
  → SQL Server/MongoDB
  → Reconciliation Report
  → Monitoring/Alerting
```

### 3. Job metadata

- batch_job_execution.
- batch_record_execution.
- checkpoint.

### 4. Rerun/idempotency

- Business key cho record.
- Record status: pending/processing/success/failed.
- Commit theo chunk.
- Retry failed records.
- Upsert/unique constraint để không duplicate output.

### 5. Performance

- Chunk size tuning.
- Bulk operations.
- Indexing.
- Parallel partitions nếu business cho phép.
- Avoid loading all records into memory.

### 6. Migration

- Reverse engineer legacy logic.
- Golden dataset.
- Parallel run old/new.
- Reconciliation.
- Feature flag/cutover/rollback.

---

# 5. Framework trả lời câu hỏi trade-off

## Kafka vs SQS/EventBridge

Trả lời theo bảng:

| Tiêu chí | Kafka | SQS/EventBridge |
|---|---|---|
| Use case | Streaming, replay, high throughput | Managed queue/event routing AWS-native |
| Ordering | Per partition | FIFO queue nếu cần, Standard không strict |
| Replay | Tốt theo retention | DLQ/re-drive, không phải event log dài hạn |
| Ops | Phức tạp hơn | Managed đơn giản hơn |
| CV mapping | Banking cash/audit events | AWS notification platform |

Câu trả lời ngắn:

> Em chọn Kafka khi cần event streaming, replay, consumer groups và throughput cao trong hệ sinh thái microservices. Em chọn SQS/EventBridge khi workload phù hợp queue-based async processing trên AWS, cần managed service, retry/DLQ và routing đơn giản. Quan trọng là requirement: ordering, replay, throughput, operational complexity và cloud ecosystem.

---

## SQL vs DynamoDB/MongoDB

| Tiêu chí | SQL | DynamoDB/MongoDB |
|---|---|---|
| Consistency/transaction | Mạnh | Tùy DB/use case |
| Query linh hoạt | Tốt | Phụ thuộc access pattern/index |
| Schema | Rõ, relational | Flexible/document/key-value |
| Use case CV | Banking, reporting, batch structured data | Notification status/event metadata, flexible documents |

Câu trả lời ngắn:

> Với banking transaction, em ưu tiên SQL vì relational model, transaction và audit query quan trọng. Với notification event/status scale lớn và access pattern rõ, DynamoDB phù hợp vì managed scaling và conditional write cho idempotency. MongoDB phù hợp khi dữ liệu dạng document/schema linh hoạt như survey/report payload.

---

## Lambda vs Container/Spring Boot service

| Tiêu chí | Lambda | Container service |
|---|---|---|
| Scaling | Auto scale theo event | Scale pods/instances |
| Runtime | Short-lived | Long-running |
| Ops | Ít vận hành server | Kiểm soát runtime tốt hơn |
| Cold start | Có thể có | Ít vấn đề hơn |
| CV mapping | Notification serverless | Banking/Batch Spring Boot trên OpenShift/K8s |

Câu trả lời ngắn:

> Lambda phù hợp event-driven workload ngắn, scale theo queue/event và giảm ops. Container/Spring Boot phù hợp service long-running, REST API phức tạp, cần kiểm soát runtime/thread/connection pool tốt hơn. Trade-off là Lambda có cold start/observability/local testing khác, còn container cần vận hành scaling/deployment nhiều hơn.

---

# 6. Framework xử lý câu hỏi “Bạn đã làm thật không?”

Interviewer có thể hỏi sâu để kiểm tra tính xác thực. Hãy trả lời bằng format:

```text
Trong project đó, phần em trực tiếp làm là...
Team cũng có phần...
Decision quan trọng là...
Challenge thực tế là...
Kết quả đo được là...
```

Ví dụ:

> Trong project notification, phần em trực tiếp tham gia là implement Lambda processing flow, tích hợp SQS/EventBridge/DynamoDB, setup CI/CD với CDK/GitHub Actions và monitoring CloudWatch/Slack. Về architecture, team cùng thảo luận nhưng em đóng góp vào phần event flow, retry/DLQ và idempotency. Challenge thực tế là xử lý duplicate/retry và quan sát lỗi production. Kết quả là hệ thống xử lý hơn 1M events/day và giảm manual processing khoảng 70%.

---

# 7. Checklist câu trả lời senior

Một câu trả lời system design tốt nên có:

- Requirement clarification.
- Scale/traffic estimate.
- API/event contract.
- Data model.
- Transaction/idempotency.
- Failure handling.
- Observability.
- Security nếu domain banking.
- Deployment/rollback.
- Trade-off.

Nếu thiếu 3 mục trở lên, câu trả lời thường bị đánh giá junior/mid hơn senior.

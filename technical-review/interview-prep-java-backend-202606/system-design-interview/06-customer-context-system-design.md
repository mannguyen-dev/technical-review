# Customer Context System Design — Cox Automotive, OCBC Bank, Zurich Insurance

> Lưu ý quan trọng: phần này tổng hợp theo **domain và loại hệ thống thường gặp** của Cox Automotive, OCBC Bank và Zurich Insurance, kết hợp với thông tin trong CV của bạn. Đây không phải mô tả nội bộ/chính thức của khách hàng. Khi phỏng vấn, nên nói theo hướng: “Based on the project context I worked on...” hoặc “In this type of system...” thay vì khẳng định chi tiết confidential.

## 1. Cox Automotive — User Notification System Modernization

### Domain context

Cox Automotive hoạt động trong hệ sinh thái automotive services, dealer platforms, vehicle lifecycle, marketplace/auction, logistics, inspection, financing hoặc service operations. Với một **User Notification System**, các event có thể đến từ nhiều business workflows khác nhau, ví dụ:

- Vehicle listing/update events.
- Appointment/service status events.
- Auction/bidding or transaction lifecycle events.
- Dealer/customer communication events.
- Operational alerts hoặc workflow reminders.

Trong CV của bạn, project này được mô tả là **cloud-native notification platform trên AWS**, xử lý **1M+ events/day**, dùng **Lambda, SQS, EventBridge, DynamoDB, S3, CDK, GitHub Actions, CloudWatch, Slack**.

### System design summary nên trình bày

```text
Business/Event Producers
  → EventBridge event bus
  → Rules route by event type/channel/priority
  → SQS queues per channel or workload type
  → Lambda workers process events
  → DynamoDB stores idempotency/status/metadata
  → S3 stores large payload/templates/artifacts if needed
  → External notification providers or internal delivery services
  → CloudWatch metrics/logs + Slack alerts
```

### Thành phần nên nhấn mạnh khi phỏng vấn

#### Event ingestion

- Event đến từ nhiều producer/service khác nhau.
- Mỗi event cần có `eventId`, `eventType`, `source`, `timestamp`, `correlationId`, `payload`.
- Schema/versioning giúp thay đổi event an toàn.

#### Event routing

- EventBridge phù hợp để route event theo rule.
- Có thể tách rule theo channel, priority, business domain.
- Giúp thêm consumer mới mà ít ảnh hưởng producer.

#### Queue và async processing

- SQS buffer traffic spike và decouple producer/consumer.
- Có thể tách queue theo channel hoặc priority:
  - email queue,
  - SMS queue,
  - push/in-app queue,
  - high-priority queue,
  - retry queue.

#### Idempotency

- Vì SQS/EventBridge có thể deliver duplicate, cần eventId/idempotency key.
- DynamoDB conditional write để đảm bảo một event chỉ được process một lần về mặt business.
- Nếu provider hỗ trợ idempotency key, truyền key sang provider.

#### Retry và DLQ

- Transient error retry với backoff.
- Permanent error không retry vô hạn.
- DLQ lưu failed messages để điều tra/replay.
- Alert khi DLQ count hoặc queue age vượt threshold.

#### DynamoDB design

Access patterns có thể gồm:

1. Check event processed chưa theo eventId.
2. Get notification status.
3. Query notifications theo user/time.
4. Query failed notifications theo status/time.

Key design ví dụ:

```text
PK = EVENT#<eventId>
SK = STATUS
```

Hoặc nếu cần query theo user:

```text
PK = USER#<userId>
SK = NOTIFICATION#<timestamp>#<eventId>
```

GSI cho failed/status query cần tránh hot partition, có thể bucket theo ngày/giờ hoặc shard.

#### Observability

Metrics nên nói:

- Event count by type/channel.
- SQS queue depth.
- Age of oldest message.
- Lambda duration/error/throttle/concurrency.
- DLQ count.
- DynamoDB throttling.
- Provider latency/error rate.
- Business metric: sent/failed/retried notifications.

### Câu hỏi system design có thể bị hỏi

#### Easy

1. Cox Automotive notification system xử lý loại event nào?
2. Vì sao notification nên xử lý async?
3. EventBridge và SQS khác vai trò thế nào?

#### Medium

1. Thiết kế event schema cho notification event.
2. Thiết kế DynamoDB table để lưu notification status và idempotency.
3. Làm sao support nhiều notification channels?
4. Làm sao thiết kế CI/CD bằng AWS CDK và GitHub Actions?

#### Hard

1. 1M+ events/day thì bottleneck có thể nằm ở đâu?
2. Provider down 30 phút thì hệ thống xử lý thế nào?
3. Làm sao tránh gửi duplicate notification?
4. Nếu cần ordering theo user/dealer, bạn thiết kế thế nào?
5. Làm sao tránh hot partition trong DynamoDB?

### Answer pitch ngắn

> In the Cox Automotive project, the notification platform was designed as an AWS event-driven system. Producers published events to EventBridge, rules routed them to SQS queues, and Lambda workers processed them asynchronously. DynamoDB was used for event status and idempotency, while CloudWatch and Slack alerts helped detect incidents faster. The key design concerns were scalability for more than 1M events per day, duplicate event handling, retry/DLQ, provider rate limits, and operational visibility.

---

## 2. OCBC Bank — Banking Teller Platform Development

### Domain context

OCBC Bank là ngân hàng lớn ở Singapore. Banking teller platform trong project của bạn là hệ thống giao dịch tại quầy theo mô hình **dual screen**: bank teller và customer tương tác trực tiếp với nhau qua hai màn hình. Teller thao tác nghiệp vụ trên màn hình nội bộ; customer xem, nhập hoặc xác nhận thông tin trên màn hình khách hàng. Hai màn hình được đồng bộ real-time thông qua **WebSocket**. Hệ thống liên quan đến nghiệp vụ chi nhánh/branch operations, teller transactions, customer confirmation, payment, insurance, cheque service, account closure, remittance, audit và compliance.

Trong CV của bạn, project này được mô tả là **Banking Teller Platform Development**, dùng **Java Spring Boot, DDD, Kafka, OpenShift, Jenkins, Kibana**, phát triển **5 module nghiệp vụ: Secure Inventory, Payment Insurance, Cheque Service, Account Closure, Remittance**, xử lý **dual-screen teller-customer interaction qua WebSocket**, đồng thời hỗ trợ **cash flow, ATM, audit events** và **daily operations across multiple branches**.

### System design summary nên trình bày

```text
Teller Screen / Branch Client                Customer Screen
        │                                           │
        ├──────── REST/WebSocket ───────┐ ┌─────────┤
        │                               │ │
        ↓                               ↓ ↓
API Gateway / Internal Gateway / WebSocket Gateway
        ↓
Spring Boot Backend Services
    - Session / Dual-Screen Orchestration
    - Payment Module
    - Insurance Module
    - Cheque Service Module
    - Account Closure Module
    - Remittance Module
    - Audit / Event Service
        ↓
Relational Database + Session/Transaction State Store
        ↓
Kafka topics for cash flow / ATM / audit / transaction events
        ↓
Downstream systems / reporting / monitoring
        ↓
OpenShift deployment + Jenkins CI/CD + Kibana logs
```

### Thành phần nên nhấn mạnh khi phỏng vấn

#### Dual-screen WebSocket flow

Flow điển hình:

```text
1. Teller login và mở giao dịch tại quầy.
2. Backend tạo sessionId/transactionId cho giao dịch.
3. Teller screen kết nối WebSocket vào session.
4. Customer screen được pair với cùng session, có thể qua QR/token/device mapping/counter mapping.
5. Teller nhập/chọn thông tin nghiệp vụ.
6. Backend validate và push state update sang customer screen.
7. Customer xem/xác nhận/nhập thông tin cần thiết.
8. Backend nhận customer confirmation, cập nhật transaction state.
9. Teller screen nhận update real-time.
10. Backend hoàn tất giao dịch, ghi DB, audit log và publish event nếu cần.
```

Message types nên có:

```text
SESSION_STARTED
SCREEN_PAIRED
FORM_UPDATED
CUSTOMER_CONFIRM_REQUIRED
CUSTOMER_CONFIRMED
CUSTOMER_REJECTED
TRANSACTION_PROCESSING
TRANSACTION_COMPLETED
SESSION_TIMEOUT
SESSION_CANCELLED
ERROR_OCCURRED
```

Thiết kế quan trọng:

- Backend là source of truth cho session/transaction state.
- WebSocket chỉ là transport real-time, không thay thế transaction validation.
- Mỗi message có `messageId`, `sessionId`, `transactionId`, `type`, `sequence`, `timestamp`, `correlationId`.
- Cần xử lý reconnect: khi client reconnect, fetch latest state theo sessionId.
- Cần timeout/cancel nếu customer không phản hồi.
- Customer screen chỉ nhận dữ liệu được phép hiển thị.
- Tất cả confirmation quan trọng phải được audit.

#### API design

Teller operations cần API rõ ràng, ví dụ:

```http
POST /api/v1/teller/transactions
GET /api/v1/teller/transactions/{transactionId}
GET /api/v1/branches/{branchId}/transactions
```

Request tạo transaction cần:

- `branchId`
- `tellerId`
- `accountId`
- `transactionType`
- `amount`
- `currency`
- `idempotencyKey`
- `correlationId`

#### Authentication / Authorization

- Authentication qua enterprise IAM/SSO/JWT hoặc mechanism nội bộ.
- Authorization theo role: teller, supervisor, branch manager, admin.
- Branch-level permission: teller chỉ thao tác branch được phân quyền.
- Một số giao dịch vượt limit có thể cần supervisor approval.

#### 5 module bạn tham gia

Bạn có thể mô tả 5 module như sau:

| Module | Chức năng có thể mô tả trong phỏng vấn | Điểm system design cần chú ý |
|---|---|---|
| Payment | Hỗ trợ giao dịch thanh toán tại quầy | idempotency, transaction status, audit, customer confirmation |
| Insurance | Hỗ trợ nghiệp vụ bảo hiểm/bancassurance tại quầy | form display/confirmation, validation, integration, document/status tracking |
| Cheque Service | Hỗ trợ nghiệp vụ liên quan cheque | validation, status workflow, audit, possible approval flow |
| Account Closure | Đóng tài khoản theo quy trình ngân hàng | eligibility checks, multi-step confirmation, audit/compliance |
| Remittance | Chuyển tiền/remittance | beneficiary validation, fee/exchange info, confirmation, transaction consistency |

Khi nói về các module này, nên nhấn mạnh rằng tuy nghiệp vụ khác nhau, pattern backend giống nhau:

```text
Start session → Load module-specific form/data → Teller input → Customer review/confirm → Backend validate/process → Audit/event → Complete transaction
```

#### Transaction consistency

- Banking operation cần DB transaction rõ ràng.
- Không dùng Java `synchronized` để giải quyết concurrency nếu service chạy nhiều pod.
- Dùng DB constraint, optimistic/pessimistic locking, transaction isolation phù hợp.
- Idempotency key chống double-submit.

#### Audit logging

Audit là trọng tâm banking:

- Ai thực hiện action?
- Action gì?
- Khi nào?
- Từ branch/device/session nào?
- Transaction/correlation id là gì?
- Before/after hoặc metadata nào cần lưu?

Audit nên append-only, không update/xóa tùy tiện.

#### Kafka events

Các event có thể gồm:

- `CashTransactionCreated`
- `CashTransactionCompleted`
- `ATMEventReceived`
- `AuditEventCreated`
- `BranchCashFlowUpdated`

Event nên có:

```json
{
  "eventId": "uuid",
  "eventType": "CashTransactionCompleted",
  "eventVersion": "1.0",
  "occurredAt": "timestamp",
  "sourceService": "teller-transaction-service",
  "correlationId": "...",
  "payload": {}
}
```

#### Outbox pattern

Vấn đề thường bị hỏi:

> DB transaction commit thành công nhưng publish Kafka fail thì sao?

Câu trả lời nên là:

- Ghi business data và outbox event trong cùng DB transaction.
- Outbox publisher publish Kafka sau.
- Retry publish nếu fail.
- Consumer idempotent.
- Monitor outbox lag.

#### Deployment trên OpenShift

- Stateless Spring Boot services.
- Multiple replicas.
- Readiness/liveness probes.
- Rolling deployment.
- Resource requests/limits.
- ConfigMaps/Secrets.
- Centralized logging với Kibana.

### Câu hỏi system design có thể bị hỏi

#### Easy

1. Banking teller platform gồm những service/module chính nào?
2. Dual-screen teller-customer flow hoạt động như thế nào?
3. Vì sao banking cần audit log?
4. Kafka dùng để làm gì trong banking teller platform?

#### Medium

1. Thiết kế API tạo cash transaction.
2. Thiết kế WebSocket message flow cho dual-screen session.
3. Thiết kế database schema cho teller transaction, session và audit.
4. Làm sao xử lý double-submit từ teller UI hoặc customer confirmation?
5. Kafka event payload nên gồm những field nào?
6. Làm sao phân quyền teller theo branch/role?

#### Hard

1. DB commit thành công nhưng Kafka publish fail thì audit event có mất không?
2. Hai teller cùng thao tác một account/cash resource thì xử lý concurrency thế nào?
3. WebSocket disconnect giữa giao dịch thì recovery như thế nào?
4. Làm sao scale WebSocket khi có nhiều branch/counter?
5. Consumer crash sau khi ghi DB nhưng trước khi commit offset thì sao?
6. Làm sao thiết kế hệ thống banking HA trên OpenShift?
7. Logging thế nào để debug production mà không leak sensitive data?

### Answer pitch ngắn

> In the OCBC Bank teller platform, the system was designed around Spring Boot microservices with DDD-style boundaries for teller operations, dual-screen session orchestration, Secure Inventory, Payment Insurance, Cheque Service, Account Closure, Remittance, cash flow, ATM integration, and audit. Kafka was used for real-time cash flow, ATM, transaction, and audit events, while WebSocket was used to synchronize the teller and customer screens in real time. Because this is a banking domain, the key design concerns were transaction consistency, idempotency, auditability, authorization by role/branch, reliable event publishing through patterns like outbox, and production observability through Kibana and deployment readiness on OpenShift.

---

## 3. Zurich Insurance — Batch System Modernization

### Domain context

Zurich Insurance là insurance domain. Trong project của bạn, hệ thống trọng tâm là **Batch Job Modernization** cho các batch job legacy chạy trên **VB6** và **Lotus Notes**, được modernize sang **Java** và **MongoDB**. Các batch job chủ yếu xử lý thông tin mà **Surveyor** thu thập được trong ngày, ví dụ dữ liệu khảo sát/inspection, form linh hoạt, metadata, trạng thái xử lý và dữ liệu phục vụ downstream/reporting. Các job chạy theo **lịch và thứ tự giờ giấc**, có nhóm job **ban ngày** và nhóm job **ban đêm**, xử lý **100K+ batch records/day**.

### System design summary nên trình bày

```text
Time-based Scheduler / Trigger
  → Batch Orchestrator Service
  → Day Job Sequence / Night Job Sequence
  → Java Chunk/Record Processor Workers
  → MongoDB for surveyor documents, raw payload, status, metadata
  → Job Status / Record Status / Checkpoint collections
  → Reconciliation reports / downstream output
  → Jenkins CI/CD
  → Kubernetes deployment
  → Monitoring/logging/alerts
```

### Thành phần nên nhấn mạnh khi phỏng vấn

#### Legacy modernization strategy

- Không rewrite blind/big-bang nếu rủi ro cao.
- Reverse engineer business rules từ legacy code và BA/QA.
- Golden datasets để so sánh output.
- Parallel run old/new nếu có thể.
- Reconciliation report.
- Feature toggle/cutover/rollback.
- Strangler pattern nếu module có thể tách dần.

#### Day jobs và night jobs

Dự án có các batch job chạy theo thứ tự giờ giấc, gồm job ban ngày và job ban đêm.

Cách mô tả tốt:

- **Day jobs**: xử lý/validate/chuẩn hóa dữ liệu Surveyor thu thập trong ngày, cập nhật trạng thái, chuẩn bị dữ liệu cho bước sau.
- **Night jobs**: chạy khi traffic thấp hơn, thường phù hợp cho aggregation, reconciliation, report generation, downstream synchronization hoặc cleanup.
- Job sequence cần được quản lý bởi scheduler/orchestrator.
- Job sau có thể phụ thuộc job trước, nên cần dependency rule rõ ràng.
- Nếu job trước fail, orchestrator quyết định dừng job sau, skip, hoặc chạy partial tùy business rule.
- Mỗi job cần SLA, alert khi fail hoặc chạy quá lâu.

#### Batch architecture

Batch system cần:

- Job orchestration.
- Job execution status.
- Record-level status.
- Chunk processing.
- Checkpoint.
- Retry failed records.
- Error classification.
- Reconciliation.

#### Job status design

```text
batch_job_execution
  - job_id
  - job_name
  - status
  - started_at
  - ended_at
  - total_count
  - success_count
  - failed_count

batch_record_execution
  - job_id
  - record_id
  - status
  - retry_count
  - error_code
  - error_message
  - updated_at
```

#### Idempotent rerun

- Mỗi record có business key.
- Output dùng unique constraint/upsert.
- Nếu job fail ở 60%, restart chỉ xử lý phần chưa success hoặc failed retryable records.
- Không tạo duplicate output.
- Checkpoint theo chunk hoặc query theo status.

#### Java + MongoDB cho Surveyor batch data

- Legacy system chạy trên VB6/Lotus Notes được chuyển sang Java để dễ maintain, test, deploy và integrate CI/CD.
- MongoDB phù hợp với surveyor data vì dữ liệu dạng document/form, field có thể thay đổi theo loại survey/inspection.
- MongoDB có thể lưu raw payload, normalized fields cần query, processing status, retry count, error reason và metadata.
- Cần index theo businessDate, surveyorId, status, jobId, policy/claim/reference id nếu có.
- Không nói MongoDB luôn nhanh hơn SQL; phải nói theo data shape và access pattern.

#### Performance

- 100K records/day không chỉ nhìn số lượng; cần SLA batch window.
- Bulk operations.
- Chunk size tuning.
- Indexing.
- Avoid N+1.
- Parallel processing theo partition nếu business cho phép.
- Không load toàn bộ data vào memory.

### Câu hỏi system design có thể bị hỏi

#### Easy

1. Vì sao Zurich Insurance cần modernize legacy batch system từ VB6/Lotus Notes sang Java/MongoDB?
2. Batch system khác REST API system thế nào?
3. Surveyor data là gì và vì sao MongoDB phù hợp?
4. Job ban ngày và job ban đêm khác nhau thế nào?

#### Medium

1. Thiết kế batch job execution/status collections cho MongoDB.
2. Batch fail giữa chừng thì resume thế nào?
3. Làm sao đảm bảo job chạy đúng thứ tự giờ giấc và dependency?
4. Làm sao test behavior mới tương thích legacy?
5. Làm sao retry failed records mà không rerun toàn bộ?

#### Hard

1. Làm sao migrate legacy VB6/Lotus Notes batch jobs với minimal downtime?
2. Batch 100K records/day chạy quá lâu thì optimize thế nào?
3. Nếu night job phụ thuộc day job nhưng day job fail thì xử lý thế nào?
4. Làm sao đảm bảo idempotent rerun và không duplicate output?
5. Legacy logic không có document rõ ràng thì reverse engineer thế nào?
6. Làm sao thiết kế reconciliation giữa old và new system?

### Answer pitch ngắn

> In the Zurich Insurance modernization project, the main challenge was modernizing legacy batch jobs from VB6 and Lotus Notes to Java and MongoDB while preserving business behavior. The jobs mainly processed surveyor data collected during the day and were scheduled in ordered day and night job sequences. The design focus was batch orchestration, job dependency management, chunk processing, MongoDB document/status modeling, job and record-level status tracking, safe rerun, retry, and reconciliation. To reduce migration risk, the system should rely on golden datasets, BA/QA validation, parallel run, and rollback/cutover planning.

---

# 4. Cross-customer comparison bạn có thể dùng khi phỏng vấn

## Cox Automotive vs OCBC Bank vs Zurich Insurance

| Khía cạnh | Cox Automotive | OCBC Bank | Zurich Insurance |
|---|---|---|---|
| Main workload | Event-driven notification | Transactional banking APIs + Kafka events | Batch processing modernization |
| Key concern | Scale, retry, idempotency, provider failure | Consistency, audit, security, authorization | Rerun, reconciliation, legacy compatibility |
| Main tech | AWS Lambda/SQS/EventBridge/DynamoDB | Spring Boot/Kafka/OpenShift | Spring Boot/SQL Server/MongoDB/Kubernetes |
| Data model | NoSQL/event status/idempotency | Relational transaction + audit | Job/record status + structured/document data |
| Failure handling | DLQ/retry/backoff | Outbox/idempotent consumer/audit | Checkpoint/retry failed records/reconciliation |
| Monitoring | CloudWatch/Slack | Kibana/log correlation | Job metrics/logs/reports |

## Một câu trả lời tổng hợp mạnh

> Across these projects, the system design concerns were different. For Cox Automotive, the main focus was scalable event-driven notification processing on AWS, so idempotency, retry/DLQ, DynamoDB key design, and provider failure handling were critical. For OCBC Bank, the focus was banking transaction consistency, auditability, authorization, and reliable Kafka event publishing, so patterns like transaction boundary, outbox, and idempotent consumers were important. For Zurich Insurance, the key challenge was legacy batch modernization, where safe migration, record-level retry, checkpointing, idempotent rerun, and reconciliation were more important than low-latency API design.

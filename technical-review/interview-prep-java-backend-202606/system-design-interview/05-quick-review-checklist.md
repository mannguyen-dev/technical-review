# Quick Review Checklist — System Design trước phỏng vấn

Dùng file này để review nhanh 30–60 phút trước buổi phỏng vấn.

---

# 1. Ba project phải nói thật rõ

## User Notification System Modernization

Bạn phải trả lời được:

- Business problem là gì?
- Flow EventBridge → SQS → Lambda → DynamoDB/provider là gì?
- 1M+ events/day scale ra sao?
- Idempotency xử lý thế nào?
- Retry/DLQ xử lý thế nào?
- Monitoring metric nào?
- Vì sao CDK/GitHub Actions giúp deployment dưới 30 phút?

Keyword cần nhớ:

```text
event-driven, async processing, SQS buffer, EventBridge routing,
Lambda concurrency, DynamoDB conditional write, DLQ, CloudWatch alert,
idempotency, retry with backoff, provider rate limit
```

---

## Banking Teller Platform

Bạn phải trả lời được:

- Teller platform hỗ trợ nghiệp vụ gì?
- Spring Boot services chia boundary như thế nào?
- Kafka xử lý cash flow/ATM/audit events ra sao?
- Transaction consistency đảm bảo thế nào?
- Audit event không mất bằng cách nào?
- Idempotent consumer là gì?
- OpenShift/Jenkins/Kibana đóng vai trò gì?

Keyword cần nhớ:

```text
DDD, bounded context, transaction boundary, audit trail,
Kafka consumer group, offset commit, idempotent consumer,
outbox pattern, OpenShift replicas, readiness/liveness, Kibana correlationId
```

---

## Batch System Modernization

Bạn phải trả lời được:

- Vì sao cần modernize legacy VB6/Lotus Notes sang Java/MongoDB?
- Batch architecture gồm những component nào?
- 100K+ records/day xử lý bằng chunk/checkpoint ra sao?
- Job fail giữa chừng recovery thế nào?
- Rerun an toàn như thế nào?
- SQL Server vs MongoDB dùng cho loại data nào?
- Legacy compatibility kiểm tra thế nào?

Keyword cần nhớ:

```text
batch orchestrator, chunk processing, job execution table,
record-level status, checkpoint, idempotent rerun,
reconciliation, parallel run, strangler pattern, rollback plan
```

---

# 2. Những câu hard rất có thể bị hỏi

## Notification

1. Design notification system 1M events/day.
2. SQS duplicate message thì tránh gửi trùng thế nào?
3. Provider down 30 phút thì xử lý thế nào?
4. DynamoDB hot partition tránh thế nào?
5. Nếu cần ordering theo user thì thay đổi gì?

## Banking

1. DB commit thành công nhưng Kafka publish fail thì audit event có mất không?
2. Hai teller cùng update một resource thì xử lý concurrency thế nào?
3. Kafka consumer crash trước khi commit offset thì sao?
4. Logging production banking không leak sensitive data như thế nào?
5. OpenShift horizontal scaling cần lưu ý gì?

## Batch

1. Batch fail ở 60% thì resume thế nào?
2. Rerun batch không duplicate output bằng cách nào?
3. Batch chạy quá lâu thì optimize gì?
4. Legacy không có document thì migration thế nào?
5. Cutover minimal downtime ra sao?

---

# 2.1. Đáp án nhanh cho những câu hard

## Notification

### 1. Design notification system 1M events/day.

Clarify channel/SLA/ordering/provider limit trước. Architecture: Producer → EventBridge → SQS → Lambda workers → provider → DynamoDB status/idempotency → CloudWatch/DLQ. 1M/day chỉ khoảng 12 events/sec average nhưng phải tính peak. Điểm chính: SQS buffer, Lambda concurrency, provider rate limit, DynamoDB key phân tán, retry/DLQ, idempotency và monitoring queue age/error rate.

### 2. SQS duplicate message thì tránh gửi trùng thế nào?

Dùng `eventId`/idempotency key và DynamoDB conditional write hoặc unique constraint. Worker chỉ gửi notification nếu atomic insert/update idempotency record thành công. Nếu duplicate đến sau, skip hoặc trả trạng thái đã xử lý.

### 3. Provider down 30 phút thì xử lý thế nào?

Timeout ngắn, retry backoff có giới hạn, không retry storm. Message được giữ trong queue hoặc vào DLQ sau max retry. Alert khi queue age/DLQ/error rate tăng. Sau khi provider recover thì replay DLQ theo quy trình kiểm soát.

### 4. DynamoDB hot partition tránh thế nào?

Chọn partition key high-cardinality như eventId/userId, tránh key ít giá trị như `STATUS#PENDING`. Với query status/time dùng bucket/shard hoặc GSI thiết kế theo access pattern. Monitor throttling/hot key.

### 5. Nếu cần ordering theo user thì thay đổi gì?

Clarify ordering theo user hay global. Nếu theo user, dùng SQS FIFO với message group id = userId hoặc Kafka partition key = userId. Trade-off là throughput thấp hơn theo từng group/partition và vẫn cần idempotency.

## Banking

### 1. DB commit thành công nhưng Kafka publish fail thì audit event có mất không?

Nếu publish trực tiếp sau DB commit thì có rủi ro mất event. Giải pháp là transactional outbox: ghi business data và outbox event trong cùng DB transaction; background publisher publish Kafka và mark published; retry nếu fail; consumer idempotent.

### 2. Hai teller cùng update một resource thì xử lý concurrency thế nào?

Không dùng `synchronized` vì chạy nhiều pod. Dùng DB transaction, optimistic locking bằng version hoặc pessimistic lock nếu conflict cao, unique/idempotency key chống double submit và audit mọi state transition.

### 3. Kafka consumer crash trước khi commit offset thì sao?

Message sẽ được consume lại theo at-least-once. Vì vậy consumer phải idempotent: lưu processed eventId/unique key trong DB cùng transaction với side effect, commit offset sau khi xử lý thành công.

### 4. Logging production banking không leak sensitive data như thế nào?

Dùng structured logs với correlationId/transactionId/sessionId; mask account/customer/card/token; không log full payload; kiểm soát quyền truy cập Kibana/logs; audit các action quan trọng.

### 5. OpenShift horizontal scaling cần lưu ý gì?

Service nên stateless, session externalized hoặc token-based, readiness/liveness probes, rolling deployment, resource limits, config/secrets, backward-compatible deployment. Với WebSocket cần reconnect strategy và không lưu state quan trọng chỉ trong memory pod.

## Batch

### 1. Batch fail ở 60% thì resume thế nào?

Lưu job execution, record-level status và checkpoint. Khi restart/rerun, skip records đã success hoặc upsert idempotently, chỉ retry failed/pending records nếu business cho phép. Không rerun mù tạo duplicate output.

### 2. Rerun batch không duplicate output bằng cách nào?

Dùng business key/unique constraint/upsert, idempotency theo record, jobName + businessDate, record status và checkpoint. Output operation phải an toàn khi chạy lại.

### 3. Batch chạy quá lâu thì optimize gì?

Measure bottleneck trước: MongoDB query/index, CPU, IO, external dependency. Tune chunk size, dùng bulk operations, index đúng, streaming/pagination, controlled parallelism theo partition và tránh load toàn bộ vào memory.

### 4. Legacy không có document thì migration thế nào?

Reverse engineer VB6/Lotus Notes code, phân tích input/output thực tế, hỏi BA/SME, tạo golden datasets, chạy old vs new song song, reconciliation report và sign-off trước cutover.

### 5. Cutover minimal downtime ra sao?

Dùng strangler/phase-by-job nếu có thể, parallel run, feature flag/routing, data reconciliation, rollback plan, freeze window nếu cần và communication với operation team.

---

# 3. Công thức trả lời nhanh

## Khi hỏi design system

```text
Clarify → Scale → Architecture → Data/API/Event → Reliability → Observability → Trade-off
```

## Khi hỏi duplicate/retry

```text
At-least-once delivery → idempotency key/eventId → atomic check/write → retry/DLQ → monitoring/replay
```

## Khi hỏi DB + Kafka consistency

```text
dual-write problem → transactional outbox → publisher retry → idempotent consumer → monitor outbox lag
```

## Khi hỏi performance

```text
measure first → identify bottleneck → optimize query/chunk/concurrency/cache → monitor result
```

## Khi hỏi production incident

```text
detect → mitigate → root cause → fix → postmortem → prevention
```

---

# 4. Lỗi cần tránh trong phỏng vấn

- Nói “exactly once” quá dễ dàng mà không giải thích idempotency.
- Nói SQS/Kafka không bao giờ duplicate.
- Dùng `synchronized` để xử lý concurrency trong hệ thống nhiều instances.
- Thiết kế DynamoDB không dựa trên access pattern.
- Quên audit/security trong banking.
- Quên retry/DLQ trong event-driven systems.
- Chỉ nói công nghệ, không nói flow và trade-off.
- Không có metric/monitoring trong câu trả lời production.
- Không phân biệt average traffic và peak traffic.

---

# 5. Câu hỏi nên hỏi ngược interviewer

- Hệ thống hiện tại có workload chủ yếu là synchronous API, batch hay event-driven?
- Team đang gặp challenge lớn nhất về scalability, reliability hay maintainability?
- Với senior backend role, expectation về system design/architecture ownership là gì?
- Team đang dùng Kafka/SQS/RabbitMQ hay messaging nào?
- Quy trình incident response và postmortem hiện tại như thế nào?
- Team có áp dụng DDD/Clean Architecture hay coding standard cụ thể không?

# System Design Questions theo từng project trong CV

## Format mỗi câu hỏi

Mỗi câu hỏi gồm:

- **Câu hỏi phỏng vấn**.
- **Mục đích**: interviewer muốn kiểm tra gì.
- **Ý chính nên trả lời**.
- **Lỗi thường gặp cần tránh**.

---

# Project 1 — User Notification System Modernization

## Easy

### Q1. Hãy mô tả high-level architecture của notification platform bạn đã làm.

**Mục đích:**

Kiểm tra bạn có thật sự hiểu hệ thống end-to-end hay chỉ biết từng AWS service riêng lẻ.

**Ý chính nên trả lời:**

- Producer gửi event vào hệ thống.
- EventBridge route event theo rule/type.
- SQS làm buffer, decouple producer/consumer.
- Lambda consume message và xử lý notification.
- DynamoDB lưu status/idempotency/metadata.
- S3 nếu cần lưu payload lớn hoặc artifact.
- CloudWatch/Slack dùng monitoring và alerting.
- GitHub Actions + CDK dùng CI/CD và infrastructure as code.

**Lỗi thường gặp cần tránh:**

- Chỉ liệt kê “Lambda, SQS, EventBridge” mà không nói flow.
- Không nói retry/DLQ/idempotency.
- Nói hệ thống “auto scale nên không cần lo gì” — serverless vẫn cần limits, throttling, observability.

---

### Q2. Trong notification system, SQS dùng để làm gì?

**Mục đích:**

Kiểm tra hiểu biết về async processing và queue.

**Ý chính nên trả lời:**

- Buffer traffic spike.
- Decouple event producer và consumer.
- Hỗ trợ retry.
- Kết hợp DLQ để giữ failed messages.
- Giúp Lambda consume theo tốc độ phù hợp.

**Lỗi thường gặp cần tránh:**

- Nói SQS chỉ để “gửi message” mà không nói decoupling/retry.
- Không đề cập at-least-once delivery và duplicate message.

---

### Q3. EventBridge khác SQS ở điểm nào trong thiết kế của bạn?

**Mục đích:**

Kiểm tra bạn hiểu vai trò từng messaging component.

**Ý chính nên trả lời:**

- EventBridge là event bus/routing layer theo rule.
- SQS là queue để buffer và reliable consumption.
- EventBridge phù hợp fan-out/routing giữa nhiều source/target.
- SQS phù hợp worker processing, retry, DLQ.

**Lỗi thường gặp cần tránh:**

- Nói hai service giống nhau.
- Không phân biệt routing vs buffering/consumption.

---

## Medium

### Q4. Thiết kế flow xử lý một notification event từ lúc nhận đến lúc hoàn tất.

**Mục đích:**

Kiểm tra khả năng mô tả flow end-to-end, state transition và error handling.

**Ý chính nên trả lời:**

- Event có `eventId`, `type`, `recipient`, `channel`, `payload`, `createdAt`.
- Validate schema/input.
- EventBridge route theo type/channel.
- SQS queue nhận event.
- Lambda poll message.
- Check idempotency trong DynamoDB.
- Gọi provider hoặc xử lý business logic.
- Update status: `RECEIVED`, `PROCESSING`, `SENT`, `FAILED`.
- Nếu lỗi transient: retry.
- Nếu lỗi quá số lần: DLQ + alert.

**Lỗi thường gặp cần tránh:**

- Không có status model.
- Không có idempotency.
- Không phân biệt transient error và permanent error.
- Không nói log/correlationId.

---

### Q5. DynamoDB table cho notification event nên thiết kế như thế nào?

**Mục đích:**

Kiểm tra NoSQL data modeling theo access pattern.

**Ý chính nên trả lời:**

- Bắt đầu từ access patterns:
  - Get event by eventId.
  - Query events by user/time.
  - Query failed events.
  - Check idempotency.
- Có thể dùng:
  - `PK = EVENT#<eventId>`, `SK = METADATA` cho lookup/idempotency.
  - Hoặc `PK = USER#<userId>`, `SK = NOTIFICATION#<timestamp>#<eventId>` nếu query theo user/time là chính.
- GSI cho access pattern phụ như status/time.
- TTL cho idempotency records hoặc logs tạm.
- Conditional write để tránh duplicate processing.

**Lỗi thường gặp cần tránh:**

- Thiết kế DynamoDB như relational database.
- Không nói access pattern.
- Chọn partition key dễ hot như `status=FAILED` làm key chính.

---

### Q6. Làm sao đảm bảo một notification không bị gửi trùng khi SQS có thể deliver duplicate message?

**Mục đích:**

Kiểm tra idempotency trong distributed systems.

**Ý chính nên trả lời:**

- Mỗi event có unique `eventId` hoặc idempotency key.
- Lambda thực hiện conditional write/update trong DynamoDB.
- Nếu event đã `SENT` hoặc đang `PROCESSING`, không gửi lại tùy logic.
- Có thể dùng lock/lease ngắn với expiry nếu worker crash.
- Nếu provider hỗ trợ idempotency key, truyền key sang provider.

**Lỗi thường gặp cần tránh:**

- Nói “SQS đảm bảo không duplicate” — sai với Standard SQS.
- Chỉ check memory local — không đúng khi nhiều Lambda instances.
- Check rồi insert không atomic — có race condition.

---

### Q7. CI/CD bằng CDK và GitHub Actions cho hệ thống này nên gồm những stage nào?

**Mục đích:**

Kiểm tra bạn hiểu deployment production-ready, không chỉ code business logic.

**Ý chính nên trả lời:**

- Checkout/build.
- Unit test.
- Static analysis/SonarQube.
- Security/dependency scan nếu có.
- CDK synth/diff.
- Deploy dev/staging.
- Smoke test.
- Manual approval cho prod nếu required.
- Deploy prod.
- Post-deploy monitoring/rollback plan.

**Lỗi thường gặp cần tránh:**

- Chỉ nói “push code là deploy”.
- Không nói test/quality gate.
- Không nói environment separation.

---

## Hard

### Q8. Hệ thống support 1M+ events/day như thế nào? Bạn sẽ tính scale ra sao?

**Mục đích:**

Kiểm tra capacity thinking, peak traffic, bottleneck analysis.

**Ý chính nên trả lời:**

- 1M/day trung bình khoảng 11.6 events/sec, nhưng phải thiết kế theo peak, ví dụ 10x–50x.
- SQS buffer peak.
- Lambda concurrency scale theo queue depth nhưng cần quản lý reserved concurrency.
- DynamoDB capacity/on-demand, partition key phân tán.
- Provider rate limit có thể là bottleneck.
- Backpressure: queue depth, throttling, retry with backoff.
- Monitoring metric: Lambda throttle/error, SQS age, DLQ count, DynamoDB throttling.

**Lỗi thường gặp cần tránh:**

- Chỉ chia trung bình theo ngày và bỏ qua peak.
- Không nhắc downstream provider limit.
- Không nói backpressure.

---

### Q9. Nếu downstream notification provider bị down 30 phút, hệ thống xử lý thế nào?

**Mục đích:**

Kiểm tra fault tolerance và degradation strategy.

**Ý chính nên trả lời:**

- Timeout ngắn khi gọi provider.
- Retry có backoff, không retry storm.
- Message vẫn nằm trong queue hoặc chuyển DLQ sau max retries.
- Alert khi error rate/DLQ/queue age tăng.
- Có replay DLQ sau khi provider recover.
- Có thể circuit breaker hoặc tạm pause consumer nếu provider down.
- Business decision: notification quá hạn có gửi tiếp không?

**Lỗi thường gặp cần tránh:**

- Retry liên tục không backoff.
- Drop message khi provider lỗi.
- Không nói alert và manual recovery.

---

### Q10. Làm sao tránh hot partition trong DynamoDB cho workload notification lớn?

**Mục đích:**

Kiểm tra hiểu biết DynamoDB scaling.

**Ý chính nên trả lời:**

- Chọn partition key có cardinality cao, phân tán đều.
- Tránh dùng key như `STATUS#PENDING` cho toàn bộ event đang pending.
- Nếu query theo status cần GSI có shard key hoặc bucket theo time/random suffix.
- Dùng time bucket hợp lý.
- Monitor throttling/hot keys.

**Lỗi thường gặp cần tránh:**

- Dùng một partition key cho tất cả event theo ngày/status.
- Không hiểu partition key ảnh hưởng throughput.

---

### Q11. Nếu hệ thống cần ordering theo từng user, bạn thay đổi thiết kế như thế nào?

**Mục đích:**

Kiểm tra trade-off ordering vs throughput.

**Ý chính nên trả lời:**

- Clarify ordering toàn hệ thống hay theo user/entity.
- Ordering global rất khó và giảm throughput.
- Nếu ordering theo user, có thể dùng SQS FIFO với message group id = userId.
- Hoặc Kafka partition by userId.
- Trade-off: throughput per message group/partition, hot user, complexity.
- Idempotency vẫn cần.

**Lỗi thường gặp cần tránh:**

- Hứa ordering global dễ dàng.
- Không nói trade-off throughput.

---

# Project 2 — Banking Teller Platform Development

## Easy

### Q12. Hãy mô tả high-level architecture của banking teller platform.

**Mục đích:**

Kiểm tra bạn hiểu hệ thống banking backend end-to-end.

**Ý chính nên trả lời:**

- Teller UI hoặc client gọi REST APIs.
- Spring Boot services xử lý nghiệp vụ.
- Database lưu transaction/customer/branch data.
- Kafka publish cash flow/ATM/audit events.
- OpenShift deploy services.
- Jenkins CI/CD.
- Kibana logs/monitoring.

**Lỗi thường gặp cần tránh:**

- Không nói transaction/audit.
- Chỉ nói “microservices” chung chung.
- Không nêu security/access control.

---

### Q13. DDD trong banking project có thể áp dụng ở đâu?

**Mục đích:**

Kiểm tra bạn hiểu DDD thực tế, không chỉ keyword.

**Ý chính nên trả lời:**

- Dùng ubiquitous language với BA/domain team.
- Chia bounded context: teller operation, cash transaction, audit, ATM integration.
- Aggregate cho transaction/account/cash operation tùy domain.
- Domain service cho business rule phức tạp.
- Repository interface tách domain khỏi persistence.

**Lỗi thường gặp cần tránh:**

- Nói DDD chỉ là chia package `controller/service/repository`.
- Không phân biệt bounded context và technical layer.

---

## Medium

### Q14. Thiết kế REST API cho teller cash transaction như thế nào?

**Mục đích:**

Kiểm tra API design, validation, idempotency, audit.

**Ý chính nên trả lời:**

- Endpoint ví dụ: `POST /api/v1/teller/transactions`.
- Request gồm branchId, tellerId, accountId, amount, currency, type, idempotencyKey.
- Validate amount/currency/account status/permission.
- Response có transactionId, status, timestamp.
- Dùng idempotency key để tránh submit trùng.
- Ghi audit event.
- Error codes rõ: invalid account, insufficient permission, duplicate request, limit exceeded.

**Lỗi thường gặp cần tránh:**

- Không có idempotency key.
- Không nói authorization.
- Không nói audit.

---

### Q15. Database design cho cash transaction cần những bảng/thông tin gì?

**Mục đích:**

Kiểm tra data modeling banking cơ bản.

**Ý chính nên trả lời:**

- `transactions`: transactionId, accountId, branchId, tellerId, amount, currency, type, status, createdAt.
- `transaction_audit`: actor, action, before/after hoặc metadata, timestamp, correlationId.
- `idempotency_keys`: key, requestHash, transactionId, status, expiry.
- Có index theo accountId/time, branchId/time, transactionId.
- Không update/xóa audit tùy tiện; audit nên append-only.

**Lỗi thường gặp cần tránh:**

- Chỉ lưu balance mới, không lưu transaction history.
- Không lưu actor/correlationId.
- Không nghĩ tới audit và reconciliation.

---

### Q16. Kafka event cho cash flow/audit nên thiết kế payload như thế nào?

**Mục đích:**

Kiểm tra event contract và integration design.

**Ý chính nên trả lời:**

- Event có eventId, eventType, version, occurredAt, sourceService, correlationId.
- Business payload: transactionId, branchId, amount, currency, status.
- Không chứa sensitive data quá mức.
- Schema versioning để backward compatibility.
- Key theo transactionId/accountId nếu cần ordering theo entity.

**Lỗi thường gặp cần tránh:**

- Event payload không có version.
- Gửi quá nhiều sensitive data.
- Không có correlationId.

---

### Q17. Consumer xử lý Kafka event fail thì làm thế nào?

**Mục đích:**

Kiểm tra reliability trong event-driven banking.

**Ý chính nên trả lời:**

- Retry transient error.
- Dead-letter topic cho poison message.
- Commit offset sau khi xử lý thành công.
- Idempotent consumer để handle duplicate.
- Alert và replay process.
- Log correlationId/eventId.

**Lỗi thường gặp cần tránh:**

- Commit offset trước khi xử lý xong.
- Không có DLQ/dead-letter topic.
- Không có idempotency.

---

## Hard

### Q18. Làm sao đảm bảo audit event không bị mất khi transaction DB commit thành công nhưng publish Kafka fail?

**Mục đích:**

Kiểm tra outbox pattern và consistency giữa DB/Kafka.

**Ý chính nên trả lời:**

- Vấn đề dual-write: DB commit và Kafka publish không atomic.
- Dùng transactional outbox:
  - Trong cùng DB transaction, ghi business data và outbox event.
  - Background publisher đọc outbox và publish Kafka.
  - Mark published sau khi publish thành công.
- Consumer idempotent.
- Có monitoring cho outbox lag/failures.

**Lỗi thường gặp cần tránh:**

- Nói “try-catch publish lại là đủ”.
- Không nhận ra dual-write problem.
- Không nói idempotency khi publisher retry.

---

### Q19. Nếu hai teller cùng thao tác trên cùng account/cash resource, xử lý concurrency thế nào?

**Mục đích:**

Kiểm tra locking, transaction isolation, race condition.

**Ý chính nên trả lời:**

- Clarify operation: update balance, cash drawer, transaction status.
- Dùng DB transaction.
- Optimistic locking với version nếu conflict thấp.
- Pessimistic lock nếu conflict cao hoặc operation critical.
- Unique constraint/idempotency key chống duplicate submit.
- Isolation level phù hợp.
- Audit mọi attempt/failure nếu cần.

**Lỗi thường gặp cần tránh:**

- Chỉ synchronized trong Java service — không đủ khi nhiều instances.
- Không dùng database constraint/transaction.
- Không nói multi-instance deployment trên OpenShift.

---

### Q20. Banking service cần high availability trên OpenShift như thế nào?

**Mục đích:**

Kiểm tra deployment/scaling/production readiness.

**Ý chính nên trả lời:**

- Multiple replicas.
- Readiness/liveness probes.
- Rolling deployment.
- Resource limits/requests.
- Horizontal scaling.
- Externalized config/secrets.
- Centralized logging với Kibana.
- Health check và rollback strategy.

**Lỗi thường gặp cần tránh:**

- Chỉ nói “deploy lên OpenShift là HA”.
- Không nói readiness/liveness.
- Không nói rollback/monitoring.

---

### Q21. Bạn thiết kế logging/tracing thế nào để debug một giao dịch banking production?

**Mục đích:**

Kiểm tra observability và incident handling.

**Ý chính nên trả lời:**

- CorrelationId/requestId xuyên suốt API và Kafka event.
- Structured logs JSON.
- Log business identifiers an toàn: transactionId, branchId, không log PII/sensitive data.
- Log state transition.
- Kibana query theo correlationId/eventId.
- Metrics: error rate, latency, consumer lag.

**Lỗi thường gặp cần tránh:**

- Log quá ít không debug được.
- Log sensitive data như full account/card info.
- Không có correlationId.

---

# Project 3 — Batch System Modernization

## Easy

### Q22. Hãy mô tả architecture cơ bản của batch modernization system.

**Mục đích:**

Kiểm tra bạn hiểu batch processing flow.

**Ý chính nên trả lời:**

- Scheduler hoặc trigger khởi chạy job.
- Batch orchestrator quản lý job execution.
- Processor xử lý record theo chunk.
- SQL Server/MongoDB lưu input/output/status.
- Jenkins/Kubernetes deploy.
- Logging/monitoring/reporting.

**Lỗi thường gặp cần tránh:**

- Không nói job status và retry.
- Không nói reconciliation.
- Không phân biệt batch processing với request-response API.

---

### Q23. Vì sao cần modernize legacy VB6/Lotus Notes sang Java/MongoDB?

**Mục đích:**

Kiểm tra business/engineering motivation.

**Ý chính nên trả lời:**

- Legacy khó maintain, thiếu developer, khó deploy/test.
- Spring Boot giúp modularize, testable, CI/CD, observability.
- Microservices hoặc modular services giúp scale/change theo capability.
- Giảm manual operations và release risk.

**Lỗi thường gặp cần tránh:**

- Chỉ nói “vì công nghệ cũ”.
- Không nói risk và migration strategy.

---

## Medium

### Q24. Thiết kế job status table cho batch 100K+ records/day như thế nào?

**Mục đích:**

Kiểm tra rerun/retry/operability.

**Ý chính nên trả lời:**

- `batch_job_execution`: jobId, jobType, status, startTime, endTime, total/success/failed count.
- `batch_record_status`: jobId, recordId, status, errorCode, retryCount, lastUpdated.
- Index theo jobId/status.
- Lưu checkpoint nếu xử lý chunk.
- Error detail đủ debug nhưng không quá lớn/sensitive.

**Lỗi thường gặp cần tránh:**

- Chỉ lưu job success/fail tổng thể.
- Không lưu record-level status.
- Không hỗ trợ retry failed records.

---

### Q25. Làm sao thiết kế batch job rerun an toàn?

**Mục đích:**

Kiểm tra idempotency trong batch processing.

**Ý chính nên trả lời:**

- Business key/idempotency key cho từng record.
- Upsert hoặc unique constraint.
- Record status: pending/processing/success/failed.
- Rerun chỉ failed/pending records nếu phù hợp.
- Checkpoint theo chunk.
- Output không duplicate.
- Reconciliation report.

**Lỗi thường gặp cần tránh:**

- Rerun toàn bộ và tạo duplicate output.
- Không có checkpoint.
- Không phân biệt retry transient vs data validation error.

---

### Q26. SQL Server và MongoDB nên dùng cho loại dữ liệu nào trong batch modernization?

**Mục đích:**

Kiểm tra khả năng chọn database theo use case.

**Ý chính nên trả lời:**

- SQL Server cho relational/transactional/reporting data cần consistency và query structured.
- MongoDB cho document-like data, flexible schema, raw payload/survey responses nếu structure thay đổi.
- Không chọn MongoDB chỉ vì “NoSQL nhanh hơn”.
- Cần index và retention policy cho cả hai.

**Lỗi thường gặp cần tránh:**

- Nói MongoDB luôn nhanh hơn SQL.
- Không nói access pattern/query requirement.

---

### Q27. Làm sao đảm bảo hệ thống mới tương thích legacy behavior?

**Mục đích:**

Kiểm tra migration/testing strategy.

**Ý chính nên trả lời:**

- Reverse engineer rule cũ.
- Document business rules với BA.
- Golden dataset/test cases.
- Parallel run old/new.
- Compare output/reconciliation.
- Feature toggle/cutover plan.
- Rollback plan.

**Lỗi thường gặp cần tránh:**

- Big bang rewrite không kiểm soát.
- Không có reconciliation.
- Không involve BA/QA.

---

## Hard

### Q28. Batch 100K records/day chạy quá lâu, bạn tối ưu như thế nào?

**Mục đích:**

Kiểm tra performance tuning có hệ thống.

**Ý chính nên trả lời:**

- Measure bottleneck: DB, CPU, IO, external call.
- Chunk size tuning.
- Parallel processing có kiểm soát.
- Bulk insert/update.
- Index query phù hợp.
- Avoid N+1 query.
- Pagination/streaming để tránh load hết memory.
- Scale pods nếu stateless và partition được workload.

**Lỗi thường gặp cần tránh:**

- Tăng thread vô tội vạ.
- Không đo bottleneck.
- Parallel làm phá ordering hoặc gây DB lock contention.

---

### Q29. Nếu batch fail ở giữa khi đã xử lý 60% records, recovery như thế nào?

**Mục đích:**

Kiểm tra fault tolerance và resumability.

**Ý chính nên trả lời:**

- Job/record status lưu persistent.
- Commit theo chunk.
- Khi restart, resume từ checkpoint hoặc query records chưa success.
- Idempotent output để không duplicate 60% đã xử lý.
- Failed records có error reason.
- Alert và report cho operation team.

**Lỗi thường gặp cần tránh:**

- Rollback toàn bộ 100K records nếu không cần.
- Không có record status.
- Không đảm bảo idempotency khi restart.

---

### Q30. Nếu migration cần zero/minimal downtime, bạn thiết kế cutover như thế nào?

**Mục đích:**

Kiểm tra modernization strategy senior-level.

**Ý chính nên trả lời:**

- Strangler pattern nếu có thể.
- Run old/new in parallel.
- Data sync/reconciliation.
- Feature flag/routing by module/customer/batch type.
- Canary cutover.
- Rollback plan.
- Freeze window nếu cần cho data consistency.

**Lỗi thường gặp cần tránh:**

- Big bang release không rollback.
- Không nói data migration/reconciliation.
- Không nói user/operation communication.

# Scalability, Performance, Reliability — Câu hỏi System Design nâng cao

File này tập trung vào các câu hỏi **Medium/Hard** mà interviewer dùng để đánh giá senior backend: scaling, performance, fault tolerance, messaging reliability, observability, deployment.

---

# 1. Scalability

## Q1 [Medium]. Notification system 1M events/day có thật sự lớn không? Bạn estimate thế nào?

**Mục đích:**

Kiểm tra bạn biết tính toán capacity thay vì chỉ nhắc con số.

**Ý chính nên trả lời:**

- 1M/day ≈ 11.6 events/sec trung bình.
- Nhưng workload thực tế có peak: có thể 10x, 50x, hoặc burst theo campaign/timezone.
- Thiết kế dựa trên peak throughput, không chỉ average.
- Cần biết payload size, provider latency, retry rate, failure rate.
- Bottleneck có thể là downstream provider hoặc DynamoDB partition, không chỉ Lambda.

**Lỗi thường gặp cần tránh:**

- Chỉ nói 1M/day là lớn mà không estimate.
- Không tính peak.
- Không hỏi latency/SLA.

---

## Q2 [Hard]. Nếu SQS queue depth tăng liên tục, bạn điều tra và xử lý thế nào?

**Mục đích:**

Kiểm tra operational thinking/backpressure.

**Ý chính nên trả lời:**

Điều tra:

- Lambda concurrency có bị limit/throttle không?
- Lambda duration tăng do provider/DB chậm không?
- Error rate tăng làm retry nhiều không?
- DynamoDB throttling không?
- Provider rate limit không?

Xử lý:

- Scale Lambda concurrency nếu bottleneck là worker.
- Optimize processing time.
- Batch processing nếu phù hợp.
- Backoff/circuit breaker nếu provider lỗi.
- Increase DynamoDB capacity hoặc fix partition key.
- Alert theo `ApproximateAgeOfOldestMessage`.

**Lỗi thường gặp cần tránh:**

- Chỉ tăng concurrency mà không biết bottleneck.
- Tăng concurrency làm provider bị overload hơn.
- Không monitor age of oldest message.

---

## Q3 [Hard]. Banking services trên OpenShift scale horizontal có vấn đề gì cần lưu ý?

**Mục đích:**

Kiểm tra multi-instance backend design.

**Ý chính nên trả lời:**

- Services nên stateless.
- Không dùng in-memory lock/cache cho consistency cross-instance.
- Session nên externalize hoặc dùng stateless token.
- DB transaction/locking xử lý concurrency.
- Kafka consumer group partition assignment.
- Readiness/liveness probes.
- Config/secrets externalized.
- Rolling deployment compatibility.

**Lỗi thường gặp cần tránh:**

- Dùng `synchronized` trong Java để bảo vệ business critical data trên nhiều pods.
- Lưu session/state local.
- Không nghĩ tới backward compatibility khi rolling deploy.

---

# 2. Performance

## Q4 [Medium]. Batch 100K records/day xử lý chậm, bạn approach thế nào?

**Mục đích:**

Kiểm tra khả năng performance tuning có phương pháp.

**Ý chính nên trả lời:**

- Measure trước: DB time, CPU, memory, external call, IO.
- Check query/index/N+1.
- Tune chunk size.
- Bulk insert/update.
- Parallelize theo partition nếu business cho phép.
- Streaming/pagination tránh load toàn bộ vào memory.
- Scale pods nếu workload partitionable.

**Lỗi thường gặp cần tránh:**

- Tăng thread ngay lập tức.
- Không đo bottleneck.
- Parallel làm tăng DB lock/deadlock.

---

## Q5 [Medium]. API banking latency cao, bạn debug theo thứ tự nào?

**Mục đích:**

Kiểm tra troubleshooting production.

**Ý chính nên trả lời:**

1. Xác định endpoint nào chậm, percentile p95/p99.
2. Check logs/tracing theo correlationId.
3. Breakdown time: app, DB, external service, Kafka publish.
4. Check DB query plan/index/lock.
5. Check thread pool/connection pool.
6. Check recent deployment/config change.
7. Mitigate trước nếu impact cao.

**Lỗi thường gặp cần tránh:**

- Chỉ nhìn average latency.
- Không phân biệt app latency và downstream latency.
- Không xem recent changes.

---

## Q6 [Hard]. Làm sao tối ưu DynamoDB cost/performance cho notification workload?

**Mục đích:**

Kiểm tra cloud database practical knowledge.

**Ý chính nên trả lời:**

- Chọn on-demand vs provisioned theo traffic predictability.
- Key design phân tán đều.
- Chỉ query theo key/index, tránh scan.
- Dùng TTL cho dữ liệu không cần giữ lâu.
- GSI chỉ tạo khi có access pattern thật.
- Payload lớn lưu S3, DynamoDB lưu reference.
- Monitor consumed capacity/throttling/hot keys.

**Lỗi thường gặp cần tránh:**

- Scan table để tìm failed events.
- Lưu payload quá lớn trong item.
- Tạo nhiều GSI không cần thiết.

---

# 3. Reliability và Fault Tolerance

## Q7 [Medium]. Retry policy cho notification provider nên thiết kế như thế nào?

**Mục đích:**

Kiểm tra xử lý lỗi transient/permanent.

**Ý chính nên trả lời:**

- Timeout rõ ràng.
- Retry exponential backoff + jitter.
- Không retry lỗi permanent như invalid recipient.
- Max retry count.
- DLQ sau khi retry fail.
- Alert và replay process.
- Idempotency để retry an toàn.

**Lỗi thường gặp cần tránh:**

- Retry vô hạn.
- Retry lỗi validation/permanent.
- Không có jitter gây thundering herd.

---

## Q8 [Hard]. Làm sao xử lý poison message trong Kafka/SQS?

**Mục đích:**

Kiểm tra robustness của consumer.

**Ý chính nên trả lời:**

- Poison message là message luôn fail do data/schema/business invalid.
- Retry giới hạn.
- Đưa vào DLQ/dead-letter topic.
- Lưu error reason và original payload/reference.
- Alert operation team.
- Tool/process replay sau khi fix.
- Schema validation trước processing.

**Lỗi thường gặp cần tránh:**

- Message fail làm block toàn bộ queue/partition mãi.
- Drop message không trace.
- Không có replay process.

---

## Q9 [Hard]. Thiết kế idempotent consumer cho Kafka banking audit event.

**Mục đích:**

Kiểm tra at-least-once processing thực tế.

**Ý chính nên trả lời:**

- Event có unique eventId.
- Consumer lưu processed_event table với unique eventId.
- Trong cùng DB transaction:
  - Check/insert processed_event.
  - Apply side effect.
- Nếu duplicate eventId, skip.
- Commit Kafka offset sau DB transaction success.
- Handle retry an toàn.

**Lỗi thường gặp cần tránh:**

- Commit offset trước DB write.
- Check eventId rồi apply side effect không atomic.
- Chỉ dùng memory set để dedupe.

---

## Q10 [Hard]. Outbox pattern giải quyết vấn đề gì trong banking platform?

**Mục đích:**

Kiểm tra dual-write consistency.

**Ý chính nên trả lời:**

- Vấn đề: DB transaction thành công nhưng publish Kafka fail, hoặc ngược lại.
- Outbox: ghi business data và event vào outbox table trong cùng DB transaction.
- Publisher đọc outbox, publish Kafka, mark as published.
- Retry publisher nếu fail.
- Consumer idempotent vì publisher có thể publish duplicate.
- Monitor outbox lag.

**Lỗi thường gặp cần tránh:**

- Không nhận ra DB + Kafka không atomic.
- Cho rằng Kafka transaction tự giải quyết mọi DB side effect.

---

# 4. Caching và Backpressure

## Q11 [Medium]. Khi nào nên dùng Redis trong các hệ thống của bạn?

**Mục đích:**

Kiểm tra áp dụng cache đúng chỗ.

**Ý chính nên trả lời:**

- Cache reference data ít thay đổi.
- Rate limiting counters.
- Distributed locks nếu thật sự cần và hiểu rủi ro.
- Session/token metadata nếu architecture yêu cầu.
- Không dùng Redis thay thế database transaction.
- TTL/invalidation rõ ràng.

**Lỗi thường gặp cần tránh:**

- Cache dữ liệu financial critical mà không consistency strategy.
- Không có TTL.
- Dùng distributed lock tùy tiện.

---

## Q12 [Hard]. Backpressure trong event-driven notification system xử lý thế nào?

**Mục đích:**

Kiểm tra hệ thống chịu tải khi downstream chậm.

**Ý chính nên trả lời:**

- Queue là buffer tự nhiên.
- Giới hạn Lambda concurrency để không overload provider.
- Retry backoff.
- Circuit breaker/pause consumer khi provider down.
- Prioritize queues nếu có notification critical/non-critical.
- Monitor queue depth/age.
- DLQ và replay.
- Business policy cho expired notifications.

**Lỗi thường gặp cần tránh:**

- Scale consumer vô hạn.
- Không có policy cho backlog quá hạn.
- Không chia priority nếu business cần.

---

# 5. Deployment, Monitoring, Incident Response

## Q13 [Medium]. CI/CD pipeline cho Spring Boot service trên OpenShift nên gồm gì?

**Mục đích:**

Kiểm tra production delivery.

**Ý chính nên trả lời:**

- Build.
- Unit/integration tests.
- Static analysis/SonarQube.
- Security scan nếu có.
- Build container image.
- Push registry.
- Deploy to dev/staging.
- Smoke test.
- Approval/prod deploy.
- Rolling update/rollback.

**Lỗi thường gặp cần tránh:**

- Không nói test/quality gate.
- Không nói rollback.
- Không nói environment config/secrets.

---

## Q14 [Hard]. Monitoring dashboard cho notification system nên có metric nào?

**Mục đích:**

Kiểm tra observability thiết kế theo SLO.

**Ý chính nên trả lời:**

Technical metrics:

- Event ingestion count.
- SQS queue depth.
- Age of oldest message.
- Lambda invocation/error/duration/throttle/concurrency.
- DLQ count.
- DynamoDB read/write throttle.
- Provider latency/error rate.

Business metrics:

- Notification sent/failed count.
- Failure by channel/type.
- Retry count.
- Time to deliver.

Alert:

- DLQ > threshold.
- Queue age > SLA.
- Error rate spike.
- Provider failure spike.

**Lỗi thường gặp cần tránh:**

- Chỉ monitor CPU/memory cho serverless.
- Không có business metric.
- Không alert theo symptom user/business impact.

---

## Q15 [Hard]. Sau production incident, bạn làm postmortem như thế nào?

**Mục đích:**

Kiểm tra senior ownership và continuous improvement.

**Ý chính nên trả lời:**

- Timeline sự kiện.
- Impact assessment.
- Root cause và contributing factors.
- What went well / what went wrong.
- Action items:
  - code fix,
  - test coverage,
  - monitoring/alert,
  - runbook,
  - process improvement.
- Blameless culture.

**Lỗi thường gặp cần tránh:**

- Chỉ tìm người mắc lỗi.
- Không có action item cụ thể.
- Không cập nhật monitoring/test để phòng ngừa.

---

# 6. Câu trả lời mẫu nhanh

## Q1. Notification system 1M events/day có thật sự lớn không?

1M events/day tương đương khoảng 11.6 events/second trung bình, nhưng không thể design theo average. Em sẽ hỏi peak traffic, payload size, provider latency, retry rate và SLA. Kiến trúc nên dùng queue như SQS để buffer burst, Lambda scale theo backlog, DynamoDB key phân tán đều, và monitor queue age/provider error để phát hiện bottleneck.

## Q2. Nếu SQS queue depth tăng liên tục?

Em sẽ kiểm tra Lambda concurrency/throttle, Lambda duration, error/retry rate, DynamoDB throttling và provider rate limit. Không tăng concurrency ngay vì có thể làm downstream quá tải hơn. Sau khi xác định bottleneck, em scale worker, tối ưu processing, giới hạn concurrency, dùng backoff/circuit breaker và alert theo age of oldest message.

## Q3. Banking services trên OpenShift scale horizontal cần lưu ý gì?

Service nên stateless, không dùng in-memory lock cho business critical data, vì nhiều pod chạy song song. Concurrency phải xử lý bằng DB transaction/locking hoặc distributed mechanism. Cần readiness/liveness probes, config/secrets externalized, rolling deployment compatible và logging/tracing theo correlationId.

## Q4. Batch 100K records/day xử lý chậm?

Em đo bottleneck trước: MongoDB query/index, CPU, memory, IO hoặc external call. Sau đó tối ưu chunk size, dùng bulk operations, index đúng, streaming/pagination để tránh load toàn bộ data, và parallelize có kiểm soát theo partition nếu business cho phép.

## Q5. API banking latency cao debug thế nào?

Em nhìn p95/p99 theo endpoint, trace correlationId, tách latency app/DB/external/Kafka publish, kiểm tra query plan, lock, connection pool, thread pool và recent deployment. Nếu impact cao thì mitigate trước, ví dụ rollback, scale hoặc tạm disable feature.

## Q6. Tối ưu DynamoDB cost/performance?

Thiết kế key theo access pattern, tránh scan, chỉ tạo GSI khi cần, dùng TTL cho dữ liệu tạm, payload lớn lưu S3, chọn on-demand/provisioned theo traffic predictability và monitor consumed capacity/throttling/hot keys.

## Q7. Retry policy cho notification provider?

Set timeout rõ, retry exponential backoff + jitter cho transient errors, không retry lỗi permanent như invalid recipient, giới hạn retry count, đưa message vào DLQ sau max retry và đảm bảo idempotency để retry không gửi trùng.

## Q8. Xử lý poison message trong Kafka/SQS?

Poison message không nên block queue/partition mãi. Em validate schema, retry giới hạn, đưa vào DLQ/dead-letter topic với error reason và payload/reference, alert operation team và có tool/process replay sau khi fix.

## Q9. Idempotent consumer cho Kafka banking audit event?

Event cần unique eventId. Consumer lưu `processed_event` với unique eventId trong cùng transaction với side effect. Nếu duplicate eventId thì skip. Kafka offset chỉ commit sau khi DB transaction thành công.

## Q10. Outbox pattern giải quyết gì?

Outbox giải quyết dual-write problem giữa DB và Kafka. Business data và outbox event được ghi trong cùng DB transaction; publisher đọc outbox để publish Kafka và mark published. Nếu publish fail thì retry; consumer phải idempotent vì event có thể publish trùng.

## Q11. Khi nào dùng Redis?

Dùng Redis cho reference data cache, rate limiting counter, session/token metadata hoặc distributed coordination nếu thật sự cần. Không dùng Redis thay thế DB transaction, không cache dữ liệu tài chính critical nếu không có TTL/invalidation/consistency strategy.

## Q12. Backpressure trong notification system?

Queue là buffer nhưng không đủ. Cần giới hạn Lambda concurrency để không overload provider, retry backoff, circuit breaker/pause consumer khi provider down, priority queue nếu có critical notification, monitor queue age/DLQ và có policy cho expired backlog.

## Q13. CI/CD pipeline Spring Boot trên OpenShift?

Pipeline gồm build, unit/integration test, SonarQube/security scan, build image, push registry, deploy dev/staging, smoke test, approval nếu prod, rolling update và rollback plan. Config/secrets phải tách theo environment.

## Q14. Monitoring dashboard notification system?

Theo dõi event count, SQS queue depth/oldest age, Lambda error/duration/throttle/concurrency, DLQ count, DynamoDB throttling, provider latency/error, sent/failed/retry count. Alert theo symptom ảnh hưởng business như queue age vượt SLA hoặc DLQ tăng.

## Q15. Postmortem sau incident?

Làm blameless postmortem gồm timeline, impact, root cause, contributing factors, what went well/wrong và action items cụ thể: code fix, test, monitoring/alert, runbook, process improvement. Mục tiêu là ngăn lặp lại, không tìm người đổ lỗi.

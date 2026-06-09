# API, Database, Transaction Design — Câu hỏi System Design theo CV

File này gom các câu hỏi thường bị hỏi khi interviewer đi sâu vào **API design**, **database design**, **transaction**, **authentication/authorization**, và **data consistency** dựa trên các project của bạn.

---

# 1. API Design

## Q1 [Easy]. Nếu thiết kế REST API cho notification system, bạn cần những endpoint nào?

**Mục đích:**

Kiểm tra khả năng bắt đầu từ functional requirements và translate thành API.

**Ý chính nên trả lời:**

- `POST /api/v1/notifications` để nhận request/event notification nếu có public/internal API.
- `GET /api/v1/notifications/{id}` để query status.
- `GET /api/v1/users/{userId}/notifications` nếu cần list theo user.
- `POST /api/v1/notifications/{id}/retry` cho operation/admin retry.
- Request có idempotency key hoặc eventId.
- Response trả notificationId/status.

**Lỗi thường gặp cần tránh:**

- Thiết kế endpoint theo action quá nhiều như `/sendEmail`, `/sendSms` nếu có thể model theo resource.
- Không có endpoint/status để troubleshoot.
- Không nghĩ tới idempotency cho create/send operation.

---

## Q2 [Medium]. Thiết kế API tạo cash transaction cho banking teller platform.

**Mục đích:**

Kiểm tra REST design trong domain cần consistency/audit.

**Ý chính nên trả lời:**

Ví dụ endpoint:

```http
POST /api/v1/teller/transactions
Idempotency-Key: <uuid>
Authorization: Bearer <token>
```

Request body:

```json
{
  "branchId": "B001",
  "tellerId": "T123",
  "accountId": "A456",
  "type": "WITHDRAWAL",
  "amount": 1000000,
  "currency": "VND",
  "description": "Cash withdrawal"
}
```

Response:

```json
{
  "transactionId": "TX789",
  "status": "COMPLETED",
  "createdAt": "2026-06-06T10:00:00Z"
}
```

Cần nói thêm:

- Validate amount/currency/account/permission.
- Idempotency key chống double submit.
- Transaction DB boundary.
- Audit event.
- Error code rõ ràng.

**Lỗi thường gặp cần tránh:**

- Không đề cập authentication/authorization.
- Không có idempotency.
- Không có audit/correlationId.

---

## Q3 [Medium]. Bạn thiết kế error response chuẩn cho các backend services như thế nào?

**Mục đích:**

Kiểm tra production API quality.

**Ý chính nên trả lời:**

Error response nên có:

```json
{
  "code": "INSUFFICIENT_BALANCE",
  "message": "Account balance is not enough for this transaction",
  "traceId": "abc-123",
  "details": []
}
```

- HTTP status đúng: 400, 401, 403, 404, 409, 422, 500.
- Business error code ổn định.
- Không expose stack trace.
- Có traceId/correlationId.
- Dùng `@RestControllerAdvice` trong Spring Boot.

**Lỗi thường gặp cần tránh:**

- Trả 200 cho mọi lỗi.
- Expose exception stack trace ra client.
- Message không nhất quán giữa services.

---

## Q4 [Hard]. Làm sao thiết kế API idempotent cho thao tác tạo transaction hoặc gửi notification?

**Mục đích:**

Kiểm tra xử lý retry/double-submit trong hệ thống thực tế.

**Ý chính nên trả lời:**

- Client gửi `Idempotency-Key` hoặc event có `eventId`.
- Server lưu key + request hash + result/status.
- Nếu request cùng key và cùng payload lặp lại, trả result cũ.
- Nếu cùng key nhưng payload khác, trả 409 Conflict.
- Storage có TTL nếu key không cần giữ mãi.
- Operation thực thi trong transaction/conditional write để tránh race.

**Lỗi thường gặp cần tránh:**

- Chỉ check cache memory local — không đúng khi scale nhiều instance.
- Không so sánh request hash.
- Check-then-insert không atomic.

---

# 2. Database Design

## Q5 [Easy]. Notification system cần lưu những dữ liệu gì?

**Mục đích:**

Kiểm tra bạn biết state và metadata cần cho operability.

**Ý chính nên trả lời:**

- eventId/notificationId.
- recipient/userId.
- channel/type.
- payload metadata hoặc S3 reference.
- status: received/processing/sent/failed.
- retryCount.
- errorCode/errorMessage ngắn.
- createdAt/updatedAt/sentAt.
- correlationId.

**Lỗi thường gặp cần tránh:**

- Chỉ lưu payload, không lưu status.
- Không lưu retry/error info.
- Không có timestamp/correlationId.

---

## Q6 [Medium]. Thiết kế relational schema cho banking transaction.

**Mục đích:**

Kiểm tra data modeling trong banking domain.

**Ý chính nên trả lời:**

Các bảng có thể có:

- `account`: accountId, customerId, status, balance/version nếu service quản lý balance.
- `branch`: branchId, name.
- `teller`: tellerId, branchId, role/status.
- `transaction`: transactionId, accountId, branchId, tellerId, type, amount, currency, status, createdAt.
- `transaction_audit`: auditId, transactionId, actorId, action, timestamp, correlationId, metadata.
- `idempotency_key`: key, requestHash, resultRef, status, expiry.

Index:

- transactionId unique.
- accountId + createdAt.
- branchId + createdAt.
- idempotency key unique.

**Lỗi thường gặp cần tránh:**

- Không lưu transaction history.
- Update balance mà không có transaction log/audit.
- Không có unique constraints cho business key/idempotency.

---

## Q7 [Medium]. Với batch system, database cần hỗ trợ job execution và record-level retry như thế nào?

**Mục đích:**

Kiểm tra batch operability và recovery.

**Ý chính nên trả lời:**

Bảng gợi ý:

- `batch_job_execution`: jobId, jobName, status, startedAt, endedAt, totalCount, successCount, failedCount.
- `batch_record_execution`: jobId, recordId, status, retryCount, errorCode, errorMessage, updatedAt.
- `batch_checkpoint`: jobId, chunkIndex, lastProcessedKey.

Nên có:

- Index theo jobId/status.
- Error classification.
- Retention policy.
- Không lưu payload quá lớn nếu có thể dùng object storage/document DB.

**Lỗi thường gặp cần tránh:**

- Chỉ có log file, không có persistent status.
- Không biết record nào fail để retry.
- Không có checkpoint.

---

## Q8 [Hard]. DynamoDB data modeling cho notification system có những access pattern nào và key design ra sao?

**Mục đích:**

Kiểm tra hiểu NoSQL thực tế.

**Ý chính nên trả lời:**

Access patterns:

1. Check event processed chưa theo eventId.
2. Get notification status theo notificationId/eventId.
3. Query notification theo user/time.
4. Query failed notifications for replay/admin.

Thiết kế có thể:

- Main table cho idempotency/status:
  - `PK = EVENT#<eventId>`
  - `SK = STATUS`
- Nếu query user/time quan trọng:
  - `PK = USER#<userId>`
  - `SK = NOTIFICATION#<timestamp>#<eventId>`
- GSI cho status/time:
  - `GSI1PK = STATUS#FAILED#<bucket>`
  - `GSI1SK = createdAt#eventId`
- TTL cho event/idempotency nếu retention cho phép.
- Conditional write cho idempotency.

**Lỗi thường gặp cần tránh:**

- Nói “DynamoDB tự scale nên key không quan trọng”.
- Dùng status làm partition key không shard gây hot partition.
- Không xác định access pattern trước.

---

# 3. Transaction và Consistency

## Q9 [Easy]. ACID quan trọng thế nào trong banking teller platform?

**Mục đích:**

Kiểm tra transaction fundamentals.

**Ý chính nên trả lời:**

- Atomicity: debit/credit hoặc transaction update phải all-or-nothing.
- Consistency: account/transaction state hợp lệ.
- Isolation: concurrent operations không làm sai balance/status.
- Durability: transaction commit rồi phải bền vững.
- Banking cần audit và traceability.

**Lỗi thường gặp cần tránh:**

- Chỉ đọc định nghĩa ACID mà không liên hệ banking.

---

## Q10 [Medium]. Transaction boundary trong Spring Boot service nên đặt ở đâu?

**Mục đích:**

Kiểm tra Spring transaction design.

**Ý chính nên trả lời:**

- Thường đặt ở application/service layer, bao quanh business use case.
- Controller không nên chứa transaction logic.
- Repository chỉ thao tác persistence.
- Chú ý rollback mặc định với unchecked exception.
- Tránh self-invocation làm `@Transactional` không active.

**Lỗi thường gặp cần tránh:**

- Đặt transaction ở mọi repository method mà không hiểu use case boundary.
- Catch exception rồi không throw lại làm commit sai.

---

## Q11 [Hard]. Trong microservices, làm sao đảm bảo consistency khi một business operation ảnh hưởng nhiều service?

**Mục đích:**

Kiểm tra distributed transaction, saga, outbox.

**Ý chính nên trả lời:**

- Tránh distributed transaction/2PC nếu không cần.
- Dùng saga orchestration/choreography.
- Mỗi service commit local transaction.
- Publish event qua outbox pattern.
- Có compensation action khi step sau fail.
- Idempotent consumer.
- Observability và reconciliation.

**Lỗi thường gặp cần tránh:**

- Nói dùng `@Transactional` là đủ cho nhiều service.
- Không nói compensation/retry/idempotency.

---

# 4. Authentication, Authorization, Security

## Q12 [Medium]. Banking teller API cần authentication/authorization như thế nào?

**Mục đích:**

Kiểm tra security mindset trong domain nhạy cảm.

**Ý chính nên trả lời:**

- Authentication qua enterprise SSO/OAuth2/JWT/session tùy hệ thống.
- Authorization theo role/permission: teller, supervisor, admin.
- Branch-level access: teller chỉ thao tác branch được phép.
- Audit mọi action quan trọng.
- Không log sensitive data.
- TLS, secrets management.

**Lỗi thường gặp cần tránh:**

- Chỉ nói “dùng JWT” mà không nói permission/domain rule.
- Không nói audit.

---

## Q13 [Hard]. Làm sao xử lý sensitive data trong logs/events?

**Mục đích:**

Kiểm tra production security/compliance.

**Ý chính nên trả lời:**

- Mask account/card/customer sensitive fields.
- Không đưa PII không cần thiết vào Kafka event/log.
- Dùng correlationId thay vì full payload.
- Access control cho Kibana/log system.
- Retention policy.
- Security scanning bằng Veracode/SonarQube.

**Lỗi thường gặp cần tránh:**

- Log full request/response trong banking.
- Gửi sensitive data sang event không cần thiết.

---

# 5. Caching

## Q14 [Medium]. Trong các project của bạn, caching có thể dùng ở đâu?

**Mục đích:**

Kiểm tra bạn biết áp dụng cache có chọn lọc.

**Ý chính nên trả lời:**

- Reference data ít thay đổi: branch config, notification template, user preferences nếu phù hợp.
- API read-heavy.
- Không cache dữ liệu transaction/balance nhạy cảm nếu consistency requirement cao.
- Redis cho distributed cache, local cache cho config nhỏ.
- TTL/invalidation rõ ràng.

**Lỗi thường gặp cần tránh:**

- Cache mọi thứ.
- Không nói invalidation.
- Cache balance/transaction state mà không đảm bảo consistency.

---

## Q15 [Hard]. Cache invalidation cho notification template hoặc branch configuration xử lý thế nào?

**Mục đích:**

Kiểm tra cache correctness.

**Ý chính nên trả lời:**

- TTL ngắn nếu consistency không quá strict.
- Event-based invalidation khi config/template thay đổi.
- Versioned config/template.
- Fallback to DB/config store nếu cache miss.
- Monitor cache hit rate/error.

**Lỗi thường gặp cần tránh:**

- Không có strategy invalidation.
- Cache stale data không kiểm soát.

---

# 6. Câu trả lời mẫu nhanh

## Q1. REST API cho notification system cần endpoint nào?

Em sẽ thiết kế theo resource: `POST /api/v1/notifications` để tạo/yêu cầu gửi notification, `GET /api/v1/notifications/{id}` để xem trạng thái, `GET /api/v1/users/{userId}/notifications` nếu cần list theo user, và endpoint retry dành cho operation/admin. Request cần `eventId` hoặc `Idempotency-Key` để retry an toàn.

## Q2. API tạo cash transaction cho teller platform?

Dùng `POST /api/v1/teller/transactions` với `Idempotency-Key`, auth token, branchId, tellerId, accountId, type, amount, currency. Backend validate permission/account/amount, xử lý trong transaction boundary, ghi audit và trả transactionId/status. Error code cần rõ ràng như duplicate request, invalid account, permission denied.

## Q3. Error response chuẩn?

Dùng format thống nhất gồm `code`, `message`, `traceId`, optional `details`. HTTP status phải đúng, ví dụ 400 validation, 401 unauthenticated, 403 unauthorized, 404 not found, 409 conflict. Không expose stack trace ra client.

## Q4. API idempotent cho transaction/notification?

Client gửi idempotency key. Server lưu key, request hash, status/result trong DB/cache persistent. Nếu cùng key/cùng payload gọi lại thì trả result cũ; cùng key khác payload trả 409. Việc insert/check key phải atomic để tránh race condition.

## Q5. Notification system cần lưu dữ liệu gì?

Cần lưu eventId/notificationId, user/recipient, channel/type, payload metadata hoặc S3 reference, status, retry count, error code, timestamps và correlationId. Không chỉ lưu payload vì operation cần tracking/retry/troubleshooting.

## Q6. Relational schema cho banking transaction?

Có bảng transaction, transaction_audit, idempotency_key, account/branch/teller nếu service quản lý. Transaction lưu amount/currency/type/status/timestamps. Audit append-only lưu actor/action/correlationId. Index theo transactionId, accountId+time, branchId+time và idempotency key unique.

## Q7. Database hỗ trợ batch job execution/retry?

Cần job execution collection/table lưu jobId, jobName, status, start/end, count; record execution lưu recordId, status, retryCount, errorCode; checkpoint lưu last processed key/chunk. Nhờ vậy biết job nào fail, record nào cần retry và resume được.

## Q8. DynamoDB data modeling notification?

Bắt đầu từ access pattern: check eventId, get status, query user/time, query failed events. Dùng key phân tán như `EVENT#eventId` hoặc `USER#userId` + sort key timestamp. GSI cho status/time cần bucket/shard để tránh hot partition. Conditional write dùng cho idempotency.

## Q9. ACID quan trọng thế nào trong banking teller platform?

Banking cần all-or-nothing cho transaction, state hợp lệ, isolation khi concurrent operations và durability sau commit. Ngoài dữ liệu chính còn cần audit/traceability để chứng minh ai làm gì và khi nào.

## Q10. Transaction boundary trong Spring Boot đặt ở đâu?

Đặt ở service/application layer bao quanh một use case như create transaction hoặc process payment. Controller chỉ nhận request, repository chỉ persistence. Cần hiểu rollback rule và tránh catch exception làm transaction commit sai.

## Q11. Microservices consistency khi operation ảnh hưởng nhiều service?

Không dùng `@Transactional` cho nhiều service. Dùng local transaction, outbox để publish event reliable, saga để phối hợp steps, compensation khi fail, idempotent consumer và reconciliation/monitoring.

## Q12. Banking teller API auth/authz?

Authentication qua SSO/OAuth2/JWT/session tùy hệ thống. Authorization theo role và branch: teller/supervisor/admin, teller chỉ thao tác branch được phép. Giao dịch quan trọng cần audit, TLS, secrets management và không log sensitive data.

## Q13. Sensitive data trong logs/events?

Mask account/card/customer data, không log full request/response, event chỉ chứa field cần thiết, dùng correlationId/transactionId để trace. Kibana/log access cần kiểm soát và có retention policy.

## Q14. Caching dùng ở đâu?

Cache reference data ít thay đổi như branch config, notification template, product config. Không cache balance/transaction critical nếu không có consistency strategy. Dùng TTL/invalidation, Redis cho distributed cache hoặc local cache cho config nhỏ.

## Q15. Cache invalidation cho template/config?

Dùng TTL nếu stale ngắn chấp nhận được, event-based invalidation khi config đổi, versioned config/template để tránh dùng nhầm bản cũ, fallback DB/config store khi cache miss và monitor cache hit/error.

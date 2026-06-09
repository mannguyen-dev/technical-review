# Database — Tổng hợp câu hỏi phỏng vấn kèm câu trả lời

## SQL

### Easy

#### 1. INNER JOIN vs LEFT JOIN?

**Trả lời:** INNER JOIN chỉ trả records match ở cả hai bảng. LEFT JOIN trả tất cả records bên trái, bên phải không match thì null.

#### 2. WHERE vs HAVING?

**Trả lời:** WHERE lọc rows trước khi group. HAVING lọc groups sau `GROUP BY`, thường dùng với aggregate như `COUNT`, `SUM`.

#### 3. Primary key vs unique key?

**Trả lời:** Primary key định danh chính của row, không null và mỗi bảng thường có một primary key. Unique key đảm bảo giá trị unique nhưng có thể có nhiều unique constraints; null handling tùy DB.

### Medium

#### 1. Index là gì và khi nào dùng?

**Trả lời:** Index là cấu trúc dữ liệu giúp DB tìm kiếm/sort/join nhanh hơn. Dùng cho column thường xuất hiện trong WHERE, JOIN, ORDER BY, có selectivity tốt. Trade-off: tốn storage và làm write chậm hơn.

#### 2. Composite index hoạt động thế nào?

**Trả lời:** Composite index gồm nhiều column và hiệu quả theo left-most prefix. Index `(branch_id, created_at)` tốt cho query filter `branch_id` và range/sort `created_at`.

#### 3. Query chậm thì điều tra gì?

**Trả lời:** Xem execution plan, index, row count, join strategy, N+1 từ ORM, lock, missing pagination, function trên indexed column, network/external latency nếu có.

### Hard

#### 1. Thiết kế bảng transaction/audit cho banking.

**Trả lời:** Cần bảng `transactions` lưu transactionId, accountId, branchId, tellerId, type, amount, currency, status, timestamps. Bảng `audit_log` append-only lưu actor, action, timestamp, correlationId, metadata. Thêm idempotency table để chống double submit và index theo account/time, branch/time.

#### 2. Function trên indexed column có thể ảnh hưởng gì?

**Trả lời:** Nếu query dùng function trên column như `DATE(created_at) = ...`, DB có thể không dùng index hiệu quả vì giá trị index không khớp expression. Nên dùng range query hoặc function-based index nếu DB hỗ trợ.

#### 3. Làm sao tối ưu query report lớn?

**Trả lời:** Xác định access pattern, index đúng, pre-aggregate/materialized view nếu cần, pagination/streaming, tránh select *, partition theo date, batch offline report, kiểm tra execution plan.

## Transaction

### Easy

#### 1. ACID là gì?

**Trả lời:** Atomicity all-or-nothing; Consistency giữ dữ liệu hợp lệ; Isolation kiểm soát transaction concurrent; Durability commit rồi thì bền vững.

#### 2. Commit vs rollback?

**Trả lời:** Commit ghi nhận thay đổi vĩnh viễn. Rollback hủy thay đổi trong transaction.

#### 3. Transaction dùng để làm gì?

**Trả lời:** Đảm bảo một nhóm thao tác DB được xử lý nhất quán, hoặc tất cả thành công hoặc tất cả rollback.

### Medium

#### 1. Isolation levels khác nhau thế nào?

**Trả lời:** Read Uncommitted có thể dirty read. Read Committed tránh dirty read. Repeatable Read tránh non-repeatable read. Serializable mạnh nhất, giống tuần tự nhưng giảm concurrency.

#### 2. Dirty read/non-repeatable read/phantom read là gì?

**Trả lời:** Dirty read là đọc dữ liệu chưa commit. Non-repeatable read là đọc cùng row hai lần thấy khác do transaction khác commit. Phantom read là query cùng điều kiện thấy thêm/mất rows.

#### 3. Optimistic vs pessimistic locking?

**Trả lời:** Optimistic dùng version, không lock khi đọc, update fail nếu version đổi; phù hợp conflict thấp. Pessimistic lock record khi đọc/update; phù hợp conflict cao nhưng giảm concurrency.

### Hard

#### 1. Transfer money giữa hai accounts xử lý thế nào?

**Trả lời:** Trong cùng DB, dùng transaction bao quanh debit và credit, validate balance/status, lock hoặc version account rows, ghi transaction/audit, commit all-or-nothing. Trong microservices, dùng saga/outbox/idempotency thay vì chỉ `@Transactional`.

#### 2. Microservices consistency không dùng distributed transaction thế nào?

**Trả lời:** Dùng local transaction per service, publish event qua outbox, saga orchestration/choreography, compensation action, idempotent consumers và reconciliation.

#### 3. Outbox pattern là gì?

**Trả lời:** Ghi business data và event vào outbox table trong cùng DB transaction. Publisher đọc outbox, publish message, mark published. Giải quyết dual-write problem giữa DB và Kafka/SQS.

## NoSQL

### Easy

#### 1. MongoDB khác SQL thế nào?

**Trả lời:** MongoDB lưu document JSON-like với schema linh hoạt; SQL lưu dữ liệu relational có schema rõ và join/transaction mạnh. Chọn theo data shape và access pattern.

#### 2. DynamoDB partition key là gì?

**Trả lời:** Partition key là key chính dùng để phân phối item vào partitions và lookup/query. Key design ảnh hưởng trực tiếp throughput và hot partition.

#### 3. GSI là gì?

**Trả lời:** Global Secondary Index là index phụ trong DynamoDB với partition/sort key riêng, phục vụ access pattern khác main table.

### Medium

#### 1. Khi nào chọn NoSQL?

**Trả lời:** Khi data dạng document/key-value, schema linh hoạt, access pattern rõ, cần scale managed/high throughput, hoặc không cần join phức tạp. Ví dụ surveyor data trong Zurich phù hợp MongoDB vì form/document linh hoạt.

#### 2. DynamoDB thiết kế theo access pattern nghĩa là gì?

**Trả lời:** Trước khi thiết kế table/key, liệt kê query cần hỗ trợ: get by id, query by user/time, query failed status. Key/GSI được chọn để phục vụ các access patterns đó, không normalize như SQL.

#### 3. TTL dùng để làm gì?

**Trả lời:** TTL tự động expire/delete item sau thời gian nhất định, phù hợp session, idempotency record tạm, cache/status temporary data.

### Hard

#### 1. Thiết kế DynamoDB cho 1M notification events/day.

**Trả lời:** Xác định access patterns: check eventId, get status, query user/time, failed events. Dùng partition key high-cardinality như `EVENT#eventId` hoặc `USER#userId`, sort key theo timestamp nếu query timeline. GSI cho status/time có bucket/shard để tránh hot partition. Dùng conditional write cho idempotency và TTL nếu retention cho phép.

#### 2. Làm sao tránh hot partition?

**Trả lời:** Chọn partition key phân tán đều, tránh key có ít giá trị như `STATUS#PENDING`. Dùng sharding/bucketing theo time/random suffix nếu query theo status, monitor throttling/hot keys.

#### 3. Conditional write dùng cho idempotency thế nào?

**Trả lời:** Khi process event, insert item với điều kiện `attribute_not_exists(eventId)`. Nếu insert thành công thì process; nếu fail nghĩa là event đã xử lý/đang xử lý, skip hoặc trả result cũ. Điều này atomic, tránh race condition.

## MongoDB cho Zurich Surveyor Batch Jobs

### Easy

#### 1. Vì sao MongoDB phù hợp cho dữ liệu Surveyor?

**Trả lời:** Dữ liệu Surveyor thường dạng form/document, field thay đổi theo loại survey/inspection. MongoDB lưu document linh hoạt, giữ raw payload, normalized fields và processing metadata trong cùng document hoặc collection liên quan.

#### 2. Batch job ban ngày và ban đêm khác nhau thế nào?

**Trả lời:** Day jobs thường ingest/validate/normalize dữ liệu thu thập trong ngày. Night jobs thường tổng hợp, reconciliation, generate report hoặc đồng bộ downstream khi traffic thấp.

### Medium

#### 1. Thiết kế MongoDB collections cho batch job status.

**Trả lời:** Có `surveyor_records`, `batch_job_executions`, `batch_record_executions`, `batch_checkpoints`. Index theo businessDate/status, jobName/businessDate, jobExecutionId/status, surveyorId/businessDate.

#### 2. Làm sao đảm bảo job chạy đúng thứ tự?

**Trả lời:** Dùng scheduler/orchestrator quản lý sequence/dependency, lưu job status persistent, job sau chỉ chạy khi dependencies success. Dùng lock/unique constraint theo jobName + businessDate để tránh nhiều pod trigger duplicate.

### Hard

#### 1. Nếu day job fail mà night job phụ thuộc thì xử lý thế nào?

**Trả lời:** Mark day job failed/partial, block hoặc skip night job theo dependency rule, alert operation team. Sau khi fix và rerun day job thành công, trigger lại night job hoặc resume sequence.

#### 2. Batch rerun không duplicate output bằng cách nào?

**Trả lời:** Dùng business key/unique constraint/upsert, record-level status, checkpoint, skip records đã success hoặc reprocess idempotently, lưu job execution theo business date.

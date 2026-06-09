# Project-Based Preparation — Answer Outlines bằng tiếng Việt

## Nguyên tắc trả lời project

Nên dùng công thức:

```text
Context → My Role → Technical Approach → Challenge → Result → Lesson Learned
```

Không nên chỉ liệt kê công nghệ. Hãy nói rõ tại sao dùng công nghệ đó và impact là gì.

---

# 1. User Notification System Modernization

## Câu hỏi: “Bạn hãy mô tả project notification system gần đây nhất.”

### Outline trả lời

> Project này là hệ thống notification platform cho một khách hàng Cox Automotive. Mục tiêu là hiện đại hóa flow xử lý notification/event để tăng reliability, scalability và giảm thao tác manual.
>
> Về kiến trúc, hệ thống đi theo hướng cloud-native và event-driven trên AWS. Event được route qua EventBridge, queue qua SQS để decouple producer/consumer, xử lý bằng Lambda, và lưu metadata/status bằng DynamoDB. Một số artifact hoặc payload lớn có thể lưu ở S3. CI/CD được tự động hóa bằng AWS CDK và GitHub Actions.
>
> Vai trò của em là design và implement các service/serverless components, xử lý integration giữa Lambda/SQS/EventBridge/DynamoDB, setup deployment automation, fix security/code quality issues và thiết lập monitoring/alerting bằng CloudWatch và Slack.
>
> Challenge chính là xử lý scale hơn 1 triệu events mỗi ngày, đảm bảo retry an toàn, tránh duplicate processing và có observability tốt khi incident xảy ra. Em chú ý thiết kế idempotency, DLQ, structured logging và alerting theo error rate/queue depth.
>
> Kết quả là hệ thống giảm manual processing khoảng 70%, deployment time xuống dưới 30 phút và incident detection time giảm khoảng 50%.

## Câu hỏi: “Vì sao dùng SQS/EventBridge thay vì gọi synchronous API?”

### Outline trả lời

- Notification không cần luôn xử lý blocking theo request chính.
- Async giúp decouple producer và consumer.
- SQS buffer traffic spike, hỗ trợ retry/DLQ.
- EventBridge phù hợp routing event theo rule giữa nhiều source/target.
- Trade-off: eventual consistency, tracing/debug khó hơn, cần idempotency.

Câu trả lời mẫu:

> Em chọn async/event-driven vì notification workload thường có traffic spike và không nên block flow chính. SQS giúp buffer và retry, EventBridge giúp routing event linh hoạt. Đổi lại, hệ thống phải xử lý eventual consistency, duplicate events và observability tốt hơn. Vì vậy em thiết kế idempotency key, DLQ và CloudWatch alert để kiểm soát reliability.

## Câu hỏi: “Bạn đảm bảo không gửi notification trùng như thế nào?”

### Outline trả lời

- Mỗi event có eventId/idempotency key.
- Lưu processing status vào DynamoDB.
- Conditional write: chỉ process nếu eventId chưa tồn tại hoặc status hợp lệ.
- Retry phải an toàn.
- External provider call nên có idempotency nếu provider hỗ trợ.

---

# 2. Banking Teller Platform Development

## Câu hỏi: “Bạn mô tả banking teller platform.”

### Outline trả lời

> Đây là platform hỗ trợ nghiệp vụ teller cho OCBC Bank, phục vụ daily operations ở nhiều branch. Điểm đặc biệt của hệ thống là mô hình dual-screen tại quầy: bank teller và customer tương tác trực tiếp qua hai màn hình được đồng bộ real-time bằng WebSocket. Teller thao tác nghiệp vụ trên màn hình của ngân hàng, còn customer có thể xem/xác nhận thông tin giao dịch trên màn hình khách hàng.
>
> Em tham gia phát triển 5 module nghiệp vụ gồm Secure Inventory, Payment Insurance, Cheque Service, Account Closure và Remittance. Backend được xây dựng bằng Java Spring Boot theo hướng DDD/microservices, xử lý nghiệp vụ giao dịch, trạng thái phiên làm việc teller-customer, audit events và integration với các hệ thống khác.
>
> Kafka được dùng cho real-time messaging giữa các service, đặc biệt với cash flow, ATM và audit event. Services được deploy trên OpenShift, CI/CD bằng Jenkins và production log/monitoring qua Kibana.
>
> Vai trò của em là phát triển 5 core backend services, tham gia architecture discussion, code review, support release và incident handling. Vì đây là banking domain, em chú ý transaction consistency, auditability, error handling và backward compatibility khi release.
>
> Kết quả là team support hơn 5 major releases và giảm deployment/maintenance effort khoảng 60%.

## Câu hỏi: “Kafka trong banking project dùng để làm gì?”

### Outline trả lời

- Publish domain/integration events.
- Decouple services.
- Support real-time processing.
- Audit trail / event log.
- Consumer group để scale processing.
- Cần chú ý ordering, retry, idempotency, offset commit.

Câu trả lời mẫu:

> Kafka được dùng để truyền các event như cash flow, ATM và audit event giữa các microservices. Lý do dùng Kafka là cần xử lý gần real-time, decouple service và có khả năng scale consumer. Với banking, em chú ý không chỉ publish/consume mà còn phải đảm bảo event không bị mất, consumer xử lý idempotent và offset commit đúng thời điểm để tránh mất event khi service fail.

## Câu hỏi: “Bạn hiểu exactly-once trong Kafka thế nào?”

### Outline trả lời

- Exactly-once end-to-end rất khó, phụ thuộc cả producer, broker, consumer, external DB.
- Kafka có idempotent producer/transactional producer, nhưng side effect ngoài Kafka vẫn cần idempotency.
- Trong business thường thiết kế at-least-once + idempotent consumer.

Câu trả lời mẫu:

> Em không mặc định nói hệ thống exactly-once end-to-end, vì nếu consumer ghi DB hoặc gọi external service thì duplicate vẫn có thể xảy ra. Thực tế em thường thiết kế theo hướng at-least-once delivery kết hợp idempotent consumer, unique constraint/idempotency key và transaction boundary rõ ràng để duplicate event không tạo duplicate side effect.

---

# 3. Batch System Modernization

## Câu hỏi: “Bạn đã modernize legacy batch system như thế nào?”

### Outline trả lời

> Project này là modernization cho hệ thống insurance batch cũ của Zurich Insurance, trước đây chạy trên VB6 và Lotus Notes. Mục tiêu là chuyển các batch job legacy sang Java và MongoDB để dễ maintain, dễ vận hành, giảm manual operation và cải thiện release stability.
>
> Các batch job chủ yếu xử lý thông tin mà Surveyor thu thập được trong ngày. Dữ liệu này có thể có cấu trúc linh hoạt, nhiều loại form/field khác nhau theo nghiệp vụ khảo sát/inspection, nên MongoDB phù hợp để lưu document/payload survey và trạng thái xử lý. Các job được schedule theo thứ tự thời gian, có job chạy ban ngày và job chạy ban đêm; một số job có thể phụ thuộc output hoặc trạng thái của job trước.
>
> Cách làm của em/team là phân tích nghiệp vụ cùng BA/QA, reverse engineer logic từ VB6/Lotus Notes, xác định input/output của từng job, thứ tự chạy, dependency giữa các job, sau đó implement lại bằng Java. Với batch processing, em chú ý validation, job status, record-level status, logging, retry, reconciliation và khả năng rerun an toàn.
>
> Jenkins và Kubernetes được dùng để tự động hóa deployment, giảm rủi ro release thủ công. Hệ thống xử lý hơn 100K batch records mỗi ngày và giúp giảm manual operations.

## Câu hỏi: “Làm sao đảm bảo hệ thống mới đúng với legacy behavior?”

### Outline trả lời

- So sánh input/output giữa old và new.
- Regression test với sample data thực tế.
- Reconciliation report.
- Parallel run/canary nếu có.
- BA/QA verify rule nghiệp vụ.
- Logging để trace mismatch.

## Câu hỏi: “Batch job rerun an toàn như thế nào?”

### Outline trả lời

- Job execution id.
- Record status: pending/processing/success/failed.
- Idempotency key theo business record.
- Upsert/unique constraint.
- Checkpointing.
- Retry failed records thay vì rerun toàn bộ nếu phù hợp.

---

# Câu chuyện leadership nên chuẩn bị

## Mentoring junior

### Outline STAR

- S: Junior mới join, chưa quen codebase/domain.
- T: Giúp junior onboard và giảm rework.
- A: Pair programming, review code cụ thể, viết guideline, giải thích lý do sau comment.
- R: Junior tự handle task nhỏ hơn, code review ít issue lặp lại hơn.

## Code review 50+ per quarter

### Tiêu chí review nên nói

- Correctness/business logic.
- Transaction/error handling.
- Security/sensitive data.
- Performance/query.
- Test coverage.
- Readability/maintainability.
- Consistency với architecture.

## Incident handling

### Outline STAR

- S: Production alert/log error tăng.
- T: Khôi phục service và tìm root cause.
- A: Check dashboard/log, identify failing component, mitigate, communicate, postmortem.
- R: Service stable, thêm alert/test/fix để tránh lặp lại.


## Câu hỏi: “Bạn thiết kế WebSocket dual-screen flow như thế nào?”

### Outline trả lời

> Trong dual-screen flow, teller và customer tham gia cùng một transaction/session tại quầy. Khi teller khởi tạo giao dịch, backend tạo một sessionId/transactionId và liên kết teller screen với customer screen. Hai màn hình kết nối tới backend qua WebSocket channel tương ứng với session đó.
>
> Teller thực hiện các bước nghiệp vụ trên teller screen, backend validate và publish state update tới customer screen để khách hàng xem hoặc xác nhận. Khi customer xác nhận, customer screen gửi event về backend qua WebSocket/API, backend cập nhật transaction state và phản hồi lại teller screen. Tất cả state transition quan trọng được audit log.
>
> Với thiết kế này, điểm quan trọng là session management, authentication/authorization, message ordering theo session, reconnect handling, timeout, idempotency cho customer confirmation và bảo vệ dữ liệu nhạy cảm để customer chỉ thấy thông tin được phép hiển thị.

### Ý chính cần nhấn mạnh

- SessionId/transactionId liên kết hai màn hình.
- WebSocket channel theo session hoặc user/session group.
- Backend là source of truth cho transaction state.
- Message type rõ: `SESSION_STARTED`, `FORM_UPDATED`, `CUSTOMER_CONFIRM_REQUIRED`, `CUSTOMER_CONFIRMED`, `TRANSACTION_COMPLETED`, `SESSION_TIMEOUT`.
- Reconnect support: client reconnect dùng sessionId và fetch latest state.
- Timeout/cancel flow nếu customer không phản hồi.
- Audit log cho từng action quan trọng.
- Không gửi sensitive/internal data sang customer screen.

## Câu hỏi: “Vì sao dùng WebSocket thay vì REST polling?”

### Outline trả lời

- Dual-screen cần real-time interaction tại quầy.
- Teller update cần hiển thị ngay cho customer.
- Customer confirmation cần phản hồi nhanh về teller screen.
- Polling tạo latency và nhiều request không cần thiết.
- WebSocket giữ connection hai chiều, phù hợp collaborative/session-based workflow.
- Trade-off: cần quản lý connection lifecycle, reconnect, scaling và security tốt hơn REST.


## Câu hỏi: “Bạn thiết kế scheduled batch jobs ban ngày/ban đêm như thế nào?”

### Outline trả lời

> Với hệ thống Zurich, batch jobs xử lý dữ liệu Surveyor thu thập trong ngày và được chạy theo lịch cố định. Em sẽ thiết kế một batch scheduler/orchestrator để quản lý job sequence, dependency và trạng thái từng job. Các job ban ngày có thể xử lý dữ liệu gần real-time hoặc chuẩn hóa dữ liệu trong ngày; job ban đêm thường phù hợp cho tổng hợp, reconciliation, báo cáo hoặc xử lý nặng khi traffic thấp hơn.
>
> Mỗi job execution cần có jobId, business date, status, start/end time, total/success/failed count. Ở mức record, cần lưu record status để biết record nào success, failed hoặc cần retry. Nếu một job fail, orchestrator phải biết job sau có được chạy tiếp không hay phải dừng theo dependency rule. Khi rerun, job phải idempotent để không tạo duplicate output.

### Ý chính cần nhấn mạnh

- Có scheduler/orchestrator quản lý thứ tự job.
- Có dependency graph hoặc job sequence config.
- Có day jobs và night jobs với mục đích khác nhau.
- Job status và record-level status lưu persistent.
- Rerun theo business date/jobId.
- Idempotency bằng business key/upsert/unique constraint.
- Alert nếu job fail hoặc chạy quá SLA.

## Câu hỏi: “Vì sao MongoDB phù hợp cho surveyor data?”

### Outline trả lời

- Surveyor data thường là document/form-based, field có thể thay đổi theo loại survey/inspection.
- MongoDB lưu JSON-like document linh hoạt hơn relational schema cứng.
- Có thể lưu raw payload, normalized fields cần query, processing status, metadata.
- Vẫn cần index theo surveyorId, businessDate, status, policy/claim/reference id nếu có.
- Không chọn MongoDB chỉ vì “nhanh hơn”; chọn vì data shape và access pattern phù hợp.

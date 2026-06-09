# Project-Based Preparation — Câu hỏi theo từng dự án trong CV

## Cách dùng tài liệu này

Với mỗi project, bạn nên trả lời được 6 nhóm câu hỏi:

1. Business context là gì?
2. Architecture tổng quan như thế nào?
3. Vai trò cá nhân của bạn là gì?
4. Technical challenge lớn nhất là gì?
5. Bạn đã đưa ra decision/trade-off nào?
6. Kết quả đo được là gì?

---

# Project 1: User Notification System Modernization

## Thông tin từ CV

- Client: Cox Automotive.
- Company: FPT Software.
- Time: Apr 2025 – Present.
- Role: Senior Backend Engineer.
- Scale: 1M+ events/day.
- Tech: AWS Lambda, SQS, EventBridge, DynamoDB, S3, CDK, GitHub Actions, SonarQube, CloudWatch, Slack.
- Impact:
  - Reduced manual processing by 70%.
  - Deployment time under 30 minutes.
  - Resolved 100+ security/code quality issues.
  - Reduced incident detection time by 50%.

## Câu hỏi dễ

1. Project này giải quyết bài toán gì?
2. Notification system xử lý loại event nào?
3. Bạn dùng những AWS services nào?
4. Vai trò của Lambda trong hệ thống là gì?
5. SQS khác EventBridge như thế nào trong project này?

## Câu hỏi trung bình

1. Vì sao chọn event-driven/serverless architecture?
2. Làm sao hệ thống support 1M+ events/day?
3. Bạn thiết kế retry và DLQ như thế nào?
4. Làm sao tránh gửi notification trùng?
5. Bạn dùng DynamoDB để lưu loại data gì?
6. CI/CD bằng AWS CDK và GitHub Actions hoạt động thế nào?
7. Monitoring bằng CloudWatch và Slack alert gồm những metric nào?

## Câu hỏi khó

1. Nếu một event được process thành công nhưng response/log bị fail, làm sao đảm bảo không xử lý trùng?
2. Nếu downstream notification provider bị down, hệ thống xử lý thế nào?
3. Làm sao tránh hot partition trong DynamoDB với 1M+ events/day?
4. Khi nào chọn Kafka thay vì SQS/EventBridge cho notification system?
5. Serverless có nhược điểm gì: cold start, observability, local testing, cost?
6. Bạn đo “reduced manual processing by 70%” như thế nào?
7. Nếu event ordering là requirement, bạn xử lý ra sao?

---

# Project 2: Banking Teller Platform Development

## Thông tin từ CV

- Client: OCBC Bank.
- Company: DXC Technology.
- Time: Jan 2024 – Mar 2025.
- Role: Backend Engineer.
- Tech: Java, Spring Boot, DDD, Kafka, OpenShift, Jenkins, Kibana.
- Scope:
  - Developed 5 core backend services.
  - Supported daily operations across multiple branches.
  - Kafka messaging for real-time cash flow, ATM, audit events.
  - 5+ major releases.
  - Reduced deployment/maintenance effort by 60%.


## Bổ sung domain chi tiết: Dual-screen teller-customer system

Hệ thống ở OCBC Bank không chỉ là backend banking API thông thường, mà là một nền tảng giao dịch tại quầy nơi **bank teller và customer tương tác trực tiếp qua hai màn hình được đồng bộ real-time**. Teller thao tác trên màn hình nghiệp vụ, customer xem/xác nhận thông tin trên màn hình khách hàng. Hai màn hình kết nối và đồng bộ trạng thái qua **WebSocket**.

Các module bạn tham gia phát triển:

- Payment.
- Insurance.
- Cheque Service.
- Account Closure.
- Remittance.

### Câu hỏi bổ sung dễ bị hỏi

1. Dual-screen teller-customer flow hoạt động như thế nào?
2. Vì sao dùng WebSocket thay vì REST polling?
3. Làm sao đồng bộ state giữa teller screen và customer screen?
4. Nếu WebSocket disconnect giữa giao dịch thì xử lý thế nào?
5. Module Payment/Insurance/Cheque/Account Closure/Remittance khác nhau ở business flow nào?
6. Làm sao đảm bảo customer chỉ thấy đúng thông tin cần xác nhận, không thấy dữ liệu nhạy cảm nội bộ?

## Câu hỏi dễ

1. Banking teller platform là gì?
2. Bạn phát triển 5 service nào hoặc loại service nào?
3. DDD được áp dụng ở mức nào?
4. Kafka dùng để xử lý event gì?
5. OpenShift khác gì so với deploy VM truyền thống?

## Câu hỏi trung bình

1. Bạn thiết kế service boundary như thế nào?
2. Làm sao đảm bảo audit events không bị mất?
3. Làm sao handle transaction consistency trong banking?
4. Code review giúp giảm production issue như thế nào?
5. Bạn monitor production bằng Kibana ra sao?
6. Jenkins pipeline gồm những stage nào?

## Câu hỏi khó

1. Nếu Kafka consumer process fail giữa chừng, làm sao đảm bảo không mất event và không duplicate side effect?
2. Banking system cần exactly-once không? Bạn giải thích thế nào?
3. DDD aggregate boundary cho cash transaction nên thiết kế thế nào?
4. Làm sao migrate/release service mà không downtime?
5. Nếu có event cash flow đến out-of-order, xử lý thế nào?
6. Bạn giảm deployment/maintenance effort 60% bằng những thay đổi cụ thể nào?

---

# Project 3: Batch System Modernization

## Thông tin từ CV

- Client: Zurich Insurance.
- Company: DXC Technology.
- Time: Oct 2021 – Dec 2023.
- Role: Backend Engineer.
- Tech: Legacy VB6, Lotus Notes → Java batch jobs/services, MongoDB, Jenkins, Kubernetes.
- Domain: xử lý dữ liệu survey/inspection mà Surveyor thu thập trong ngày.
- Batch schedule: các job chạy theo thứ tự thời gian, có nhóm job ban ngày và job ban đêm.
- Scale: 100K+ batch records/day.
- Collaboration: BA, QA.


## Bổ sung domain chi tiết: Zurich Surveyor Batch Job Modernization

Dự án Zurich Insurance là modernization một hệ thống batch job legacy chạy trên **VB6** và **Lotus Notes**, chuyển sang sử dụng **Java** và **MongoDB**. Các batch job chủ yếu xử lý thông tin mà **Surveyor** thu thập trong ngày, ví dụ dữ liệu khảo sát/inspection, thông tin khách hàng/tài sản/hồ sơ, hình ảnh hoặc tài liệu metadata tùy nghiệp vụ.

Đặc điểm quan trọng:

- Batch jobs chạy theo lịch và theo thứ tự giờ giấc.
- Có nhóm job ban ngày và nhóm job ban đêm.
- Job sau có thể phụ thuộc output/trạng thái của job trước.
- Cần xử lý dữ liệu thu thập trong ngày, tổng hợp, validate, transform và lưu vào MongoDB hoặc publish/prepare cho downstream processing.
- Modernization cần đảm bảo behavior tương thích hệ thống cũ và giảm manual operation.

### Câu hỏi bổ sung dễ bị hỏi

1. Surveyor data là loại dữ liệu gì và vì sao phù hợp MongoDB?
2. Batch job ban ngày và ban đêm khác nhau như thế nào?
3. Làm sao đảm bảo job chạy đúng thứ tự?
4. Nếu một job ban ngày fail thì job ban đêm phụ thuộc xử lý thế nào?
5. Làm sao migrate logic từ VB6/Lotus Notes sang Java mà không làm sai business rule?
6. Làm sao rerun batch job an toàn khi dữ liệu survey đã được xử lý một phần?

## Câu hỏi dễ

1. Legacy system cũ gồm những công nghệ nào?
2. Vì sao cần modernization?
3. Batch system xử lý dữ liệu gì?
4. Bạn dùng SQL Server và MongoDB cho mục đích gì?
5. Bạn phối hợp với BA/QA như thế nào?

## Câu hỏi trung bình

1. Bạn migrate legacy module sang Spring Boot như thế nào?
2. Làm sao đảm bảo behavior mới tương thích hệ thống cũ?
3. Batch job xử lý lỗi và retry thế nào?
4. Làm sao test batch processing 100K+ records/day?
5. Jenkins/Kubernetes giúp release stability ra sao?

## Câu hỏi khó

1. Chiến lược strangler pattern có áp dụng được không?
2. Nếu legacy logic không có document rõ ràng, bạn reverse engineer như thế nào?
3. Làm sao đảm bảo data reconciliation giữa old và new system?
4. Nếu batch chạy quá lâu, bạn tối ưu thế nào?
5. Làm sao thiết kế batch idempotent để rerun an toàn?

---

# Leadership và Cross-Project Questions

## Câu hỏi dễ

1. Bạn từng mentor junior như thế nào?
2. Bạn review code theo tiêu chí nào?
3. Bạn làm việc với international team ra sao?

## Câu hỏi trung bình

1. Khi junior viết code chưa đạt, bạn feedback như thế nào?
2. Bạn cân bằng delivery deadline và code quality ra sao?
3. Architecture discussion bạn thường đóng góp phần nào?

## Câu hỏi khó

1. Kể về lần bạn disagree với technical decision của team.
2. Kể về production incident và cách bạn xử lý.
3. Nếu Product muốn release nhanh nhưng QA phát hiện issue nghiêm trọng, bạn làm gì?

---

# Gợi ý trả lời nhanh cho các câu hỏi trong file

## Project 1 — Cox Automotive Notification System

### Easy

#### 1. Project này giải quyết bài toán gì?

Hệ thống hiện đại hóa notification/event processing cho Cox Automotive, giúp nhận và xử lý lượng lớn event theo hướng cloud-native, giảm thao tác manual, tăng reliability và scalability.

#### 2. Notification system xử lý loại event nào?

Có thể là event từ các workflow automotive như cập nhật trạng thái service/listing/appointment/operation. Khi trả lời nên nói theo project context: event từ nhiều producer được route, queue, xử lý và cập nhật trạng thái notification.

#### 3. Bạn dùng những AWS services nào?

Lambda để xử lý event, EventBridge để route event, SQS để queue/buffer/retry, DynamoDB để lưu status/idempotency/metadata, S3 nếu cần payload/artifact lớn, CloudWatch cho monitoring, CDK + GitHub Actions cho CI/CD.

#### 4. Vai trò của Lambda trong hệ thống là gì?

Lambda là worker serverless consume message từ SQS hoặc event trigger, validate payload, check idempotency, xử lý business logic/gọi provider và cập nhật trạng thái.

#### 5. SQS khác EventBridge như thế nào trong project này?

EventBridge là event bus/routing layer theo rule; SQS là queue để buffer, decouple consumer, retry và DLQ. EventBridge quyết định event đi đâu, SQS giúp xử lý ổn định theo tốc độ consumer.

### Medium

#### 1. Vì sao chọn event-driven/serverless architecture?

Vì notification workload async, có traffic spike và không nên block main flow. Serverless giảm operational overhead, scale theo event, còn event-driven giúp decouple producer/consumer. Trade-off là cần xử lý duplicate, eventual consistency và observability tốt.

#### 2. Làm sao hệ thống support 1M+ events/day?

Không chỉ tính average 1M/day khoảng 12 events/sec mà phải tính peak. SQS buffer spike, Lambda scale theo queue depth, DynamoDB key phải phân tán, provider rate limit cần backpressure, monitoring queue age/error/DLQ/throttle.

#### 3. Bạn thiết kế retry và DLQ như thế nào?

Transient errors retry có giới hạn/backoff; permanent errors không retry vô hạn. Sau max receive count message vào DLQ, có alert và quy trình replay sau khi fix root cause.

#### 4. Làm sao tránh gửi notification trùng?

Mỗi event có eventId/idempotency key. Lambda dùng DynamoDB conditional write/atomic update để chỉ process một lần. Duplicate event sẽ skip hoặc trả trạng thái cũ. Nếu provider hỗ trợ idempotency, truyền key sang provider.

#### 5. Bạn dùng DynamoDB để lưu loại data gì?

Lưu event/notification status, idempotency record, retry count, error code, timestamps, correlationId và metadata. Payload lớn có thể lưu S3, DynamoDB chỉ lưu reference.

#### 6. CI/CD bằng AWS CDK và GitHub Actions hoạt động thế nào?

Pipeline build/test/static scan, CDK synth/diff, deploy theo environment, smoke test, approval nếu prod, monitor sau deploy. CDK quản lý infrastructure as code giúp deploy repeatable và giảm manual step.

#### 7. Monitoring bằng CloudWatch và Slack alert gồm những metric nào?

SQS queue depth, age of oldest message, DLQ count, Lambda error/duration/throttle/concurrency, DynamoDB throttling, provider latency/error, notification sent/failed/retried.

### Hard

#### 1. Nếu một event được process thành công nhưng response/log bị fail, làm sao đảm bảo không xử lý trùng?

Dùng idempotency state trong persistent store. Khi side effect thành công, update trạng thái `SENT/PROCESSED`. Retry sau đó check state trước khi gửi lại. Log failure không được quyết định business state.

#### 2. Nếu downstream notification provider bị down, hệ thống xử lý thế nào?

Timeout ngắn, retry backoff, không retry storm, giữ message trong queue hoặc DLQ, alert, có replay process. Nếu provider down lâu, có thể pause/limit consumer và áp dụng business rule cho notification quá hạn.

#### 3. Làm sao tránh hot partition trong DynamoDB với 1M+ events/day?

Chọn partition key high-cardinality như eventId/userId, tránh key ít giá trị như status. Với query theo status dùng GSI có bucket/shard theo time/random suffix. Monitor throttling/hot key.

#### 4. Khi nào chọn Kafka thay vì SQS/EventBridge cho notification system?

Chọn Kafka nếu cần event streaming throughput cao, replay theo retention, nhiều consumer groups, ordering theo partition. Chọn SQS/EventBridge nếu cần AWS-native managed queue/routing, retry/DLQ đơn giản, serverless integration.

#### 5. Serverless có nhược điểm gì?

Cold start, giới hạn runtime/concurrency/payload, observability/debug local khó hơn, cost khó dự đoán nếu traffic/retry cao, vendor lock-in và cần quản lý IAM/limits kỹ.

#### 6. Bạn đo “reduced manual processing by 70%” như thế nào?

So sánh số bước/thời gian/ticket manual trước và sau automation: số event cần xử lý tay, thời gian vận hành, deployment/manual recovery steps. Nên nói đây là metric team/project đo theo operational workload.

#### 7. Nếu event ordering là requirement, bạn xử lý ra sao?

Clarify ordering global hay theo entity/user. Ordering global rất tốn throughput. Nếu theo user/entity, dùng SQS FIFO message group id hoặc Kafka partition key theo user/entity, vẫn cần idempotency.

## Project 2 — OCBC Bank Dual-Screen Teller Platform

### Câu hỏi bổ sung dual-screen

#### 1. Dual-screen teller-customer flow hoạt động như thế nào?

Teller mở giao dịch, backend tạo sessionId/transactionId, teller screen và customer screen join cùng session qua WebSocket. Teller nhập thông tin, backend validate/push state sang customer screen, customer xác nhận, backend cập nhật state, audit và phản hồi real-time cho teller.

#### 2. Vì sao dùng WebSocket thay vì REST polling?

Vì hai màn hình cần đồng bộ real-time hai chiều. WebSocket giảm latency và request polling không cần thiết. Trade-off là phải xử lý connection lifecycle, reconnect, scaling và security.

#### 3. Làm sao đồng bộ state giữa teller screen và customer screen?

Backend là source of truth. Mỗi message có sessionId, transactionId, sequence/version, correlationId. Client update theo state từ backend; nếu reconnect hoặc sequence mismatch thì fetch latest snapshot.

#### 4. Nếu WebSocket disconnect giữa giao dịch thì xử lý thế nào?

Client reconnect với session token/sessionId, backend validate session còn active và trả latest state. Nếu quá timeout thì cancel/expire session. Mọi disconnect/reconnect quan trọng nên audit/log.

#### 5. Module Payment/Insurance/Cheque/Account Closure/Remittance khác nhau ở business flow nào?

Payment/Remittance cần idempotency, fee/rate/beneficiary validation và transaction consistency; Insurance cần hiển thị/confirm sản phẩm hoặc consent; Cheque Service cần validation/workflow status; Account Closure cần eligibility checks, multi-step confirmation và compliance/audit.

#### 6. Làm sao đảm bảo customer chỉ thấy đúng thông tin cần xác nhận?

Backend tạo DTO riêng cho customer screen, mask sensitive data, không push full internal transaction object, áp dụng authorization theo session, audit dữ liệu customer đã xem/xác nhận.

### Easy

#### 1. Banking teller platform là gì?

Là nền tảng hỗ trợ giao dịch tại quầy giữa teller và customer, trong project OCBC có dual-screen realtime bằng WebSocket và các module Secure Inventory, Payment Insurance, Cheque Service, Account Closure, Remittance.

#### 2. Bạn phát triển 5 service/module nào?

Secure Inventory, Payment Insurance, Cheque Service, Account Closure và Remittance. Có thể nói thêm các module này dùng chung pattern session → teller input → customer confirmation → backend processing → audit/event.

#### 3. DDD được áp dụng ở mức nào?

Áp dụng ở việc chia bounded context/module theo nghiệp vụ, dùng domain language, tách business logic khỏi controller/infrastructure, định nghĩa aggregate/service/repository theo domain.

#### 4. Kafka dùng để xử lý event gì?

Kafka dùng cho cash flow, ATM, transaction/audit events hoặc integration events giữa services/downstream systems, giúp decouple và xử lý gần real-time.

#### 5. OpenShift khác gì so với deploy VM truyền thống?

OpenShift chạy container orchestration, hỗ trợ replicas, rolling deployment, health probes, config/secrets, autoscaling và quản lý deployment nhất quán hơn VM thủ công.

### Medium

#### 1. Bạn thiết kế service boundary như thế nào?

Theo business capability: Secure Inventory, Payment Insurance, Cheque Service, Account Closure, Remittance, session orchestration/audit. Boundary dựa trên ownership data, business workflow, transaction boundary và integration needs.

#### 2. Làm sao đảm bảo audit events không bị mất?

Ghi audit trong DB transaction hoặc dùng outbox pattern: business data + outbox event cùng transaction, publisher publish Kafka sau, retry nếu fail, consumer idempotent.

#### 3. Làm sao handle transaction consistency trong banking?

Dùng DB transaction cho local operation, idempotency key chống double submit, locking/version cho concurrent updates, audit log. Với cross-service dùng saga/outbox/idempotent consumer.

#### 4. Code review giúp giảm production issue như thế nào?

Review correctness, transaction/error handling, security, sensitive data logging, query performance, test coverage và consistency với architecture. Feedback sớm giảm bug trước release.

#### 5. Bạn monitor production bằng Kibana ra sao?

Dùng structured logs với correlationId/sessionId/transactionId, query lỗi theo time/service/errorCode, theo dõi state transition, không log sensitive data. Kết hợp metric/alert nếu có.

#### 6. Jenkins pipeline gồm những stage nào?

Checkout, build, unit test, static analysis, package image/artifact, push registry, deploy dev/staging, smoke test, approval/prod deploy, post-deploy verification/rollback plan.

### Hard

#### 1. Nếu Kafka consumer fail giữa chừng, làm sao không mất event và không duplicate side effect?

Commit offset sau khi xử lý thành công. Side effect phải idempotent bằng eventId/unique constraint/processed_event table. Lỗi retry; poison message vào dead-letter topic.

#### 2. Banking system cần exactly-once không?

End-to-end exactly-once rất khó khi có DB/external systems. Thực tế nên thiết kế at-least-once delivery + idempotent processing + transaction/outbox để side effect đúng một lần về business.

#### 3. DDD aggregate boundary cho cash transaction nên thiết kế thế nào?

Aggregate nên bao quanh invariant cần consistency mạnh. Ví dụ Transaction aggregate quản lý transaction state; Account/CashDrawer có version/lock riêng nếu update balance/cash. Tránh aggregate quá lớn gây lock contention.

#### 4. Làm sao migrate/release service mà không downtime?

Backward-compatible API/event/schema, rolling deploy, feature flag, database migration expand-contract, health checks, canary nếu có, rollback plan và monitoring sau release.

#### 5. Nếu có event cash flow đến out-of-order, xử lý thế nào?

Dùng sequence/version/timestamp theo entity, idempotency, buffer hoặc reject stale events tùy business, partition by entity nếu cần ordering, reconciliation để phát hiện lệch.

#### 6. Bạn giảm deployment/maintenance effort 60% bằng những thay đổi cụ thể nào?

Tự động hóa Jenkins/OpenShift deployment, chuẩn hóa pipeline/config, giảm manual steps, cải thiện logging/monitoring, code review/quality gate và runbook vận hành.

## Project 3 — Zurich Insurance Batch Job Modernization

### Câu hỏi bổ sung Zurich

#### 1. Surveyor data là loại dữ liệu gì và vì sao phù hợp MongoDB?

Là dữ liệu survey/inspection/form do Surveyor thu thập trong ngày, có schema linh hoạt theo loại khảo sát. MongoDB phù hợp vì lưu document/raw payload/metadata/status linh hoạt và index theo access pattern.

#### 2. Batch job ban ngày và ban đêm khác nhau như thế nào?

Day jobs thường ingest/validate/normalize dữ liệu trong ngày. Night jobs thường tổng hợp, reconciliation, generate report hoặc downstream sync khi traffic thấp.

#### 3. Làm sao đảm bảo job chạy đúng thứ tự?

Dùng scheduler/orchestrator quản lý job sequence/dependency, lưu job status persistent, job sau chỉ chạy khi dependency success, dùng lock/unique key để tránh duplicate execution.

#### 4. Nếu một job ban ngày fail thì job ban đêm phụ thuộc xử lý thế nào?

Mark day job failed/partial, block hoặc skip night job theo dependency rule, alert operation team. Sau khi fix/rerun day job thành công thì trigger/resume night job.

#### 5. Làm sao migrate logic từ VB6/Lotus Notes sang Java mà không sai business rule?

Inventory jobs, reverse engineer input/output/rules, làm việc với BA/QA, tạo golden dataset, chạy old vs new song song nếu có thể, reconciliation output và sign-off trước cutover.

#### 6. Làm sao rerun batch job an toàn khi dữ liệu survey đã được xử lý một phần?

Lưu job/record status, checkpoint, dùng business key/upsert/unique constraint, skip record đã success hoặc process idempotently, retry failed/pending records.

### Easy

#### 1. Legacy system cũ gồm những công nghệ nào?

VB6 và Lotus Notes, được modernize sang Java và MongoDB.

#### 2. Vì sao cần modernization?

Legacy khó maintain/test/deploy, thiếu observability, manual operation cao. Java/MongoDB giúp maintainable hơn, CI/CD tốt hơn, xử lý document survey linh hoạt và vận hành ổn định hơn.

#### 3. Batch system xử lý dữ liệu gì?

Xử lý dữ liệu Surveyor thu thập trong ngày: survey/inspection forms, metadata, trạng thái xử lý, dữ liệu phục vụ reconciliation/report/downstream.

#### 4. Bạn dùng SQL Server và MongoDB cho mục đích gì?

Theo thông tin cập nhật, trọng tâm là MongoDB cho surveyor documents/raw payload/status/metadata. Nếu có SQL Server trong hệ khác, SQL phù hợp structured/reporting data; MongoDB phù hợp flexible survey document.

#### 5. Bạn phối hợp với BA/QA như thế nào?

Làm rõ business rules legacy, xác nhận input/output từng job, chuẩn bị test cases/golden datasets, verify reconciliation và support UAT/release.

### Medium

#### 1. Bạn migrate legacy module sang Spring Boot/Java như thế nào?

Phân tích job legacy, document schedule/dependency/input/output, implement Java job tương ứng, test với golden dataset, parallel run/reconciliation, cutover từng phần nếu có thể.

#### 2. Làm sao đảm bảo behavior mới tương thích hệ thống cũ?

So sánh output old vs new với cùng input, regression test, reconciliation report, BA/QA sign-off, log mismatch và fix rule mapping.

#### 3. Batch job xử lý lỗi và retry thế nào?

Phân loại transient vs validation error, retry có giới hạn cho transient, record invalid lưu failed status/error code, DLQ/error report nếu có, rerun failed records sau khi fix.

#### 4. Làm sao test batch processing 100K+ records/day?

Test unit rule, integration DB, performance/load test với dataset lớn, verify memory/chunking/index, test failure/restart/rerun và reconciliation.

#### 5. Jenkins/Kubernetes giúp release stability ra sao?

Jenkins tự động build/test/deploy; Kubernetes quản lý container, config, replicas, health check, rollback/rolling deployment và giảm manual deployment error.

### Hard

#### 1. Chiến lược strangler pattern có áp dụng được không?

Có nếu có thể tách từng job/module legacy và route từng phần sang Java mới. Chạy song song old/new, compare output, cutover dần để giảm rủi ro big bang.

#### 2. Nếu legacy logic không có document rõ ràng, bạn reverse engineer như thế nào?

Đọc code VB6/Lotus Notes, phân tích input/output thực tế, hỏi BA/SME, tạo test data, log/trace behavior legacy, document rule và xác nhận bằng reconciliation.

#### 3. Làm sao đảm bảo data reconciliation giữa old và new system?

Chạy cùng input, compare count/totals/status/output fields, generate mismatch report, phân loại expected vs bug, BA/QA review và sign-off trước cutover.

#### 4. Nếu batch chạy quá lâu, bạn tối ưu thế nào?

Measure bottleneck, tối ưu MongoDB index/query, chunk size, bulk operations, streaming thay vì load all, controlled parallelism theo partition, tránh lock/contention và monitor duration.

#### 5. Làm sao thiết kế batch idempotent để rerun an toàn?

Dùng business key, upsert/unique constraint, record-level status, checkpoint, job execution theo businessDate, skip success records hoặc reprocess không tạo duplicate output.

## Leadership và Cross-Project Questions

### Easy

#### 1. Bạn từng mentor junior như thế nào?

Pair programming/review PR, giải thích lý do sau comment, hướng dẫn coding standard/domain, giao task tăng dần độ khó và follow-up để giảm lỗi lặp lại.

#### 2. Bạn review code theo tiêu chí nào?

Correctness, business rules, transaction/error handling, security, performance/query, test coverage, readability, maintainability và consistency với architecture.

#### 3. Bạn làm việc với international team ra sao?

Giao tiếp rõ bằng tiếng Anh, confirm requirement bằng example, summarize decision sau meeting, raise blocker sớm và phối hợp Product/QA/BA theo Agile.

### Medium

#### 1. Khi junior viết code chưa đạt, bạn feedback như thế nào?

Feedback cụ thể, respectful, giải thích impact và đề xuất cách sửa. Nếu issue lặp lại, pair review hoặc tạo guideline/example.

#### 2. Bạn cân bằng delivery deadline và code quality ra sao?

Ưu tiên critical quality như correctness/security/transaction; scope non-critical có thể phase sau. Communicate trade-off/risk với team lead/Product và giữ rollback/test tối thiểu.

#### 3. Architecture discussion bạn thường đóng góp phần nào?

Đóng góp service boundary, transaction consistency, event design, retry/idempotency, database design, deployment/observability và trade-off giữa options.

### Hard

#### 1. Kể về lần bạn disagree với technical decision của team.

Dùng STAR: nêu context, options, trade-off, cách bạn đưa evidence/prototype, team quyết định gì và kết quả. Tránh nói theo hướng cá nhân thắng/thua.

#### 2. Kể về production incident và cách bạn xử lý.

Detect qua alert/log, mitigate impact trước, check recent changes/metrics, rollback/hotfix nếu cần, communicate status, root cause, postmortem và action item phòng ngừa.

#### 3. Nếu Product muốn release nhanh nhưng QA phát hiện issue nghiêm trọng, bạn làm gì?

Đánh giá severity/impact, nếu critical thì đề xuất block release hoặc disable feature. Communicate rõ risk, option hotfix/rollback/feature flag. Với banking/security, ưu tiên an toàn và auditability.

# OCBC Dual-Screen WebSocket System Design — Teller & Customer Interaction

## 1. Bối cảnh project

Trong dự án OCBC Bank, bạn cùng team phát triển hệ thống giao dịch tại quầy theo mô hình **dual screen**:

- **Bank teller** thao tác nghiệp vụ trên màn hình nội bộ.
- **Customer** tương tác/xác nhận thông tin trên màn hình khách hàng tại quầy.
- Hai màn hình được đồng bộ real-time thông qua **WebSocket**.
- Backend dùng Java/Spring Boot, triển khai trên OpenShift, CI/CD bằng Jenkins, logging/monitoring qua Kibana.

Bạn tham gia phát triển 5 module:

1. **Payment**
2. **Insurance**
3. **Cheque Service**
4. **Account Closure**
5. **Remittance**

## 2. High-level architecture

```text
┌──────────────────┐                         ┌────────────────────┐
│ Teller Screen    │                         │ Customer Screen    │
│ Internal UI      │                         │ Counter UI         │
└────────┬─────────┘                         └─────────┬──────────┘
         │ REST + WebSocket                             │ WebSocket
         │                                              │
         └───────────────┐              ┌───────────────┘
                         ↓              ↓
              ┌────────────────────────────────┐
              │ API Gateway / WebSocket Gateway │
              └────────────────┬───────────────┘
                               ↓
              ┌────────────────────────────────┐
              │ Spring Boot Backend Services    │
              │ - Session Orchestration         │
              │ - Payment                       │
              │ - Insurance                     │
              │ - Cheque Service                │
              │ - Account Closure               │
              │ - Remittance                    │
              │ - Audit/Event Publisher         │
              └────────────────┬───────────────┘
                               ↓
              ┌────────────────────────────────┐
              │ Relational DB                   │
              │ session / transaction / audit   │
              └────────────────┬───────────────┘
                               ↓
              ┌────────────────────────────────┐
              │ Kafka Topics                    │
              │ transaction / audit / cash flow │
              └────────────────────────────────┘
```

## 3. Core flow: teller và customer giao dịch qua dual screen

```text
1. Teller đăng nhập và chọn module nghiệp vụ: Payment/Insurance/Cheque/Account Closure/Remittance.
2. Backend tạo teller-customer session với sessionId và transactionId.
3. Teller screen mở WebSocket connection vào session.
4. Customer screen được pair vào cùng session.
5. Teller nhập thông tin giao dịch.
6. Backend validate bước hiện tại và push thông tin cần hiển thị sang customer screen.
7. Customer xem thông tin, xác nhận hoặc từ chối.
8. Backend nhận confirmation event, kiểm tra idempotency và cập nhật state.
9. Teller screen nhận kết quả real-time.
10. Backend ghi transaction/audit, publish Kafka event nếu cần, kết thúc session.
```

## 4. WebSocket message design

### Message envelope

```json
{
  "messageId": "uuid",
  "sessionId": "session-123",
  "transactionId": "txn-456",
  "type": "CUSTOMER_CONFIRM_REQUIRED",
  "sequence": 12,
  "timestamp": "2026-06-06T10:00:00Z",
  "correlationId": "corr-789",
  "payload": {}
}
```

### Message types

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

## 5. Session và state management

Backend nên là **source of truth** cho session state.

State ví dụ:

```text
CREATED
TELLER_CONNECTED
CUSTOMER_CONNECTED
IN_PROGRESS
WAITING_CUSTOMER_CONFIRMATION
CUSTOMER_CONFIRMED
PROCESSING
COMPLETED
CANCELLED
TIMEOUT
FAILED
```

Các điểm cần chú ý:

- Không tin hoàn toàn state từ frontend.
- Mỗi state transition phải validate.
- Customer confirmation cần idempotency.
- Session timeout nếu không có phản hồi.
- Reconnect phải fetch latest state từ backend.

## 6. Database design gợi ý

### `dual_screen_session`

```text
session_id
branch_id
counter_id
teller_id
customer_ref/session_customer_id
current_module
current_transaction_id
status
created_at
updated_at
expires_at
```

### `transaction`

```text
transaction_id
session_id
module_type       -- PAYMENT / INSURANCE / CHEQUE / ACCOUNT_CLOSURE / REMITTANCE
status
amount
currency
created_by_teller
created_at
updated_at
```

### `customer_confirmation`

```text
confirmation_id
transaction_id
session_id
confirmation_type
status            -- REQUIRED / CONFIRMED / REJECTED / EXPIRED
confirmed_at
idempotency_key
request_hash
```

### `audit_log`

```text
audit_id
session_id
transaction_id
actor_type        -- TELLER / CUSTOMER / SYSTEM
actor_id
action
timestamp
correlation_id
metadata
```

## 7. Module-specific design notes

## Payment

System design concerns:

- Idempotency cho payment submission.
- Customer confirmation trước khi execute.
- Transaction status rõ ràng.
- Audit every critical step.
- Handle duplicate click/double submit.

Câu hỏi có thể bị hỏi:

- Làm sao tránh payment bị submit hai lần?
- Nếu customer confirmed nhưng backend process fail thì trạng thái gì?
- Nếu teller screen disconnect sau khi customer confirmed thì sao?

## Insurance

System design concerns:

- Hiển thị thông tin sản phẩm/benefit/fee cho customer screen.
- Customer review/confirmation.
- Có thể cần document/reference data.
- Audit customer consent.

Câu hỏi có thể bị hỏi:

- Làm sao đảm bảo customer đã xem/xác nhận đúng thông tin?
- Dữ liệu nào được phép hiển thị trên customer screen?
- Cache insurance product/reference data thế nào?

## Cheque Service

System design concerns:

- Validation cheque information.
- Workflow status.
- Audit action.
- Có thể cần integration downstream.

Câu hỏi có thể bị hỏi:

- Thiết kế workflow xử lý cheque service như thế nào?
- Nếu validation fail thì đồng bộ lỗi về hai màn hình ra sao?

## Account Closure

System design concerns:

- Eligibility checks.
- Multi-step customer confirmation.
- Compliance/audit.
- Không cho đóng account nếu còn pending transaction/constraint.

Câu hỏi có thể bị hỏi:

- Trước khi close account cần check gì?
- Làm sao thiết kế multi-step confirmation?
- Nếu customer đổi ý giữa flow thì rollback/cancel thế nào?

## Remittance

System design concerns:

- Beneficiary validation.
- Fee/exchange rate display.
- Customer confirmation.
- Transaction consistency.
- Audit and compliance.

Câu hỏi có thể bị hỏi:

- Làm sao hiển thị fee/exchange rate và đảm bảo không stale?
- Nếu rate thay đổi trước khi customer confirm thì xử lý thế nào?
- Làm sao tránh duplicate remittance transaction?

## 8. Scaling WebSocket trên OpenShift

Câu hỏi hard có thể gặp:

> Nếu nhiều branch/counter cùng mở dual-screen session, bạn scale WebSocket như thế nào?

Ý chính nên trả lời:

- WebSocket connection là long-lived, cần nhiều replicas nhưng phải quản lý routing.
- Nếu dùng sticky session ở load balancer, một connection gắn với một pod.
- Nếu cần broadcast/cross-pod messaging, dùng Redis pub/sub, Kafka, hoặc message broker nội bộ.
- Session state không nên chỉ nằm trong memory của pod; lưu DB/Redis để reconnect.
- Readiness/liveness probes và graceful shutdown để không cắt connection đột ngột khi rolling deploy.
- Monitor active connections, message rate, disconnect rate, error rate.

Lỗi cần tránh:

- Lưu toàn bộ session state trong memory và không có recovery.
- Không nghĩ tới rolling deployment làm mất WebSocket connections.
- Không có reconnect strategy.

## 9. Failure scenarios và cách trả lời

## WebSocket disconnect

Ý chính:

- Client reconnect với sessionId/token.
- Backend validate session còn active.
- Fetch latest state từ DB/state store.
- Resume từ state gần nhất.
- Nếu timeout quá lâu, cancel/expire session.
- Audit disconnect/reconnect nếu cần.

## Customer confirms duplicate

Ý chính:

- Confirmation có idempotency key/messageId.
- Atomic update từ `WAITING_CUSTOMER_CONFIRMATION` sang `CUSTOMER_CONFIRMED`.
- Duplicate confirmation trả same result hoặc ignore.

## Teller và customer state lệch nhau

Ý chính:

- Backend là source of truth.
- Client nhận state version/sequence.
- Nếu sequence mismatch, client fetch latest snapshot.
- Không rely vào frontend local state.

## Backend pod restart

Ý chính:

- Connection mất, client reconnect.
- Session state lưu persistent/distributed store.
- Graceful shutdown khi deploy.
- Health checks và rolling deploy.

## 10. Interview questions theo mức độ

## Easy

### Q1. Dual-screen system là gì?

**Mục đích:** kiểm tra bạn giải thích được business context.

**Ý chính:** teller và customer tương tác tại quầy qua hai màn hình đồng bộ real-time bằng WebSocket.

**Tránh:** nói chung chung là “web app banking”.

### Q2. Vì sao dùng WebSocket?

**Mục đích:** kiểm tra hiểu real-time communication.

**Ý chính:** two-way real-time updates, giảm polling, customer confirmation phản hồi ngay cho teller.

**Tránh:** nói WebSocket luôn tốt hơn REST; phải nói trade-off.

## Medium

### Q3. Thiết kế message flow khi teller yêu cầu customer xác nhận payment.

**Mục đích:** kiểm tra flow/state/idempotency.

**Ý chính:** teller input → backend validate → push confirm request → customer confirm → backend atomic state update → teller receives result → audit.

**Tránh:** để frontend tự quyết định transaction completed.

### Q4. Database cần lưu gì cho dual-screen session?

**Mục đích:** kiểm tra state persistence.

**Ý chính:** session, transaction, confirmation, audit, status, timestamps, correlationId.

**Tránh:** chỉ lưu transaction, không lưu session/confirmation state.

### Q5. Customer screen chỉ nên thấy dữ liệu gì?

**Mục đích:** kiểm tra security/privacy.

**Ý chính:** chỉ hiển thị thông tin cần customer review/confirm; mask sensitive/internal fields; không expose teller/internal notes.

**Tránh:** push full transaction object sang customer screen.

## Hard

### Q6. WebSocket disconnect giữa giao dịch thì recovery thế nào?

**Mục đích:** kiểm tra fault tolerance.

**Ý chính:** reconnect, validate session, fetch latest state, timeout, audit, cancel if expired.

**Tránh:** mất session hoặc bắt đầu lại từ đầu gây duplicate.

### Q7. Làm sao scale WebSocket trên nhiều pod OpenShift?

**Mục đích:** kiểm tra production architecture.

**Ý chính:** sticky session hoặc shared messaging, distributed state, graceful shutdown, active connection metrics.

**Tránh:** in-memory only state.

### Q8. Làm sao đảm bảo Payment/Remittance không bị process trùng?

**Mục đích:** kiểm tra idempotency/transaction.

**Ý chính:** idempotency key, unique constraint, atomic state transition, DB transaction, audit, duplicate returns same result.

**Tránh:** chỉ disable button ở frontend.

### Q9. Nếu customer confirmation đến sau khi session timeout thì xử lý thế nào?

**Mục đích:** kiểm tra state machine correctness.

**Ý chính:** backend check current state, reject stale confirmation, notify screens, audit action.

**Tránh:** xử lý confirmation chỉ vì message đến hợp lệ về format.

### Q10. Nếu exchange rate trong Remittance thay đổi trước khi customer confirm thì sao?

**Mục đích:** kiểm tra business consistency.

**Ý chính:** rate quote có expiry/version, confirmation phải match quote version, nếu expired thì refresh và yêu cầu confirm lại.

**Tránh:** dùng rate cũ vô thời hạn hoặc tự động apply rate mới không customer consent.

## 11. Câu trả lời mẫu cho interview questions

### Q1. Dual-screen system là gì?

**Trả lời:** Đây là hệ thống giao dịch tại quầy nơi teller và customer dùng hai màn hình riêng nhưng được đồng bộ trong cùng một session. Teller thao tác nghiệp vụ, customer xem hoặc xác nhận thông tin trên customer screen, backend đồng bộ trạng thái qua WebSocket và lưu audit cho các action quan trọng.

### Q2. Vì sao dùng WebSocket?

**Trả lời:** Vì flow teller-customer cần real-time, hai chiều và latency thấp. Nếu dùng REST polling, customer screen phải liên tục hỏi server, vừa chậm vừa tạo request thừa. WebSocket phù hợp hơn nhưng cần xử lý reconnect, timeout, scaling và security.

### Q3. Thiết kế message flow khi teller yêu cầu customer xác nhận payment.

**Trả lời:** Teller nhập thông tin payment, backend validate và tạo confirmation request với sessionId/transactionId. Backend push message `CUSTOMER_CONFIRM_REQUIRED` sang customer screen. Customer confirm/reject, backend atomic update trạng thái, ghi audit, process payment nếu hợp lệ và push kết quả về teller screen.

### Q4. Database cần lưu gì cho dual-screen session?

**Trả lời:** Cần lưu `dual_screen_session` cho session/counter/teller/customer/status, `transaction` cho nghiệp vụ hiện tại, `customer_confirmation` cho trạng thái xác nhận/idempotency, và `audit_log` cho action của teller/customer/system. Mỗi record nên có timestamp, correlationId và status rõ ràng.

### Q5. Customer screen chỉ nên thấy dữ liệu gì?

**Trả lời:** Chỉ hiển thị dữ liệu customer cần xem/xác nhận như amount, fee, beneficiary, product summary hoặc terms cần consent. Không gửi full internal transaction object, internal notes, teller-only fields, sensitive account/customer data chưa mask.

### Q6. WebSocket disconnect giữa giao dịch thì recovery thế nào?

**Trả lời:** Client reconnect bằng session token/sessionId, backend validate session còn active và trả latest state từ DB/state store. Nếu session đã timeout thì reject confirmation và notify hai màn hình. Disconnect/reconnect quan trọng nên được log/audit.

### Q7. Làm sao scale WebSocket trên nhiều pod OpenShift?

**Trả lời:** WebSocket là long-lived connection nên cần sticky session hoặc shared messaging layer như Redis pub/sub/Kafka để route message cross-pod. Session state không lưu only in-memory mà lưu DB/Redis. Cần graceful shutdown khi rolling deploy, readiness/liveness probes và metrics active connections/disconnect rate.

### Q8. Làm sao đảm bảo Payment/Remittance không bị process trùng?

**Trả lời:** Dùng idempotency key/messageId cho customer confirmation và transaction submission, unique constraint hoặc atomic state transition từ `WAITING_CONFIRMATION` sang `CONFIRMED/PROCESSING`. Duplicate request trả cùng result hoặc ignored. Không chỉ dựa vào disable button ở frontend.

### Q9. Nếu customer confirmation đến sau khi session timeout thì xử lý thế nào?

**Trả lời:** Backend check current state. Nếu session đã `TIMEOUT/CANCELLED`, confirmation bị reject như stale message, notify customer/teller và ghi audit. Không process chỉ vì message format hợp lệ.

### Q10. Nếu exchange rate trong Remittance thay đổi trước khi customer confirm thì sao?

**Trả lời:** Rate quote cần có version/expiry. Customer confirmation phải match quote version còn valid. Nếu quote expired hoặc rate thay đổi, backend yêu cầu refresh quote và customer confirm lại; không tự áp rate mới khi chưa có consent.

## 12. Answer pitch ngắn

> In the OCBC project, I worked on a dual-screen branch banking platform where bank tellers and customers interact at the counter through two synchronized screens. The teller uses the internal screen to initiate and manage transactions, while the customer screen displays information and captures confirmation. The two screens are synchronized through WebSocket, with the backend acting as the source of truth for session and transaction state. I contributed to five modules: Secure Inventory, Payment Insurance, Cheque Service, Account Closure, and Remittance. The key design concerns were real-time state synchronization, session management, reconnect handling, customer confirmation idempotency, transaction consistency, auditability, and secure data exposure on the customer screen.

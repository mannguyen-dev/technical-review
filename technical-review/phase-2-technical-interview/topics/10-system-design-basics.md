# System Design Basics — 9 Most Common Interview Questions for NAB Senior Java

## Priority: Medium

## How to use this file

- Học theo thứ tự Easy → Medium → Hard.
- Mỗi câu trả lời nên luyện bản 45–90 giây.
- Khi phù hợp, liên hệ với project thật: OCBC Bank, Cox Automotive, Zurich Insurance.

## Question Map

| Level | Question |
|---|---|
| Easy | How do you start a system design question? |
| Easy | What matters most in banking system design? |
| Easy | What is idempotency in API design? |
| Medium | How would you design a transaction history API? |
| Medium | How would you design a payment API? |
| Medium | How do you ensure auditability? |
| Hard | How would you design OCBC dual-screen teller flow? |
| Hard | How do you handle failure in distributed design? |
| Hard | How do you design for observability? |

---
## Questions and Suggested Answers

### Question 1 (Easy): How do you start a system design question?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Clarify requirements, users, scale, critical flows, non-functional needs, then design API, data model, components, and trade-offs.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 2 (Easy): What matters most in banking system design?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Data correctness, security, auditability, idempotency, reliability, observability, and compliance are often more important than just speed.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 3 (Easy): What is idempotency in API design?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Repeated same request should have same business result. Use idempotency key for payment/confirmation/create operations.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 4 (Medium): How would you design a transaction history API?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Define filters/pagination, secure user/account access, indexed query by account/date/id, keyset pagination, masking, audit, caching carefully.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 5 (Medium): How would you design a payment API?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Validate auth/authorization, request schema, idempotency key, transaction boundary, status model, audit log, async events, error handling.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 6 (Medium): How do you ensure auditability?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Persist actor, action, timestamp, business id, correlationId, before/after or metadata. Make audit append-only and searchable.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 7 (Hard): How would you design OCBC dual-screen teller flow?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Create sessionId/transactionId, pair teller/customer via WebSocket, backend source of truth, state machine, idempotent confirmation, timeout, audit, masking.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 8 (Hard): How do you handle failure in distributed design?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Timeout, retry/backoff, circuit breaker, queue async work, idempotency, reconciliation, monitoring, alerting, and clear status model.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 9 (Hard): How do you design for observability?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Structured logs, correlationId, metrics, traces, dashboards, alerts, business KPIs, error codes, and no sensitive data in logs.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

## Final quick checklist

- [ ] Tôi trả lời được 3 câu Easy không ngập ngừng.
- [ ] Tôi trả lời được 3 câu Medium có trade-off.
- [ ] Tôi trả lời được 3 câu Hard có production/banking mindset.
- [ ] Tôi có ít nhất 1 ví dụ từ OCBC/Cox/Zurich cho topic này.

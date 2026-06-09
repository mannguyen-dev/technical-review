# Authentication / Authorization — 9 Most Common Interview Questions for NAB Senior Java

## Priority: High

## How to use this file

- Học theo thứ tự Easy → Medium → Hard.
- Mỗi câu trả lời nên luyện bản 45–90 giây.
- Khi phù hợp, liên hệ với project thật: OCBC Bank, Cox Automotive, Zurich Insurance.

## Question Map

| Level | Question |
|---|---|
| Easy | Authentication vs authorization? |
| Easy | What is JWT? |
| Easy | Access token vs refresh token? |
| Medium | Session-based auth vs JWT? |
| Medium | What is RBAC? |
| Medium | How do you secure REST APIs? |
| Hard | How do you revoke JWT? |
| Hard | How would you design authorization for teller/customer dual-screen flow? |
| Hard | What security checks should be applied to JWT claims? |

---
## Questions and Suggested Answers

### Question 1 (Easy): Authentication vs authorization?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Authentication verifies identity; authorization checks permissions. 401 means unauthenticated, 403 means authenticated but forbidden.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 2 (Easy): What is JWT?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
A signed token with header, payload, signature. Backend validates signature, expiry, issuer, audience, roles/scopes.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 3 (Easy): Access token vs refresh token?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Access token is short-lived for API access. Refresh token is longer-lived and used to obtain new access tokens; it must be protected and revocable.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 4 (Medium): Session-based auth vs JWT?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Session stores state on server; JWT is stateless token. JWT scales well for APIs but revocation and sensitive claims need care.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 5 (Medium): What is RBAC?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Role-Based Access Control grants permissions based on roles such as admin, teller, manager. For banking, also consider fine-grained permissions/scopes.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 6 (Medium): How do you secure REST APIs?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
HTTPS, token validation, authorization checks, input validation, rate limiting, secure error handling, audit logging, no sensitive logs.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 7 (Hard): How do you revoke JWT?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Use short-lived access tokens, refresh token revocation, token blacklist, token version, or introspection depending on architecture.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 8 (Hard): How would you design authorization for teller/customer dual-screen flow?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Bind sessionId to authenticated teller/customer screen, validate role/session ownership, expose customer-safe DTO only, audit all confirmations.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 9 (Hard): What security checks should be applied to JWT claims?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Validate signature, exp, nbf, issuer, audience, subject, scopes/roles, algorithm, key rotation, and reject unsigned/invalid tokens.
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

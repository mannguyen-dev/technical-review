# Caching / Redis — 9 Most Common Interview Questions for NAB Senior Java

## Priority: Medium-High

## How to use this file

- Học theo thứ tự Easy → Medium → Hard.
- Mỗi câu trả lời nên luyện bản 45–90 giây.
- Khi phù hợp, liên hệ với project thật: OCBC Bank, Cox Automotive, Zurich Insurance.

## Question Map

| Level | Question |
|---|---|
| Easy | What is caching and why use it? |
| Easy | What is cache-aside pattern? |
| Easy | What is TTL? |
| Medium | How do you invalidate cache? |
| Medium | What are common Redis use cases? |
| Medium | What is cache stampede? |
| Hard | How do you handle cache consistency in banking? |
| Hard | Write-through vs write-behind? |
| Hard | How would you cache transaction history? |

---
## Questions and Suggested Answers

### Question 1 (Easy): What is caching and why use it?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Caching stores frequently accessed data closer to the application to reduce latency and database load. Use carefully with consistency and security.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 2 (Easy): What is cache-aside pattern?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
App checks cache first; on miss loads DB and writes cache with TTL. On write, update DB then invalidate/update cache.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 3 (Easy): What is TTL?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Time-to-live defines how long a cache entry remains valid. It reduces stale data and limits memory usage.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 4 (Medium): How do you invalidate cache?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Invalidate on write, use TTL, versioned keys, or event-based invalidation. Choice depends on consistency requirement and write frequency.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 5 (Medium): What are common Redis use cases?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Reference data caching, session/token blacklist, rate limiting, idempotency key, distributed lock with caution, counters, queues in simple cases.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 6 (Medium): What is cache stampede?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Many requests hit DB simultaneously when a hot key expires. Mitigate with locking, early refresh, jittered TTL, request coalescing.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 7 (Hard): How do you handle cache consistency in banking?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Avoid caching sensitive/highly transactional data unless policy allows. Use short TTL, invalidation, audit-aware design, and DB as source of truth.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 8 (Hard): Write-through vs write-behind?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Write-through writes cache and DB synchronously, stronger consistency but slower. Write-behind writes DB async, faster but riskier if cache/process fails.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 9 (Hard): How would you cache transaction history?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Usually avoid caching raw sensitive transaction data broadly. Cache query metadata/reference data; if caching, use user-scoped keys, short TTL, encryption/policy, invalidation on new transaction.
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

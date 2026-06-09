# Core Java — 9 Most Common Interview Questions for NAB Senior Java

## Priority: High

## How to use this file

- Học theo thứ tự Easy → Medium → Hard.
- Mỗi câu trả lời nên luyện bản 45–90 giây.
- Khi phù hợp, liên hệ với project thật: OCBC Bank, Cox Automotive, Zurich Insurance.

## Question Map

| Level | Question |
|---|---|
| Easy | What are the four OOP principles? |
| Easy | What is the difference between interface and abstract class? |
| Easy | What is the difference between checked and unchecked exceptions? |
| Medium | Why do we need to override equals() and hashCode() together? |
| Medium | How does HashMap work internally? |
| Medium | What is immutability and why is it useful? |
| Hard | How do you handle race condition in Java? |
| Hard | What is the difference between volatile and synchronized? |
| Hard | How would you troubleshoot a memory leak or GC issue? |

---
## Questions and Suggested Answers

### Question 1 (Easy): What are the four OOP principles?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Encapsulation, abstraction, inheritance, polymorphism. In backend work, I use them to keep domain logic readable, hide implementation details, and make code easier to extend and test.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 2 (Easy): What is the difference between interface and abstract class?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
An interface defines a contract, while an abstract class can share common state and behavior. I prefer interfaces for service contracts and use abstract classes only when there is real shared logic.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 3 (Easy): What is the difference between checked and unchecked exceptions?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Checked exceptions are compile-time checked; unchecked exceptions extend RuntimeException. In Spring Boot, I often use custom unchecked business exceptions and map them with @ControllerAdvice.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 4 (Medium): Why do we need to override equals() and hashCode() together?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
HashMap uses hashCode to find a bucket and equals to compare keys. If two objects are equal, they must have the same hashCode; otherwise hash-based collections behave incorrectly.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 5 (Medium): How does HashMap work internally?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
HashMap hashes the key, finds a bucket, then compares keys using equals. Collisions are handled in the bucket; in modern Java, long collision chains can become tree bins.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 6 (Medium): What is immutability and why is it useful?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Immutable objects cannot change after construction. Use final fields, no setters, and defensive copies. They are easier to reason about and safer in concurrent code.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 7 (Hard): How do you handle race condition in Java?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Identify shared mutable state, then protect it using synchronization, locks, atomic classes, concurrent collections, or immutability. Also keep critical sections small and test concurrent behavior.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 8 (Hard): What is the difference between volatile and synchronized?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
volatile guarantees visibility but not atomicity for compound operations. synchronized provides mutual exclusion and visibility. Use volatile for simple flags, synchronized/locks for critical sections.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 9 (Hard): How would you troubleshoot a memory leak or GC issue?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Check heap usage, GC logs, thread dumps/heap dumps, object retention paths, caches, static references, and unclosed resources. Then reproduce, fix, and monitor memory after deployment.
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

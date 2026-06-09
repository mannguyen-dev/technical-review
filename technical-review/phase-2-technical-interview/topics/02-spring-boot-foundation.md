# Spring Boot Foundation — 9 Most Common Interview Questions for NAB Senior Java

## Priority: High

## How to use this file

- Học theo thứ tự Easy → Medium → Hard.
- Mỗi câu trả lời nên luyện bản 45–90 giây.
- Khi phù hợp, liên hệ với project thật: OCBC Bank, Cox Automotive, Zurich Insurance.

## Question Map

| Level | Question |
|---|---|
| Easy | What is Dependency Injection? |
| Easy | What is the difference between @Component, @Service, and @Repository? |
| Easy | Why is constructor injection preferred? |
| Medium | How does @Transactional work? |
| Medium | How do you handle validation in Spring Boot REST APIs? |
| Medium | How do you handle exceptions globally? |
| Hard | Why might @Transactional not work as expected? |
| Hard | How does Spring Boot auto-configuration work at a high level? |
| Hard | How would you design a clean REST controller/service/repository flow? |

---
## Questions and Suggested Answers

### Question 1 (Easy): What is Dependency Injection?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Dependency Injection means dependencies are provided from outside by Spring instead of created manually. It reduces coupling and improves testability.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 2 (Easy): What is the difference between @Component, @Service, and @Repository?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
All register beans. @Service marks business logic, @Repository marks persistence and exception translation, @Component is generic.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 3 (Easy): Why is constructor injection preferred?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
It makes dependencies explicit, supports immutability, avoids partially initialized objects, and makes unit testing easier.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 4 (Medium): How does @Transactional work?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Spring uses a proxy to open, commit, or rollback a transaction around a method. By default it rolls back on unchecked exceptions. Service layer is usually the right boundary.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 5 (Medium): How do you handle validation in Spring Boot REST APIs?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Use DTOs with Bean Validation annotations and @Valid at controller boundary. Business validation belongs in service/domain layer.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 6 (Medium): How do you handle exceptions globally?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Use @ControllerAdvice and @ExceptionHandler to map exceptions into consistent error responses and proper HTTP status codes.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 7 (Hard): Why might @Transactional not work as expected?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Common causes are self-invocation, private methods, method not called through Spring proxy, wrong propagation, checked exception rollback not configured, or async boundary.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 8 (Hard): How does Spring Boot auto-configuration work at a high level?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Spring Boot inspects classpath, properties, and conditions to configure beans automatically. We can override by defining our own beans or configuration.
```

#### NAB / Banking angle
Với NAB, luôn nhấn mạnh reliability, security, auditability, data correctness, idempotency hoặc production support nếu topic liên quan.

#### Common follow-up questions
- Can you give an example from your project?
- What are the trade-offs?
- How would you handle this in production?

---

### Question 9 (Hard): How would you design a clean REST controller/service/repository flow?

#### What the interviewer wants to check
Khả năng hiểu concept, giải thích practical trade-off và áp dụng vào Java backend/banking system.

#### Key points to remember
- Trả lời ngắn gọn định nghĩa trước.
- Nêu trade-off hoặc production concern.
- Liên hệ project thật nếu phù hợp.

#### Sample short answer

```text
Controller handles DTO/validation, service handles business transaction, repository handles persistence, mapper converts DTO/entity, global handler manages errors.
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

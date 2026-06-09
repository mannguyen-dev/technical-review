# Phase 2: Technical Interview Preparation

## 1. Overview

Phase 2 là phần trọng tâm nhất trong buổi phỏng vấn **Senior Java Developer / Backend Engineer tại NAB**. Interviewer thường dùng phase này để kiểm tra:

- Nền tảng **Core Java** có chắc không.
- Có hiểu **Spring Boot backend foundation** hay chỉ biết dùng framework theo pattern có sẵn.
- Có kinh nghiệm thực tế với **microservices, REST API, Kafka/message queue, database, caching, security** không.
- Có tư duy **production-ready**: reliability, idempotency, transaction consistency, monitoring, error handling.
- Có thể giải thích trade-off ở level Senior không.

Chiến lược học:

1. Ưu tiên **Core Java + Spring Boot + Microservices + Database + Security**.
2. Luôn trả lời theo hướng practical, gắn với project OCBC Bank, Zurich Insurance, Cox Automotive nếu phù hợp.
3. Với câu hỏi technical, dùng công thức:

```text
Definition → Practical Usage → Trade-off → Example from project
```

Ví dụ mindset Senior:

```text
I would not only implement the happy path. I would also consider timeout, retry, idempotency, logging, monitoring, and how the system behaves in production.
```

---

## 2. Topic Priority Map

| Topic | Priority | Why it matters |
|---|---|---|
| Core Java | High | Nền tảng bắt buộc cho Senior Java; dễ bị hỏi sâu về collections, equals/hashCode, exception, concurrency |
| Spring Boot Foundation | High | Role Java backend chắc chắn hỏi DI, bean, REST, transaction, validation, exception handling |
| Microservices | High | NAB digital banking thường dùng distributed systems; cần hiểu communication, resilience, idempotency, consistency |
| Database / SQL Optimization | High | Banking backend phụ thuộc data correctness, transaction, index, query performance |
| Authentication / Authorization | High | Banking APIs yêu cầu security; cần hiểu JWT/OAuth2/RBAC/401 vs 403 |
| Design Patterns | Medium-High | Senior cần biết áp dụng pattern thực tế trong backend, không chỉ định nghĩa |
| Caching / Redis | Medium-High | Hay hỏi performance và consistency trade-off |
| Network & Security Basics | Medium | HTTP/HTTPS/CORS/OWASP là kiến thức backend cơ bản |
| Testing | Medium | Không phải trọng số cao nhất nhưng cần mindset tốt về unit/integration/E2E |
| System Design Basics | Medium | Có thể hỏi high-level design cho banking flow |
| CI/CD / Docker / DevOps | Low-Medium | Có thể hỏi thêm, nhất là vì CV có Jenkins/OpenShift/AWS/Docker |
| Web General Knowledge | Low | Chủ yếu hỏi để kiểm tra hiểu biết tổng quan |

---

## 3. Lý thuyết cần nhớ

### 3.1 Core Java

#### Priority

**High**

#### Key Concepts

- OOP principles: Encapsulation, Inheritance, Polymorphism, Abstraction.
- SOLID principles.
- Collections: List, Set, Map, ArrayList, LinkedList, HashMap, ConcurrentHashMap.
- `equals()` / `hashCode()` contract.
- Exception handling: checked vs unchecked, custom exception, global handling.
- Stream API: map/filter/flatMap/reduce/collect/lazy evaluation.
- Immutability: final class, final fields, defensive copy.
- Multithreading: Thread, Runnable, Callable, ExecutorService, CompletableFuture.
- Concurrency: synchronized, volatile, locks, race condition, deadlock.
- JVM basics: heap, stack, GC, memory leak basics.

#### Must-remember Notes

- `HashMap` uses `hashCode()` to find bucket and `equals()` to compare keys.
- If two objects are equal, they must have the same hashCode.
- Prefer immutable objects for thread-safety and predictable behavior.
- Use `ExecutorService` instead of manually creating many threads.
- Parallel stream is not always faster; avoid it for IO-heavy or shared mutable state.
- Checked exception = recoverable/forced handling; unchecked exception = programming/runtime/business errors.

#### Common Mistakes

- Nói HashMap chỉ là key-value mà không giải thích collision.
- Override `equals()` nhưng quên `hashCode()`.
- Dùng `parallelStream()` như performance magic.
- Catch `Exception` quá rộng rồi swallow error.
- Không phân biệt thread-safety và immutability.

---

### 3.2 Spring Boot Foundation

#### Priority

**High**

#### Key Concepts

- IoC / Dependency Injection.
- Bean lifecycle.
- `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`.
- Constructor injection vs field injection.
- Spring MVC / REST API.
- Request validation with `@Valid`, `@NotNull`, `@NotBlank`.
- Global exception handling using `@ControllerAdvice`.
- Transaction management with `@Transactional`.
- Configuration: `application.yml`, profiles, `@ConfigurationProperties`.
- Spring Security basics.

#### Must-remember Notes

- Constructor injection is preferred because dependencies are explicit, immutable, and easier to test.
- Transaction boundary should usually be at service layer.
- `@Transactional` rollback default is for unchecked exceptions.
- DTO validation should happen at API boundary, business validation in service/domain layer.
- Global exception handler should return consistent error response.

#### Common Mistakes

- Đặt `@Transactional` ở private method hoặc self-invocation rồi nghĩ transaction vẫn hoạt động.
- Dùng entity trực tiếp làm request/response DTO.
- Không chuẩn hóa error response.
- Field injection quá nhiều làm code khó test.

---

### 3.3 Microservices

#### Priority

**High**

#### Key Concepts

- Service decomposition by business capability.
- API Gateway.
- Synchronous communication: REST/gRPC.
- Asynchronous communication: Kafka/SQS/EventBridge.
- Resilience: timeout, retry, circuit breaker, bulkhead.
- Idempotency.
- Event-driven architecture.
- Distributed transaction problem.
- Saga pattern.
- Eventual consistency.
- Observability: logs, metrics, tracing, correlation ID.

#### Must-remember Notes

- Microservices solve team/domain scalability but introduce operational complexity.
- Do not retry blindly; retry only transient errors and combine with timeout/backoff.
- In banking/payment flows, idempotency is critical to avoid duplicate transactions.
- End-to-end exactly-once is hard; often use at-least-once + idempotent consumer.
- Use outbox pattern to reliably publish events after DB changes.

#### Common Mistakes

- Nói microservices luôn tốt hơn monolith.
- Không nói trade-off về data consistency, latency, monitoring.
- Retry without timeout gây retry storm.
- Không có idempotency key cho critical operations.

---

### 3.4 Design Patterns

#### Priority

**Medium-High**

#### Key Concepts

- Singleton: Spring beans are singleton by default.
- Factory: create/select implementation based on type.
- Strategy: replace complex if-else business rules.
- Template Method: define common workflow with custom steps.
- Builder: build complex immutable objects/DTOs.
- Adapter: wrap external APIs/providers.
- Observer / Publisher-Subscriber: event-driven communication.
- Repository: abstract persistence.
- Circuit Breaker: resilience pattern.

#### Must-remember Notes

- Interviewer thích nghe project example hơn definition.
- Strategy rất phù hợp với business rules: payment type, notification channel, validation rule.
- Adapter phù hợp khi integrate external provider.
- Publisher-Subscriber liên quan Kafka/EventBridge.

#### Common Mistakes

- Chỉ đọc định nghĩa GoF mà không có ví dụ.
- Over-engineer pattern cho simple logic.
- Nhầm Strategy với Factory.

---

### 3.5 Database / SQL Optimization

#### Priority

**High**

#### Key Concepts

- Index: B-tree, composite index, selectivity.
- Query optimization: execution plan, avoid `SELECT *`, proper WHERE clause.
- Transaction ACID.
- Isolation levels: Read Committed, Repeatable Read, Serializable.
- Locking: optimistic vs pessimistic.
- Pagination: offset vs keyset pagination.
- N+1 query problem.
- Connection pool: HikariCP basics.
- Database design: normalization, constraints, indexes, audit table.

#### Must-remember Notes

- Index improves read but slows writes and consumes storage.
- Composite index order matters.
- Avoid function on indexed column in WHERE clause if it prevents index usage.
- For large data, keyset pagination is often better than large OFFSET.
- Optimistic locking uses version field; pessimistic locking locks rows.

#### Common Mistakes

- Nói “add index” cho mọi slow query.
- Không check execution plan.
- Không hiểu N+1 trong JPA/Hibernate.
- Không biết transaction isolation impact.

---

### 3.6 Caching

#### Priority

**Medium-High**

#### Key Concepts

- Cache-aside.
- Write-through.
- Write-behind.
- TTL.
- Cache invalidation.
- Redis use cases.
- Cache consistency.
- Cache stampede.

#### Must-remember Notes

- Cache-aside: app checks cache first, loads DB on miss, then stores in cache.
- Cache invalidation is usually the hardest part.
- Use TTL to limit stale data.
- Avoid caching sensitive banking data unless policy allows.
- Redis use cases: reference data, session, rate limiting, distributed lock with caution, idempotency keys.

#### Common Mistakes

- Cache everything.
- Không có invalidation strategy.
- Cache PII/token/raw sensitive data không kiểm soát.
- Không xử lý cache stampede.

---

### 3.7 Network & Security Basics

#### Priority

**Medium**

#### Key Concepts

- HTTP vs HTTPS.
- REST principles.
- HTTP methods and status codes.
- TLS basics.
- CORS.
- Rate limiting.
- OWASP basics: SQL Injection, XSS, CSRF, Broken Authentication, Sensitive Data Exposure.

#### Must-remember Notes

- HTTPS = HTTP over TLS; protects confidentiality and integrity in transit.
- CORS is browser security policy, not backend authentication.
- Use parameterized queries to prevent SQL injection.
- Never log passwords, tokens, full card numbers, or sensitive PII.
- Use rate limiting to protect APIs from abuse.

#### Common Mistakes

- Nhầm 401 và 403.
- Nghĩ CORS là security mechanism cho backend API authorization.
- Log sensitive data để debug.

---

### 3.8 Authentication / Authorization

#### Priority

**High**

#### Key Concepts

- Authentication vs Authorization.
- Session-based auth vs JWT.
- JWT: header, payload, signature.
- Access token vs refresh token.
- OAuth2 basics.
- Role-Based Access Control.
- Scope/permission-based access control.
- Token expiration and revocation.
- Secure API design.

#### Must-remember Notes

- Authentication = who you are; Authorization = what you can do.
- 401 = unauthenticated; 403 = authenticated but not allowed.
- JWT should be validated for signature, expiry, issuer, audience, scopes/roles.
- Access token should be short-lived; refresh token should be protected and revocable.
- Banking systems require least privilege, audit logs, secure transport, and sensitive data protection.

#### Common Mistakes

- Store sensitive data in JWT payload.
- Long-lived access token with no revocation strategy.
- Only check authentication but not authorization.

---

### 3.9 Testing

#### Priority

**Medium**

#### Key Concepts

- Unit test.
- Integration test.
- Mocking with Mockito.
- Contract test.
- E2E test mindset.
- Test pyramid.
- Spring Boot testing: `@SpringBootTest`, `@WebMvcTest`, `@DataJpaTest`.

#### Must-remember Notes

- Unit test verifies business logic in isolation.
- Integration test verifies wiring and real integration behavior.
- Mock external services in unit tests; use Testcontainers/WireMock if available for integration.
- E2E tests are valuable but expensive and slower.
- Code coverage number is less important than meaningful test cases.

#### Common Mistakes

- Mock everything and test nothing real.
- Only test happy path.
- Confuse unit test with integration test.

---

### 3.10 Lower-weight Topics

#### Priority

**Low-Medium**

#### Key Concepts

- System design basics: requirements, API, data model, consistency, scalability, security, observability.
- CI/CD: build, test, scan, package, deploy, smoke test, rollback.
- Docker: image, container, Dockerfile, environment variables.
- Logging/Monitoring: structured logs, correlation ID, metrics, alerts.
- Web general knowledge: browser request flow, DNS, HTTP headers, cookies.

#### Must-remember Notes

- For system design in banking, emphasize correctness, auditability, idempotency, security.
- CI/CD helps reduce manual deployment errors.
- Docker image is a package; container is a running instance.
- Logs should be useful but must not expose sensitive data.

---

## 4. Câu hỏi và câu trả lời mẫu

## 4.1 Core Java Questions

### Question 1: What is the difference between ArrayList and LinkedList?

#### What the interviewer wants to check

Interviewer muốn kiểm tra hiểu biết về Collections, internal structure và trade-off khi chọn data structure.

#### Key points to remember

- ArrayList uses dynamic array.
- LinkedList uses doubly linked list.
- ArrayList fast random access; LinkedList better for frequent insert/delete when node reference is known.
- In practice, ArrayList is used more often due to memory locality and simpler access pattern.

#### Easy-to-remember answer structure

```text
Internal Structure → Access → Insert/Delete → Practical Choice
```

#### Sample short answer

```text
ArrayList is backed by a dynamic array, so random access by index is fast, usually O(1). LinkedList is backed by nodes, so random access is O(n), but insertion or deletion can be efficient if we already have the node reference. In most backend applications, I use ArrayList more often because it is simpler and usually performs better for iteration and read-heavy operations.
```

#### Sample full answer

```text
ArrayList and LinkedList are both implementations of the List interface, but they have different internal structures.

ArrayList is backed by a dynamic array. It provides fast random access by index, usually O(1). Adding to the end is usually efficient, but inserting or removing in the middle can be O(n) because elements may need to be shifted.

LinkedList is backed by a doubly linked list. Random access is O(n) because it needs to traverse nodes. Insert and delete can be efficient if we already have the node reference, but in many real cases we still need to search first.

In backend projects, I usually prefer ArrayList unless I have a very specific reason to use LinkedList. ArrayList has better memory locality and is generally faster for typical read and iteration-heavy use cases.
```

#### Common follow-up questions

- When would you use LinkedList?
- What is the time complexity of add/remove/get?

#### Vietnamese explanation

ArrayList thường là lựa chọn default trong backend. LinkedList nghe có vẻ insert/delete tốt nhưng thực tế phải tìm node trước, lại tốn memory do lưu pointer.

---

### Question 2: Why do we need to override both equals() and hashCode()?

#### What the interviewer wants to check

Kiểm tra hiểu biết về object equality và HashMap/HashSet behavior.

#### Key points to remember

- `equals()` defines logical equality.
- `hashCode()` determines bucket in hash-based collections.
- Equal objects must have same hashCode.
- If contract is broken, HashMap/HashSet may behave incorrectly.

#### Easy-to-remember answer structure

```text
equals Purpose → hashCode Purpose → Contract → Collection Impact
```

#### Sample short answer

```text
We override both because hash-based collections such as HashMap and HashSet use hashCode to find the bucket and equals to compare objects inside the bucket. If two objects are equal according to equals, they must have the same hashCode. Otherwise, the collection may not be able to find the object correctly.
```

#### Sample full answer

```text
`equals()` is used to define logical equality between two objects, while `hashCode()` is used by hash-based collections to decide where an object should be stored.

For example, HashMap first uses the key's hashCode to find the bucket. Then it uses equals to compare keys inside that bucket. The important contract is: if two objects are equal according to equals, they must return the same hashCode.

If we override equals but not hashCode, two logically equal objects may have different hash codes and end up in different buckets. Then HashMap or HashSet may fail to find duplicates or retrieve values correctly.

In backend applications, this is important when we use domain objects or DTOs as keys in maps or store them in sets.
```

#### Common follow-up questions

- Can two different objects have the same hashCode?
- What happens during HashMap collision?

#### Vietnamese explanation

Đây là câu rất hay hỏi. Nhớ câu: `hashCode finds bucket, equals compares inside bucket`.

---

### Question 3: What is the difference between checked and unchecked exceptions?

#### What the interviewer wants to check

Kiểm tra exception handling strategy và best practice trong backend.

#### Key points to remember

- Checked exception must be declared or caught.
- Unchecked exception extends RuntimeException.
- Business/runtime errors often use unchecked custom exceptions in Spring.
- Use global exception handler for consistent API response.

#### Easy-to-remember answer structure

```text
Definition → Use Case → Spring Practice → Example
```

#### Sample short answer

```text
Checked exceptions are checked at compile time and must be caught or declared. Unchecked exceptions are RuntimeExceptions and do not require explicit handling. In Spring Boot backend, I usually use custom unchecked exceptions for business errors and map them to proper HTTP responses using @ControllerAdvice.
```

#### Sample full answer

```text
Checked exceptions are exceptions that the compiler forces us to handle or declare, for example IOException. They are useful when the caller can reasonably recover from the error.

Unchecked exceptions extend RuntimeException. They usually represent programming errors, invalid state, or business rule failures that we do not want to force every method to declare.

In Spring Boot applications, I usually define custom unchecked exceptions for business errors, such as ResourceNotFoundException or InvalidTransactionException. Then I use a global exception handler with @ControllerAdvice to map those exceptions to consistent HTTP responses.

The important point is not just checked or unchecked, but having a clear exception strategy, not swallowing errors, and returning meaningful error responses to API clients.
```

#### Common follow-up questions

- When would you create a custom exception?
- How do you handle exceptions globally in Spring Boot?

#### Vietnamese explanation

Trong backend Spring, custom unchecked exception + `@ControllerAdvice` là pattern phổ biến.

---

### Question 4: What is immutability and why is it useful?

#### What the interviewer wants to check

Kiểm tra hiểu biết về object design, thread-safety, defensive coding.

#### Key points to remember

- Immutable object state cannot change after creation.
- Use final class/fields, no setters, defensive copy.
- Easier to reason about, thread-safe if fields are immutable.
- Useful for value objects, DTOs, config.

#### Easy-to-remember answer structure

```text
Definition → How to Create → Benefits → Example
```

#### Sample short answer

```text
An immutable object cannot be changed after it is created. In Java, we can make a class immutable by using final fields, no setters, constructor initialization, and defensive copies for mutable fields. Immutability is useful because it makes objects easier to reason about and safer in multithreaded code.
```

#### Sample full answer

```text
Immutability means an object's state cannot be changed after construction.

To create an immutable class in Java, I would make fields private and final, initialize them through the constructor, avoid setters, and use defensive copies for mutable objects like Date or List. Sometimes the class itself can also be final to prevent subclasses from changing behavior.

The benefits are predictability, thread-safety, and easier debugging. If an object cannot change, we do not need to worry about another thread modifying it unexpectedly.

In backend systems, immutability is useful for value objects, request snapshots, configuration objects, and domain values where consistency is important.
```

#### Common follow-up questions

- Is String immutable?
- How do you handle mutable fields inside immutable classes?

#### Vietnamese explanation

Immutability không chỉ là `final`; phải chú ý mutable fields và defensive copy.

---

### Question 5: What is the difference between Runnable and Callable?

#### What the interviewer wants to check

Kiểm tra Java concurrency basics.

#### Key points to remember

- Runnable returns nothing and cannot throw checked exception.
- Callable returns a value and can throw checked exception.
- Callable is used with Future/ExecutorService.

#### Easy-to-remember answer structure

```text
Return Value → Exception → Usage
```

#### Sample short answer

```text
Runnable represents a task that does not return a result and cannot throw checked exceptions directly. Callable can return a result and can throw checked exceptions. Callable is usually submitted to an ExecutorService and returns a Future.
```

#### Sample full answer

```text
Runnable and Callable are both used to represent tasks that can run asynchronously.

Runnable has a `run()` method, returns no result, and cannot throw checked exceptions directly. Callable has a `call()` method, returns a value, and can throw checked exceptions.

In practice, if I only need to run a background task, Runnable is enough. If I need a result or need to handle exceptions more explicitly, I use Callable with ExecutorService, which returns a Future.

In backend systems, this is useful for parallelizing independent operations, but we also need to control thread pool size, timeout, error handling, and monitoring.
```

#### Common follow-up questions

- What is Future?
- What is CompletableFuture?

#### Vietnamese explanation

Senior answer nên thêm thread pool, timeout, error handling chứ không chỉ định nghĩa.

---

## 4.2 Spring Boot Questions

### Question 1: What is Dependency Injection and why is it useful?

#### What the interviewer wants to check

Kiểm tra Spring foundation và design/testability mindset.

#### Key points to remember

- DI means dependencies are provided by container, not created manually.
- Promotes loose coupling.
- Easier testing and configuration.
- Constructor injection preferred.

#### Easy-to-remember answer structure

```text
Definition → Benefits → Spring Example → Best Practice
```

#### Sample short answer

```text
Dependency Injection means an object receives its dependencies from outside, usually from the Spring container, instead of creating them directly. It reduces coupling, improves testability, and makes configuration easier. In Spring Boot, I prefer constructor injection because dependencies are explicit and immutable.
```

#### Sample full answer

```text
Dependency Injection is a design principle where a class does not create its dependencies directly. Instead, dependencies are provided from outside, usually by the Spring IoC container.

This improves loose coupling because the class depends on abstractions rather than concrete implementations. It also improves testability because we can inject mocks or test implementations during unit tests.

In Spring Boot, services, repositories, and clients are usually registered as beans, and Spring injects them where needed. I prefer constructor injection because it makes required dependencies explicit, supports immutability, and avoids partially initialized objects.

For example, a PaymentService can depend on a PaymentRepository and KafkaPublisher through constructor injection. During testing, I can inject mocked versions of those dependencies.
```

#### Common follow-up questions

- Constructor injection vs field injection?
- What is IoC container?

#### Vietnamese explanation

Nên nhấn mạnh testability, explicit dependencies, immutability.

---

### Question 2: How does @Transactional work in Spring?

#### What the interviewer wants to check

Kiểm tra transaction management, proxy mechanism và rollback behavior.

#### Key points to remember

- Spring uses proxy to manage transaction around method call.
- Usually placed at service layer.
- Default rollback for unchecked exceptions.
- Self-invocation/private method issue.

#### Easy-to-remember answer structure

```text
Purpose → Proxy Mechanism → Rollback → Best Practice/Caveats
```

#### Sample short answer

```text
@Transactional defines a transaction boundary around a method. Spring usually uses a proxy to start a transaction before the method and commit or rollback after it. By default, it rolls back on unchecked exceptions. I usually put it at the service layer because that is where business transaction boundaries are defined.
```

#### Sample full answer

```text
`@Transactional` is used to define a transaction boundary in Spring.

When a transactional method is called through a Spring proxy, Spring opens a transaction before the method executes. If the method completes successfully, the transaction is committed. If an exception occurs, Spring decides whether to roll back based on the rollback rules. By default, rollback happens for unchecked exceptions, but we can configure rollback for checked exceptions as well.

I usually place `@Transactional` at the service layer because the service method typically represents a business operation, such as creating a payment or closing an account.

There are some caveats. Since Spring uses proxies, self-invocation may bypass the proxy, and private methods will not be transactional in the expected way. Also, we need to be careful when mixing transactions with async processing or external API calls.
```

#### Common follow-up questions

- What is propagation?
- What is isolation level?
- Why does self-invocation not work?

#### Vietnamese explanation

Câu này cực hay hỏi. Nhớ: proxy, service layer, rollback unchecked, self-invocation caveat.

---

### Question 3: How do you handle validation and exceptions in a REST API?

#### What the interviewer wants to check

Kiểm tra API quality, error handling consistency.

#### Key points to remember

- Bean validation at DTO boundary.
- Business validation in service/domain.
- Use `@ControllerAdvice` for global exception handling.
- Return consistent error response.

#### Easy-to-remember answer structure

```text
Request Validation → Business Validation → Exception Mapping → Error Response
```

#### Sample short answer

```text
I use Bean Validation annotations such as @NotNull and @NotBlank on request DTOs and trigger them with @Valid. For business rules, I validate in the service or domain layer and throw custom exceptions. Then I use @ControllerAdvice to map exceptions to consistent HTTP responses with proper status codes and error messages.
```

#### Sample full answer

```text
For REST API validation, I separate request validation and business validation.

At the API boundary, I use request DTOs with Bean Validation annotations such as @NotNull, @NotBlank, @Size, and trigger validation using @Valid in the controller. This handles basic input validation.

For business rules, I validate inside the service or domain layer. For example, in a banking flow, checking whether an account is eligible for closure should not be only a DTO validation; it belongs to business logic.

When validation fails, I throw custom exceptions such as BusinessValidationException or ResourceNotFoundException. Then I use @ControllerAdvice to map exceptions into consistent error responses with proper HTTP status codes like 400, 404, 409, or 422.

This approach keeps controllers clean and gives API clients predictable error responses.
```

#### Common follow-up questions

- 400 vs 422?
- How do you standardize error response?

#### Vietnamese explanation

Senior nên phân biệt request validation và business validation.

---

## 4.3 Microservices Questions

### Question 1: What are the advantages and disadvantages of microservices?

#### What the interviewer wants to check

Kiểm tra bạn có hiểu trade-off hay chỉ buzzword.

#### Key points to remember

- Advantages: independent deployment, scalability, ownership, domain separation.
- Disadvantages: distributed complexity, latency, data consistency, observability.
- Not always better than monolith.

#### Easy-to-remember answer structure

```text
Benefits → Costs → When to Use → Production Concerns
```

#### Sample short answer

```text
Microservices allow teams to split a system by business capability, deploy services independently, and scale specific parts of the system. However, they also introduce complexity such as network latency, distributed transactions, observability, and operational overhead. I would choose microservices when domain boundaries and team ownership justify the complexity.
```

#### Sample full answer

```text
Microservices can be useful when a system has clear business capabilities and different parts need independent deployment, scaling, or ownership. For example, in a banking platform, payment, remittance, account closure, and audit could be separate capabilities if their boundaries are clear.

The advantages are independent deployment, better team autonomy, technology flexibility, and scaling services independently.

The disadvantages are also significant. We need to handle network failures, latency, distributed tracing, data consistency, deployment complexity, and monitoring. A local transaction in a monolith can become a distributed consistency problem in microservices.

So I do not think microservices are always better. For small systems or unclear domains, a modular monolith may be simpler. I would choose microservices when the business domain, team structure, and operational maturity justify the complexity.
```

#### Common follow-up questions

- How do you define service boundary?
- How do you handle distributed transactions?

#### Vietnamese explanation

Nói microservices có trade-off sẽ nghe Senior hơn nhiều.

---

### Question 2: How do you handle failure when calling another service?

#### What the interviewer wants to check

Kiểm tra resilience pattern thực tế.

#### Key points to remember

- Timeout.
- Retry with backoff for transient errors.
- Circuit breaker.
- Fallback if appropriate.
- Logging/metrics/correlation ID.

#### Easy-to-remember answer structure

```text
Timeout → Retry Carefully → Circuit Breaker → Fallback → Observability
```

#### Sample short answer

```text
When calling another service, I always configure timeout first. For transient failures, I may use retry with backoff, but only when the operation is safe or idempotent. For repeated failures, I use circuit breaker to avoid overloading the downstream service. I also add proper logging, metrics, and correlation IDs for troubleshooting.
```

#### Sample full answer

```text
For service-to-service communication, I do not rely only on the happy path.

First, every remote call should have a timeout. Without timeout, threads can be blocked and the failure can cascade.

Second, retry should be used carefully. I would retry only transient errors, use limited retries with backoff, and make sure the operation is idempotent or safe to retry. For example, retrying a payment submission without idempotency can create duplicate transactions.

Third, if the downstream service keeps failing, a circuit breaker can stop sending requests temporarily and allow the service to recover. Depending on the business case, we may return fallback data, queue the request, or fail fast with a clear error.

Finally, observability is important. I would log correlation IDs, track latency/error rate, and create alerts for downstream failures.
```

#### Common follow-up questions

- What is circuit breaker?
- When should retry not be used?

#### Vietnamese explanation

Trong banking, retry phải gắn với idempotency. Đây là điểm rất quan trọng.

---

### Question 3: How do you handle duplicate events in an event-driven system?

#### What the interviewer wants to check

Kiểm tra Kafka/message queue experience và idempotency.

#### Key points to remember

- At-least-once delivery can create duplicates.
- Use eventId/idempotency key.
- Store processed event or business status.
- Commit offset after successful processing.

#### Easy-to-remember answer structure

```text
Duplicate Cause → Idempotency Key → Persistent State → Safe Side Effect
```

#### Sample short answer

```text
In event-driven systems, duplicate events can happen because most systems use at-least-once delivery. I handle this by using an eventId or idempotency key, storing processed state in a database, and making the consumer idempotent. The consumer should commit the offset only after processing succeeds.
```

#### Sample full answer

```text
Duplicate events are normal in many event-driven systems, especially when using at-least-once delivery.

To handle duplicates, each event should have a unique eventId or business idempotency key. The consumer checks whether that event or business operation has already been processed. This can be done using a processed_event table, unique constraint, DynamoDB conditional write, or business status table.

The side effect must also be idempotent. For example, if a notification event is retried, the user should not receive the same notification twice. If a banking event is retried, it should not create a duplicate transaction.

For Kafka, I would commit the offset only after successful processing. If processing fails, the message can be retried, and after repeated failures it can go to a dead-letter topic for investigation.

In practice, I prefer saying at-least-once delivery plus idempotent processing rather than claiming exactly-once end to end.
```

#### Common follow-up questions

- What is exactly-once in Kafka?
- How do you design idempotency key?

#### Vietnamese explanation

Liên hệ OCBC Kafka hoặc Cox SQS/DynamoDB conditional write rất tốt.

---

## 4.4 Design Pattern Questions

### Question 1: Which design patterns have you used in backend projects?

#### What the interviewer wants to check

Kiểm tra khả năng áp dụng pattern thực tế.

#### Key points to remember

- Strategy for business rules.
- Factory for selecting implementations.
- Adapter for external systems.
- Publisher-Subscriber for events.
- Repository in persistence layer.

#### Easy-to-remember answer structure

```text
Pattern → Use Case → Benefit → Project Example
```

#### Sample short answer

```text
I have used Strategy for different business rules, Factory to select implementations based on type, Adapter to wrap external service integrations, Repository for data access, and Publisher-Subscriber in event-driven systems using Kafka or AWS EventBridge. I try to use patterns only when they simplify the design, not just for over-engineering.
```

#### Sample full answer

```text
In backend projects, I have used several patterns in practical ways.

Strategy is useful when we have multiple business rules or processing flows. For example, different notification channels or banking modules can have different validation or processing strategies.

Factory is useful when we need to select an implementation based on a type, such as choosing a handler for Payment, Insurance, Cheque Service, Account Closure, or Remittance.

Adapter is useful when integrating with external systems. It hides provider-specific request and response formats behind a clean internal interface.

Publisher-Subscriber is used in event-driven architecture. In my projects, Kafka or EventBridge allowed producers and consumers to be decoupled.

Repository is used to abstract data access.

The key is to use patterns to improve maintainability and readability, not to make the code more complicated.
```

#### Common follow-up questions

- Strategy vs Factory?
- Give an example of Adapter pattern.

#### Vietnamese explanation

Nên lấy ví dụ từ OCBC modules hoặc Cox notification handlers.

---

## 4.5 Database / SQL Optimization Questions

### Question 1: How do you optimize a slow SQL query?

#### What the interviewer wants to check

Kiểm tra practical troubleshooting, không chỉ biết index.

#### Key points to remember

- Reproduce and measure.
- Check execution plan.
- Check indexes and WHERE clause.
- Avoid unnecessary joins/select all.
- Check N+1 and pagination.

#### Easy-to-remember answer structure

```text
Measure → Execution Plan → Index/Query Review → Application Layer → Verify
```

#### Sample short answer

```text
I would first reproduce and measure the query. Then I check the execution plan to see whether indexes are used, whether there is a full table scan, or expensive joins. I review the WHERE clause, selected columns, pagination, and indexes. I also check whether the issue is actually from application-level N+1 queries. After changes, I compare performance before and after.
```

#### Sample full answer

```text
When optimizing a slow SQL query, I start by measuring the actual problem instead of guessing.

First, I reproduce the query with realistic data volume and check execution time. Then I look at the execution plan to understand whether the database is using indexes, doing full table scans, sorting large datasets, or performing expensive joins.

Next, I review the query itself. I avoid SELECT * if only a few columns are needed, check whether the WHERE clause is selective, avoid functions on indexed columns if they prevent index usage, and verify join conditions.

Then I review indexes. A proper index can help, but too many indexes can slow down writes, so I choose indexes based on access patterns. For composite indexes, column order matters.

I also check the application layer. In JPA/Hibernate, N+1 queries can make the database look slow even when each individual query is simple.

Finally, after applying changes, I compare before and after performance and monitor production metrics.
```

#### Common follow-up questions

- What is execution plan?
- What is composite index?
- What is N+1 query?

#### Vietnamese explanation

Answer Senior phải có process: measure → plan → fix → verify.

---

### Question 2: What is the difference between optimistic and pessimistic locking?

#### What the interviewer wants to check

Kiểm tra transaction/concurrency trong database.

#### Key points to remember

- Optimistic locking assumes conflicts are rare.
- Uses version column.
- Pessimistic locking locks row to prevent concurrent update.
- Banking needs careful choice based on contention and consistency.

#### Easy-to-remember answer structure

```text
Optimistic → Pessimistic → Trade-off → Use Case
```

#### Sample short answer

```text
Optimistic locking assumes conflicts are rare and usually uses a version column. If two transactions update the same row, one will fail due to version mismatch. Pessimistic locking locks the row during the transaction to prevent others from modifying it. Optimistic locking gives better concurrency, while pessimistic locking is safer for high-contention critical updates but can reduce throughput.
```

#### Sample full answer

```text
Optimistic locking and pessimistic locking are two ways to handle concurrent updates.

Optimistic locking assumes conflicts are rare. We usually add a version column to the table. When updating a row, the update includes the expected version. If another transaction has already changed the row, the version will not match and the update fails. This is good for systems with low contention because it does not lock rows for a long time.

Pessimistic locking assumes conflicts may happen and locks the row when reading or updating it. Other transactions must wait. This can be useful for critical sections, but it can reduce throughput and may cause deadlock if not used carefully.

In banking systems, I would choose based on the business case. For many normal updates, optimistic locking is enough. For very sensitive high-contention operations, pessimistic locking or stronger transaction control may be needed.
```

#### Common follow-up questions

- How does JPA support optimistic locking?
- What can cause deadlock?

#### Vietnamese explanation

Đây là câu rất hợp banking vì liên quan consistency.

---

## 4.6 Caching Questions

### Question 1: What is cache-aside pattern?

#### What the interviewer wants to check

Kiểm tra caching pattern cơ bản và trade-off.

#### Key points to remember

- App checks cache first.
- On miss, load DB and populate cache.
- Use TTL.
- Need invalidation strategy.

#### Easy-to-remember answer structure

```text
Read Flow → Write/Invalidation → Benefits → Risks
```

#### Sample short answer

```text
In cache-aside, the application first checks the cache. If data is found, it returns from cache. If not, it loads data from the database, stores it in cache with a TTL, and returns it. The main benefit is reducing repeated database reads, but we need a clear invalidation strategy to avoid stale data.
```

#### Sample full answer

```text
Cache-aside is a common caching pattern where the application manages the cache explicitly.

For a read request, the application first checks Redis or another cache. If the value exists, it returns the cached value. If it is a cache miss, the application queries the database, stores the result in cache with a TTL, and returns the data.

For writes, the application updates the database first and then either invalidates the cache or updates the cached value depending on the consistency requirement.

This pattern is useful for reference data or read-heavy data. However, it can return stale data if invalidation is not handled well. In banking systems, I would be careful about what data is cached and avoid caching sensitive or highly transactional data unless there is a clear policy.
```

#### Common follow-up questions

- How do you invalidate cache?
- What is cache stampede?

#### Vietnamese explanation

Cache-aside là pattern hay hỏi nhất. Nhớ mention TTL và stale data.

---

## 4.7 Network & Security Questions

### Question 1: What is the difference between HTTP and HTTPS?

#### What the interviewer wants to check

Kiểm tra network/security basics.

#### Key points to remember

- HTTPS = HTTP over TLS.
- Encrypts data in transit.
- Provides server authentication and integrity.
- Important for banking APIs.

#### Easy-to-remember answer structure

```text
HTTP → HTTPS/TLS → Security Benefits → Banking Relevance
```

#### Sample short answer

```text
HTTP sends data in plain text, while HTTPS is HTTP over TLS. HTTPS encrypts data in transit, verifies the server certificate, and protects data integrity. For banking or financial systems, HTTPS is mandatory because APIs may transfer sensitive customer or transaction data.
```

#### Sample full answer

```text
HTTP is the application protocol used for communication between clients and servers, but by itself it does not encrypt the data.

HTTPS is HTTP over TLS. TLS provides encryption, server authentication through certificates, and integrity protection so that data cannot be easily read or modified in transit.

In backend systems, especially banking systems, HTTPS is required for APIs because requests may contain personal data, tokens, or transaction information. Even with HTTPS, we still need other security controls such as authentication, authorization, input validation, and secure logging.
```

#### Common follow-up questions

- What is TLS handshake?
- Is HTTPS enough for API security?

#### Vietnamese explanation

HTTPS là cần thiết nhưng không đủ; vẫn cần auth, authorization, validation.

---

### Question 2: How do you prevent SQL Injection?

#### What the interviewer wants to check

Kiểm tra OWASP/security basics.

#### Key points to remember

- Use parameterized queries/prepared statements.
- Avoid string concatenation.
- Validate input.
- Least privilege DB user.

#### Easy-to-remember answer structure

```text
Root Cause → Prevention → Additional Controls
```

#### Sample short answer

```text
SQL Injection happens when user input is directly concatenated into SQL. To prevent it, I use parameterized queries or prepared statements, avoid dynamic SQL string concatenation, validate input, and use least-privilege database accounts. ORMs like JPA help, but unsafe native queries can still be vulnerable.
```

#### Sample full answer

```text
SQL Injection happens when untrusted user input becomes part of a SQL command without proper parameterization.

The main prevention is to use parameterized queries or prepared statements, where user input is treated as data, not executable SQL. In JPA or Spring Data, using repository methods or named parameters helps, but we still need to be careful with native queries or dynamic query building.

I would also validate input, restrict database permissions using least privilege, and avoid exposing detailed database errors to clients.

In banking systems, this is especially important because SQL Injection can lead to data leakage or unauthorized data modification.
```

#### Common follow-up questions

- Can ORM fully prevent SQL Injection?
- What other OWASP risks do you know?

#### Vietnamese explanation

Đừng nói “dùng JPA là an toàn tuyệt đối”. Native query vẫn có thể lỗi.

---

## 4.8 Authentication / Authorization Questions

### Question 1: What is the difference between authentication and authorization?

#### What the interviewer wants to check

Kiểm tra security foundation.

#### Key points to remember

- Authentication = verify identity.
- Authorization = verify permissions.
- 401 vs 403.
- Roles/scopes/permissions.

#### Easy-to-remember answer structure

```text
Authentication → Authorization → HTTP Status → Example
```

#### Sample short answer

```text
Authentication verifies who the user is, for example by username/password, token, or SSO. Authorization checks what the authenticated user is allowed to do, based on roles, permissions, or scopes. If the user is not authenticated, we return 401. If authenticated but not allowed, we return 403.
```

#### Sample full answer

```text
Authentication and authorization are related but different.

Authentication is about verifying the identity of the user or client. For example, a user logs in with credentials, or a service sends a valid access token.

Authorization happens after authentication. It checks whether that user or service has permission to access a resource or perform an action. This can be based on roles, permissions, or OAuth2 scopes.

In REST APIs, if the request has no valid authentication, the response should be 401 Unauthorized. If the user is authenticated but does not have permission, the response should be 403 Forbidden.

In banking systems, both are important. We need to know who is calling the API and also ensure they can access only the allowed customer, account, or transaction data.
```

#### Common follow-up questions

- What is RBAC?
- What is scope-based authorization?

#### Vietnamese explanation

Nhớ 401 vs 403. Đây là câu dễ nhưng rất hay hỏi.

---

### Question 2: How does JWT work?

#### What the interviewer wants to check

Kiểm tra token-based authentication.

#### Key points to remember

- JWT has header, payload, signature.
- Server validates signature and claims.
- Do not store sensitive data in payload.
- Access token should be short-lived.

#### Easy-to-remember answer structure

```text
Structure → Login Flow → Validation → Security Concerns
```

#### Sample short answer

```text
JWT is a signed token with three parts: header, payload, and signature. After login, the server issues a token. The client sends it in the Authorization header. The backend validates the signature, expiration, issuer, audience, and roles or scopes before allowing access. We should not store sensitive data in the JWT payload because it is encoded, not encrypted by default.
```

#### Sample full answer

```text
JWT stands for JSON Web Token. It has three parts: header, payload, and signature.

The header describes the token type and signing algorithm. The payload contains claims such as subject, roles, scopes, issuer, audience, and expiration. The signature is used to verify that the token was issued by a trusted authority and was not modified.

In a typical flow, the user authenticates, and the identity provider or backend issues an access token. The client sends the token in the Authorization header as Bearer token. The backend validates the signature, expiration, issuer, audience, and required roles or scopes.

JWT is useful for stateless APIs, but there are security concerns. The payload is only Base64Url encoded, not encrypted by default, so we should not store sensitive data there. Access tokens should be short-lived, and refresh tokens should be protected and revocable.
```

#### Common follow-up questions

- Access token vs refresh token?
- How do you revoke JWT?

#### Vietnamese explanation

JWT payload không nên chứa sensitive data. Signature để verify integrity, không phải encryption.

---

## 4.9 Testing Questions

### Question 1: What is the difference between unit test and integration test?

#### What the interviewer wants to check

Kiểm tra testing mindset.

#### Key points to remember

- Unit test isolates a small unit.
- Integration test verifies components work together.
- Mock external dependencies in unit tests.
- Integration tests are slower but closer to real behavior.

#### Easy-to-remember answer structure

```text
Unit Scope → Integration Scope → Trade-off → Example
```

#### Sample short answer

```text
A unit test verifies a small unit of logic in isolation, usually with mocked dependencies. An integration test verifies that multiple components work together, such as controller-service-repository or database integration. Unit tests are faster and good for business logic, while integration tests give more confidence in wiring and real behavior.
```

#### Sample full answer

```text
Unit tests and integration tests have different purposes.

A unit test focuses on a small unit of code, such as a service method or business rule, in isolation. Dependencies like repositories or external clients are usually mocked with Mockito. Unit tests are fast and useful for covering business logic and edge cases.

An integration test verifies that multiple components work together. For example, a Spring Boot integration test may load the application context and test controller-service-repository integration, database queries, transaction behavior, or API behavior.

Unit tests are faster and easier to run frequently, but they may miss configuration or wiring issues. Integration tests are slower but provide more confidence that the system works closer to real runtime behavior.

In important banking flows, I would use both: unit tests for business rules and integration tests for API/database/transaction behavior.
```

#### Common follow-up questions

- What do you usually mock?
- What is test pyramid?

#### Vietnamese explanation

Nếu testing không quá mạnh, trả lời mindset rõ ràng là đủ tốt.

---

## 4.10 Lower-weight Questions

### Question 1: What does a typical CI/CD pipeline include?

#### What the interviewer wants to check

Kiểm tra DevOps awareness.

#### Key points to remember

- Build.
- Test.
- Static analysis/security scan.
- Package image/artifact.
- Deploy.
- Smoke test/monitoring.

#### Easy-to-remember answer structure

```text
Build → Test → Scan → Package → Deploy → Verify
```

#### Sample short answer

```text
A typical CI/CD pipeline includes checkout, build, unit tests, static code analysis or security scan, packaging the application or Docker image, deploying to an environment, running smoke tests, and monitoring after deployment. In my projects, I have used Jenkins, GitHub Actions, OpenShift, AWS CDK, and CloudWatch/Kibana for deployment and monitoring.
```

#### Sample full answer

```text
A typical CI/CD pipeline starts with checking out the code and building the application. Then it runs automated tests such as unit tests and possibly integration tests.

After that, the pipeline can run static code analysis or security scans using tools like SonarQube or Veracode. Then it packages the application, for example as a jar or Docker image, pushes the artifact to a repository, and deploys it to the target environment.

For production, there may be approval steps, smoke tests, health checks, monitoring, and rollback plans.

In my experience, Jenkins and OpenShift were used in the OCBC banking project, while GitHub Actions and AWS CDK were used in the Cox Automotive AWS project. CI/CD helps reduce manual deployment errors and improves release consistency.
```

#### Common follow-up questions

- What is blue-green deployment?
- How do you rollback?

#### Vietnamese explanation

Câu này nên link với CV: Jenkins/OpenShift, GitHub Actions/CDK.

---

### Question 2: What is the difference between Docker image and container?

#### What the interviewer wants to check

Kiểm tra Docker basics.

#### Key points to remember

- Image is a packaged template.
- Container is a running instance.
- Image contains app and dependencies.
- Container runs isolated process.

#### Easy-to-remember answer structure

```text
Image Definition → Container Definition → Example
```

#### Sample short answer

```text
A Docker image is a packaged, read-only template that contains the application and its dependencies. A container is a running instance of an image. For example, we build a Spring Boot application into an image, push it to a registry, and run containers from that image in Kubernetes or OpenShift.
```

#### Sample full answer

```text
A Docker image is a packaged artifact. It includes the application, runtime, dependencies, and configuration needed to run the application. It is usually built from a Dockerfile and pushed to an image registry.

A container is a running instance of that image. It runs as an isolated process with its own filesystem, environment variables, and network configuration.

For example, in a Spring Boot project, the CI/CD pipeline can build a jar, create a Docker image, push it to a registry, and then OpenShift or Kubernetes runs containers based on that image.
```

#### Common follow-up questions

- What is Dockerfile?
- How do containers get configuration?

#### Vietnamese explanation

Image = bản đóng gói; container = instance đang chạy.

---

## 5. Final Revision Checklist

### High Priority

- [ ] Tôi giải thích được OOP principles và SOLID ở mức practical.
- [ ] Tôi hiểu ArrayList, LinkedList, HashMap, ConcurrentHashMap.
- [ ] Tôi giải thích được equals/hashCode contract.
- [ ] Tôi phân biệt checked vs unchecked exception và biết global exception handling.
- [ ] Tôi hiểu Stream API: map/filter/flatMap/reduce/lazy evaluation.
- [ ] Tôi giải thích được immutability và thread-safety.
- [ ] Tôi hiểu Runnable, Callable, ExecutorService, CompletableFuture.
- [ ] Tôi biết race condition, deadlock, synchronized, volatile basics.
- [ ] Tôi hiểu Dependency Injection, bean, constructor injection.
- [ ] Tôi hiểu `@Transactional`, rollback, service-layer boundary, self-invocation caveat.
- [ ] Tôi biết REST API design, validation, exception response.
- [ ] Tôi hiểu microservices trade-off, service boundary, resilience.
- [ ] Tôi giải thích được retry/timeout/circuit breaker/idempotency.
- [ ] Tôi hiểu Kafka/event-driven basics và duplicate event handling.
- [ ] Tôi biết SQL optimization process: execution plan, index, query review, N+1.
- [ ] Tôi hiểu transaction isolation và optimistic/pessimistic locking.
- [ ] Tôi phân biệt authentication vs authorization, 401 vs 403.
- [ ] Tôi giải thích được JWT, access token, refresh token.

### Medium Priority

- [ ] Tôi có ví dụ thực tế cho Strategy, Factory, Adapter, Pub/Sub.
- [ ] Tôi giải thích được cache-aside, TTL, invalidation, Redis use cases.
- [ ] Tôi hiểu cache consistency và cache stampede.
- [ ] Tôi giải thích được HTTP vs HTTPS, TLS basics.
- [ ] Tôi hiểu CORS và OWASP basics.
- [ ] Tôi biết cách prevent SQL Injection.
- [ ] Tôi phân biệt unit test, integration test, E2E test.
- [ ] Tôi biết Spring Boot testing annotations cơ bản.
- [ ] Tôi có thể nói testing mindset tích cực dù không quá sâu.

### Low Priority

- [ ] Tôi giải thích được CI/CD pipeline stages.
- [ ] Tôi phân biệt Docker image và container.
- [ ] Tôi biết logging/monitoring basics: structured logs, correlation ID, alerts.
- [ ] Tôi có thể design high-level banking flow: payment, teller session, transaction history.
- [ ] Tôi biết web general basics: DNS, HTTP headers, cookies.

### Project Examples to Reuse in Phase 2

- [ ] OCBC Bank: WebSocket dual-screen, Kafka events, banking auditability, OpenShift/Jenkins/Kibana.
- [ ] Cox Automotive: AWS Lambda/SQS/EventBridge/DynamoDB, idempotency, DLQ, CloudWatch alerts.
- [ ] Zurich Insurance: Java/MongoDB batch modernization, job dependency, record-level status, idempotent rerun.

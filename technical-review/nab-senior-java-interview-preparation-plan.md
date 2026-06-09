# NAB Senior Java Developer Interview Preparation Plan

## Overview

Kế hoạch này được thiết kế cho buổi phỏng vấn **Senior Java Developer / Backend Engineer tại NAB**, dựa trên format HR đã gợi ý:

- **Phase 1:** English communication, self-introduction, project sharing.
- **Phase 2:** Technical interview, trọng tâm là Java backend, Spring Boot, microservices, database, security, testing.
- **Phase 3:** Behavioral interview, tập trung vào tình huống làm việc trong team, conflict, ownership, delivery mindset.
- **Phase 4:** Q&A với interviewer để thể hiện sự chuẩn bị, quan tâm đến team, tech stack và culture.

Chiến lược ôn tập nên là: **chọn một dự án backend/microservice mình hiểu sâu nhất**, chuẩn bị câu chuyện rõ ràng từ business đến technical, sau đó ôn kỹ các topic backend có xác suất hỏi cao. Với NAB, nên thể hiện mindset của Senior Engineer: **ownership, reliability, security, maintainability, collaboration, production mindset**.

---

## Phase 1: English Communication & Project Sharing

### Goal

Mục tiêu của phase này là kiểm tra khả năng giao tiếp tiếng Anh, khả năng trình bày kinh nghiệm thực tế và độ hiểu sâu về dự án đã làm. Đây cũng là phần tạo ấn tượng đầu tiên trước khi vào technical deep dive.

Interviewer thường muốn biết:

- Bạn có thể giao tiếp hiệu quả trong môi trường quốc tế không.
- Bạn có thật sự hiểu business domain của dự án không.
- Bạn có thể giải thích technical design một cách rõ ràng không.
- Bạn có ownership trong project hay chỉ làm task nhỏ lẻ.

### What to Prepare

Chuẩn bị trước bằng tiếng Anh cho 3 phần chính:

1. **Self-introduction trong 1.5 - 2 phút**
2. **Project sharing trong 5 - 7 phút**
3. **Deep dive follow-up questions về project**

Nên chọn một dự án bạn rành nhất, tốt nhất là dự án có:

- Java / Spring Boot backend.
- REST API hoặc microservices.
- Database transaction, integration với external systems.
- Business flow rõ ràng.
- Có technical challenge thật sự: performance, scalability, security, reliability, migration, refactoring, production issue.

### Key Points to Learn

- Cách giới thiệu bản thân ngắn gọn:
  - Years of experience.
  - Main tech stack.
  - Domain experience.
  - Recent project.
  - Strengths relevant to NAB.
- Cách trình bày project theo cấu trúc:
  - Business context.
  - Your role.
  - System architecture.
  - Main tech stack.
  - Key responsibilities.
  - Challenges and solutions.
  - Result / impact.
- Từ vựng tiếng Anh nên luyện:
  - `I was responsible for...`
  - `The main business goal was...`
  - `The system was designed to...`
  - `We integrated with...`
  - `One of the main challenges was...`
  - `To solve this, we...`
  - `The outcome was...`
- Chuẩn bị diagram đơn giản trong đầu:
  - Client / Frontend.
  - API Gateway nếu có.
  - Backend services.
  - Database.
  - Message queue / cache / external services nếu có.
- Chuẩn bị 2 - 3 technical decisions bạn đã tham gia:
  - Vì sao chọn REST API design đó.
  - Vì sao tách service hoặc không tách service.
  - Vì sao dùng cache hoặc index.
  - Vì sao chọn async processing.

### Possible Interview Questions

- Can you introduce yourself?
- Can you walk me through your recent project?
- What was the business purpose of the project?
- What was your role and responsibility in the team?
- What was the architecture of the system?
- Which part of the project were you most involved in?
- What was the most challenging technical problem you solved?
- How did you ensure code quality in that project?
- How did your team handle requirement changes?
- How did you collaborate with BA, QA, Product Owner, or offshore teams?
- What would you improve if you had more time?

### Recommended Answer Strategy

Dùng cấu trúc trả lời sau:

```text
1. Context: The project was about...
2. Business goal: The main goal was to...
3. My role: I worked as a Senior Java Developer and was responsible for...
4. Technical stack: We used Java, Spring Boot, REST API, PostgreSQL/Oracle, Redis, Kafka...
5. Architecture: The system had several services such as...
6. Challenge: One key challenge was...
7. Solution: To solve it, we...
8. Result: As a result, we improved/reduced/enabled...
```

Ví dụ ngắn:

```text
In my recent project, I worked on a backend system for digital customer onboarding.
The business goal was to allow users to submit applications online and track their status.
I was responsible for designing and implementing several Spring Boot services, including customer profile, application workflow, and integration with external verification services.
One of the main challenges was handling inconsistent responses from external systems while keeping the customer journey reliable.
To solve this, we introduced retry logic, timeout handling, proper error mapping, and audit logging.
As a result, the system became more stable and easier to troubleshoot in production.
```

Điểm cần tránh:

- Không kể quá dài về company hoặc team mà thiếu technical details.
- Không nói chung chung kiểu `I joined development and fixed bugs`.
- Không chọn project mình không nhớ rõ architecture.
- Không exaggerate nếu không thể trả lời follow-up.

### Priority

**High**

Phase này ảnh hưởng lớn đến first impression và tạo context cho technical phase. Nếu chia sẻ project tốt, interviewer thường sẽ hỏi follow-up dựa trên project đó, giúp bạn chủ động hơn.

### Suggested Preparation Time

- **2 - 3 giờ:** Viết script self-introduction và project sharing.
- **2 giờ:** Luyện nói tiếng Anh thành tiếng, record lại và sửa.
- **1 - 2 giờ:** Chuẩn bị follow-up technical questions từ project.

---

## Phase 2: Technical Interview

### Goal

Mục tiêu của phase này là đánh giá nền tảng backend senior: Core Java, Spring Boot, microservices, database, security, testing và khả năng giải thích trade-off trong thiết kế hệ thống.

Với Senior Java tại NAB, trọng tâm không chỉ là biết syntax mà là:

- Hiểu nguyên lý hoạt động.
- Biết áp dụng vào production system.
- Biết trade-off giữa các giải pháp.
- Có mindset về reliability, security, maintainability và performance.

### What to Prepare

Ưu tiên học theo thứ tự:

1. Core Java và OOP.
2. Spring Boot foundation.
3. REST API và microservices.
4. Database, transaction, SQL optimization.
5. Authentication / Authorization và security basics.
6. Testing mindset.
7. Caching, Redis.
8. System design backend banking.
9. CI/CD, Docker, DevOps basics.

### Key Points to Learn

#### 1. Core Java

**Priority:** High  
**Suggested time:** 4 - 6 giờ

Cần ôn:

- OOP principles:
  - Encapsulation.
  - Inheritance.
  - Polymorphism.
  - Abstraction.
- SOLID principles:
  - Single Responsibility.
  - Open/Closed.
  - Liskov Substitution.
  - Interface Segregation.
  - Dependency Inversion.
- Difference between abstract class and interface.
- Equals and hashCode contract.
- Immutability:
  - `final` class.
  - private final fields.
  - no setter.
  - defensive copy.
- Java memory basics:
  - stack vs heap.
  - garbage collection basic idea.
- Exception handling:
  - checked vs unchecked exception.
  - custom exception.
  - when to catch vs throw.
  - global exception handling in Spring.

Possible questions:

- What are the main OOP principles?
- What is the difference between interface and abstract class?
- Why do we need to override equals and hashCode together?
- What makes a class immutable in Java?
- What is the difference between checked and unchecked exception?
- How do you handle exceptions in a Spring Boot application?

Answer strategy:

- Trả lời định nghĩa ngắn.
- Đưa ví dụ thực tế trong backend.
- Nêu best practice.

Ví dụ:

```text
I usually use unchecked exceptions for business validation or runtime failures, then map them to proper HTTP responses using a global exception handler with @ControllerAdvice. For recoverable cases, I try to handle them close to the source, but for business errors I prefer throwing domain-specific exceptions to keep the service logic clean.
```

#### 2. Collections

**Priority:** High  
**Suggested time:** 3 - 4 giờ

Cần ôn:

- List vs Set vs Map.
- ArrayList vs LinkedList.
- HashMap vs ConcurrentHashMap.
- HashSet internal mechanism.
- Time complexity:
  - HashMap get/put average O(1).
  - ArrayList get O(1), insert middle O(n).
- Fail-fast behavior.
- Thread-safety of collections.

Possible questions:

- How does HashMap work internally?
- What happens when two keys have the same hashCode?
- Difference between HashMap and ConcurrentHashMap?
- When would you use Set instead of List?
- How do you avoid ConcurrentModificationException?

Answer strategy:

- Với HashMap, cần nói được: hashCode -> bucket -> equals -> collision handling.
- Với ConcurrentHashMap, nhấn mạnh thread-safe access và better concurrency than synchronizing whole map.

#### 3. Stream API

**Priority:** Medium - High  
**Suggested time:** 2 - 3 giờ

Cần ôn:

- `map`, `filter`, `flatMap`, `collect`, `reduce`.
- Intermediate vs terminal operations.
- Lazy evaluation.
- Parallel stream: khi nào nên / không nên dùng.
- Avoid side effects trong stream.

Possible questions:

- Difference between map and flatMap?
- What is lazy evaluation in Stream API?
- When should we avoid parallel stream?
- How do you group data using streams?

Answer strategy:

- Đưa ví dụ xử lý DTO list, grouping, filtering.
- Nói rõ parallel stream không phải lúc nào cũng nhanh hơn, đặc biệt với IO-bound tasks hoặc shared mutable state.

#### 4. Multithreading and Concurrency

**Priority:** High  
**Suggested time:** 4 - 6 giờ

Cần ôn:

- Thread vs Runnable vs Callable.
- ExecutorService.
- Future vs CompletableFuture.
- synchronized, volatile, lock basics.
- Race condition.
- Deadlock.
- Thread pool sizing basic idea.
- Async processing in Spring with `@Async`.
- Transaction and async caveats.

Possible questions:

- What is the difference between Runnable and Callable?
- What is race condition and how do you prevent it?
- What is volatile used for?
- How does ExecutorService help compared to creating threads manually?
- What can cause deadlock?
- Have you used CompletableFuture?

Answer strategy:

- Gắn với backend use case: call multiple external APIs, async notification, background processing.
- Nói rõ cần handle timeout, exception, thread pool config, observability.

#### 5. Spring Boot Foundation

**Priority:** High  
**Suggested time:** 5 - 7 giờ

Cần ôn:

- Dependency Injection / IoC.
- Bean lifecycle basic.
- `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`.
- `@Configuration`, `@Bean`.
- `@Autowired` vs constructor injection.
- Application properties / profiles.
- Spring Boot auto-configuration.
- Validation with `@Valid`.
- Global exception handling with `@ControllerAdvice`.
- Transaction management with `@Transactional`.
- Spring Data JPA basics nếu có dùng.

Possible questions:

- What is Dependency Injection?
- Why is constructor injection preferred?
- What is Spring Boot auto-configuration?
- Difference between @Component, @Service, and @Repository?
- How does @Transactional work?
- How do you validate request payloads?
- How do you handle exceptions globally?

Answer strategy:

- Luôn liên hệ đến maintainability và testability.
- Với `@Transactional`, nói được rollback, propagation basic, transaction boundary ở service layer.

Ví dụ:

```text
I prefer constructor injection because it makes dependencies explicit, supports immutability, and makes unit testing easier. It also helps detect missing dependencies at object creation time instead of later during execution.
```

#### 6. REST API Design

**Priority:** High  
**Suggested time:** 3 - 4 giờ

Cần ôn:

- HTTP methods: GET, POST, PUT, PATCH, DELETE.
- Status codes:
  - 200, 201, 204.
  - 400, 401, 403, 404, 409, 422.
  - 500, 502, 503.
- Resource naming.
- Request / response DTO.
- Pagination, sorting, filtering.
- Idempotency.
- API versioning.
- Error response standardization.
- Correlation ID / request ID.

Possible questions:

- How do you design a REST API for creating a payment?
- Difference between PUT and PATCH?
- What status code do you return for validation error?
- How do you handle duplicate requests?
- How do you version APIs?

Answer strategy:

- Với banking/payment, nhấn mạnh idempotency, validation, audit logging, security, traceability.

#### 7. Microservices

**Priority:** High  
**Suggested time:** 4 - 6 giờ

Cần ôn:

- Microservice vs monolith.
- Service boundary.
- Synchronous vs asynchronous communication.
- REST vs messaging.
- API Gateway basic.
- Service discovery basic.
- Resilience patterns:
  - timeout.
  - retry.
  - circuit breaker.
  - bulkhead.
- Distributed transaction problem.
- Saga pattern basic.
- Eventual consistency.
- Observability:
  - logs.
  - metrics.
  - tracing.
- Config management.

Possible questions:

- What are the advantages and disadvantages of microservices?
- How do services communicate with each other?
- How do you handle failure when calling another service?
- How do you manage distributed transactions?
- What is eventual consistency?
- How do you monitor microservices in production?

Answer strategy:

- Không nói microservices luôn tốt.
- Nêu trade-off: complexity, deployment, observability, data consistency.
- Với banking, nhấn mạnh auditability, transaction safety, idempotency, reliability.

#### 8. Design Patterns for Backend

**Priority:** Medium - High  
**Suggested time:** 3 - 4 giờ

Cần ôn các pattern thường gặp:

- Singleton:
  - Spring beans mặc định singleton.
- Factory:
  - Tạo object hoặc chọn implementation theo type.
- Strategy:
  - Chọn business rule/payment method/validation rule.
- Template Method:
  - Common flow với một vài bước custom.
- Builder:
  - Tạo object phức tạp, immutable DTO.
- Adapter:
  - Wrap external API/client.
- Facade:
  - Đơn giản hóa orchestration logic.
- Repository:
  - Data access abstraction.
- Circuit Breaker:
  - Resilience khi gọi external service.

Possible questions:

- Which design patterns have you used in your project?
- How would you avoid many if-else conditions for different business rules?
- Where have you used Strategy pattern?
- How is Factory pattern useful?
- Is Singleton still relevant in Spring Boot?

Answer strategy:

- Chuẩn bị 2 ví dụ thật từ project.
- Tránh chỉ đọc định nghĩa; phải nói được khi nào dùng và vì sao.

#### 9. Database, SQL Optimization, Transaction, Indexing

**Priority:** High  
**Suggested time:** 5 - 7 giờ

Cần ôn:

- SQL joins: inner, left, right.
- Aggregation: group by, having.
- Index:
  - B-tree basic.
  - composite index.
  - selectivity.
  - index trade-off with write performance.
- Query optimization:
  - avoid select *.
  - use proper where conditions.
  - avoid function on indexed column.
  - pagination strategy.
  - analyze execution plan.
- Transaction ACID:
  - Atomicity.
  - Consistency.
  - Isolation.
  - Durability.
- Isolation levels:
  - Read Uncommitted.
  - Read Committed.
  - Repeatable Read.
  - Serializable.
- Common problems:
  - dirty read.
  - non-repeatable read.
  - phantom read.
- Locking:
  - optimistic locking.
  - pessimistic locking.
- N+1 query problem.
- Connection pool basics.

Possible questions:

- How do you optimize a slow SQL query?
- What is an index and when should we use it?
- Can too many indexes be bad?
- What are ACID properties?
- What is the difference between optimistic and pessimistic locking?
- How do you prevent duplicate transactions?
- What is N+1 query problem?

Answer strategy:

- Với slow query, trả lời theo checklist:

```text
First, I would reproduce the issue and check the query execution plan.
Then I would verify whether the query uses the right index, whether the WHERE clause is selective, and whether there are unnecessary joins or SELECT *.
I would also check data volume, pagination, and whether the issue comes from application-level N+1 queries.
After applying changes, I would compare execution time before and after and monitor production metrics.
```

#### 10. Caching Strategy and Redis

**Priority:** Medium - High  
**Suggested time:** 2 - 4 giờ

Cần ôn:

- Why caching.
- Cache-aside pattern.
- Read-through / write-through basic idea.
- TTL.
- Cache invalidation.
- Cache key design.
- Avoid caching sensitive data.
- Redis use cases:
  - session/token blacklist.
  - reference data.
  - rate limiting.
  - distributed lock, with caution.
- Cache stampede.
- Consistency trade-off.

Possible questions:

- When would you use caching?
- How do you invalidate cache?
- What data should not be cached?
- How do you handle stale data?
- Have you used Redis? For what use case?

Answer strategy:

- Nhấn mạnh cache không phải giải pháp cho mọi performance issue.
- Với banking, chú ý sensitive data, TTL ngắn, encryption/masking nếu cần, audit/security policy.

#### 11. Authentication / Authorization

**Priority:** High  
**Suggested time:** 4 - 5 giờ

Cần ôn:

- Authentication vs Authorization.
- Session-based auth vs token-based auth.
- JWT structure:
  - header.
  - payload.
  - signature.
- Access token vs refresh token.
- OAuth2 basic flow.
- Role-based access control.
- Permission/scope-based access control.
- Spring Security filter chain basic.
- Password hashing basic.
- Token expiration and revocation.
- 401 vs 403.

Possible questions:

- Difference between authentication and authorization?
- How does JWT work?
- What is the difference between access token and refresh token?
- How do you secure REST APIs?
- What is OAuth2 used for?
- Difference between 401 and 403?

Answer strategy:

- Trả lời bằng flow:
  - User login.
  - System validates credentials.
  - Issues token.
  - Client sends token in Authorization header.
  - Backend validates token and checks roles/scopes.
- Với banking, nhấn mạnh principle of least privilege, token expiry, audit log, secure transport via HTTPS.

#### 12. Basic Network and Security

**Priority:** Medium - High  
**Suggested time:** 3 - 4 giờ

Cần ôn:

- HTTP vs HTTPS.
- TLS basic.
- DNS basic.
- TCP vs UDP basic.
- HTTP headers.
- CORS.
- OWASP common risks:
  - SQL Injection.
  - XSS.
  - CSRF.
  - Broken Authentication.
  - Sensitive Data Exposure.
- Input validation.
- Output encoding.
- Rate limiting.
- Secure logging:
  - không log password, token, PII.
- Secrets management.

Possible questions:

- What happens when you call an HTTPS URL from browser/client?
- What is CORS?
- How do you prevent SQL Injection?
- What sensitive information should not be logged?
- How do you secure communication between services?

Answer strategy:

- Gắn với practical backend:
  - Use parameterized queries.
  - Validate input.
  - Use HTTPS.
  - Mask sensitive logs.
  - Secure secrets outside source code.

#### 13. Testing: Unit, Integration, E2E Mindset

**Priority:** Medium  
**Suggested time:** 3 - 4 giờ

Cần ôn:

- Unit test:
  - test service logic isolated.
  - JUnit, Mockito.
- Integration test:
  - test with Spring context.
  - repository/API integration.
  - Testcontainers nếu biết.
- E2E test:
  - validate full business flow.
- Test pyramid.
- Mock vs stub.
- Code coverage vs test quality.
- Testing edge cases and error cases.

Possible questions:

- How do you write unit tests for a service?
- What is the difference between unit test and integration test?
- What do you usually mock?
- How do you test external service integration?
- What is your view on code coverage?

Answer strategy:

- Nếu chưa quá mạnh về testing, trả lời với positive mindset:

```text
I believe testing is important for maintainability, especially for business-critical flows. For service logic, I write unit tests with mocked dependencies. For database or API integration, I prefer integration tests to verify wiring and real behavior. I also pay attention to negative cases, boundary conditions, and regression tests for production bugs.
```

#### 14. Basic System Design for Backend Banking

**Priority:** Medium - High  
**Suggested time:** 4 - 6 giờ

Cần chuẩn bị design đơn giản cho các bài:

- Design a payment transfer service.
- Design transaction history API.
- Design customer onboarding service.
- Design notification service.
- Design audit logging for banking actions.

Key points:

- API design.
- Data model.
- Transaction boundary.
- Idempotency.
- Validation.
- Authentication and authorization.
- Audit log.
- Error handling.
- Retry and timeout.
- Observability.
- Security and PII masking.

Possible questions:

- How would you design a money transfer API?
- How do you avoid duplicate payment?
- How do you ensure auditability?
- How do you handle external payment gateway timeout?
- How would you scale a transaction history service?

Answer strategy:

- Luôn bắt đầu bằng requirements clarification.
- Với banking, nói rõ consistency và security quan trọng hơn chỉ performance.
- Nêu happy path, failure path và observability.

#### 15. CI/CD, Docker, DevOps Basics

**Priority:** Low - Medium  
**Suggested time:** 2 - 3 giờ

Cần ôn:

- CI/CD pipeline stages:
  - build.
  - unit test.
  - static code analysis.
  - package.
  - deploy.
- Docker basics:
  - image.
  - container.
  - Dockerfile.
  - environment variables.
- Health checks.
- Blue-green / rolling deployment basic.
- Config per environment.
- Logs and monitoring.

Possible questions:

- What happens in your CI/CD pipeline?
- Have you used Docker?
- What is the difference between Docker image and container?
- How do you configure different environments?
- How do you know if deployment is successful?

Answer strategy:

- Không cần quá sâu DevOps nhưng phải hiểu flow release.
- Nói được mình phối hợp với DevOps/team để deploy, monitor, rollback nếu cần.

### Recommended Answer Strategy for Phase 2

Khi gặp câu hỏi technical, dùng format:

```text
1. Definition: Explain the concept briefly.
2. Practical usage: Where I use it in backend projects.
3. Trade-off: When it works well and when to be careful.
4. Example: Give a concrete project example if possible.
```

Ví dụ với caching:

```text
Caching is used to reduce repeated expensive operations, such as reading reference data from the database or calling external systems. In my projects, I usually use cache-aside with a TTL. The application checks Redis first, and if the data is missing, it loads from the database and stores it in cache. However, I am careful with cache invalidation and I avoid caching sensitive or frequently changing banking data unless there is a clear policy.
```

### Priority

**High**

Đây là phase quan trọng nhất của buổi phỏng vấn. Nên dành phần lớn thời gian ôn tập cho Core Java, Spring Boot, REST API, microservices, database và security.

### Suggested Preparation Time

Nếu có 7 ngày:

- **Day 1:** Core Java, OOP, Collections, Exception.
- **Day 2:** Stream API, Multithreading, concurrency basics.
- **Day 3:** Spring Boot foundation, REST API design.
- **Day 4:** Microservices, resilience, design patterns.
- **Day 5:** Database, transaction, indexing, SQL optimization.
- **Day 6:** Security, authentication/authorization, caching, testing.
- **Day 7:** System design, CI/CD, mock interview.

---

## Phase 3: Behavioral Interview

### Goal

Mục tiêu của phase này là đánh giá cách bạn làm việc trong team, xử lý conflict, cân bằng delivery và quality, khả năng học cái mới, collaboration và maturity của một Senior Developer.

NAB có môi trường quốc tế và đề cao khả năng fullstack/collaboration, nên câu trả lời cần thể hiện:

- Ownership.
- Open communication.
- Data-driven decision making.
- Respectful disagreement.
- Customer/business mindset.
- Willingness to learn.
- Team-first mindset.

### What to Prepare

Chuẩn bị 6 - 8 câu chuyện thực tế theo STAR format:

```text
S - Situation: Bối cảnh là gì?
T - Task: Bạn cần xử lý việc gì?
A - Action: Bạn đã làm gì cụ thể?
R - Result: Kết quả ra sao?
```

Mỗi câu chuyện nên có:

- Vấn đề thật.
- Hành động cụ thể của bạn.
- Kết quả đo được hoặc lesson learned.
- Không blame người khác.

### Key Points to Learn

#### 1. Conflict về technical solution giữa developers

**Priority:** High  
**Suggested time:** 1 giờ chuẩn bị câu chuyện

Possible questions:

- Tell me about a time you disagreed with another developer about a technical solution.
- How do you handle conflict with peers?
- What if two developers propose different solutions?

Recommended answer strategy:

- Không nói ai đúng ai sai ngay.
- So sánh dựa trên criteria:
  - maintainability.
  - performance.
  - security.
  - delivery timeline.
  - team familiarity.
  - operational impact.
- Đề xuất POC nếu cần.
- Align với team/lead sau khi có data.

Answer template:

```text
In one project, we had different opinions about the implementation approach.
Instead of debating based on personal preference, I suggested we compare both options using criteria such as maintainability, performance, delivery effort, and long-term support.
I prepared a small POC and documented the trade-offs.
After discussion with the team and tech lead, we selected the option that was simpler to maintain and safer for the timeline.
The key lesson for me was to make technical discussions objective and data-driven.
```

#### 2. Conflict với tech lead

**Priority:** High  
**Suggested time:** 1 giờ

Possible questions:

- What would you do if you disagree with your tech lead?
- Have you ever challenged a decision from your lead?

Recommended answer strategy:

- Respect hierarchy nhưng vẫn có responsibility góp ý.
- Trình bày concern bằng facts.
- Đề xuất alternative.
- Nếu lead quyết định, commit theo decision sau khi đã raise concern.

Answer template:

```text
If I disagree with my tech lead, I first try to understand the reason behind the decision because they may have broader context.
Then I share my concerns with concrete examples, such as possible performance impact, security risk, or maintenance cost.
If needed, I suggest an alternative or a small POC.
Once the final decision is made, I support it and focus on successful delivery, unless there is a serious risk that needs escalation.
```

#### 3. Deadline gấp nhưng code quality chưa tốt

**Priority:** High  
**Suggested time:** 1 giờ

Possible questions:

- What would you do if the deadline is tight but the code quality is not good?
- How do you balance delivery and quality?

Recommended answer strategy:

- Không chọn cực đoan: không phải delay mọi thứ, cũng không phải ship code xấu.
- Phân loại quality issues:
  - must-fix: security, data correctness, production risk.
  - can-defer: refactor, minor cleanup.
- Communicate risk rõ ràng.
- Tạo technical debt ticket nếu defer.
- Bảo vệ critical quality gates: tests, review, monitoring.

Answer template:

```text
I would first identify which quality issues are critical and which can be safely deferred.
For example, anything related to security, data correctness, transaction consistency, or production stability must be fixed before release.
For non-critical refactoring, I would discuss with the team and PO, document the technical debt, and plan a follow-up task.
I believe delivery is important, but for banking systems we should not compromise on critical quality and customer data safety.
```

#### 4. Làm việc với requirement chưa rõ

**Priority:** High  
**Suggested time:** 45 phút

Possible questions:

- What do you do when requirements are unclear?
- How do you work with BA or Product Owner?

Recommended answer strategy:

- Clarify business goal.
- Ask examples and edge cases.
- Confirm acceptance criteria.
- Document assumptions.
- Break work into smaller deliverables.

Answer template:

```text
When requirements are unclear, I avoid making assumptions silently.
I usually clarify the business goal with the BA or Product Owner, ask for examples, edge cases, and expected behavior for error scenarios.
Then I document the agreed understanding and acceptance criteria so that developers, QA, and business stakeholders are aligned.
This reduces rework and helps QA prepare better test cases.
```

#### 5. Production issue hoặc bug nghiêm trọng

**Priority:** High  
**Suggested time:** 1 giờ

Possible questions:

- Tell me about a production issue you handled.
- What do you do when there is a severe production bug?
- How do you handle incident pressure?

Recommended answer strategy:

- Bình tĩnh, ưu tiên impact mitigation.
- Triage:
  - scope.
  - severity.
  - affected users.
  - data impact.
- Communicate with stakeholders.
- Rollback/hotfix nếu cần.
- Root cause analysis.
- Prevent recurrence.

Answer template:

```text
For a serious production issue, my first priority is to reduce customer and business impact.
I would help triage the issue by checking logs, metrics, recent deployments, and affected user flows.
If the issue is caused by a recent release, we consider rollback or hotfix depending on the impact.
After recovery, I participate in root cause analysis and propose preventive actions such as better test coverage, monitoring alerts, or validation rules.
```

#### 6. Sẵn sàng học công nghệ mới

**Priority:** Medium - High  
**Suggested time:** 30 phút

Possible questions:

- Are you willing to learn new technologies?
- Tell me about a time you had to learn something new quickly.

Recommended answer strategy:

- Cho ví dụ thật.
- Nêu cách học:
  - đọc docs.
  - làm POC.
  - hỏi người có kinh nghiệm.
  - áp dụng vào task nhỏ.
- Thể hiện growth mindset.

Answer template:

```text
Yes, I am comfortable learning new technologies when the project needs them.
My approach is to first understand the problem the technology solves, then read the official documentation, build a small POC, and discuss with teammates before applying it to production code.
I believe a senior engineer should not only learn fast but also evaluate whether a technology is suitable for the team and long-term maintenance.
```

#### 7. Sẵn sàng làm fullstack nếu công ty yêu cầu

**Priority:** Medium - High  
**Suggested time:** 30 phút

Possible questions:

- NAB values fullstack capability. Are you open to working on frontend as well?
- How comfortable are you with fullstack development?

Recommended answer strategy:

- Thành thật về strength backend.
- Thể hiện sẵn sàng học và support frontend.
- Nhấn mạnh goal là deliver product value.

Answer template:

```text
My strongest experience is in backend development with Java and Spring Boot, but I am open to working fullstack when the team needs it.
I have worked with frontend teams and can understand API integration, UI flow, and basic frontend changes.
If the project requires deeper frontend contribution, I am willing to learn and gradually take more ownership.
For me, the priority is helping the team deliver business value effectively.
```

#### 8. Collaboration với BA, QA, Product Owner, team quốc tế

**Priority:** High  
**Suggested time:** 45 phút

Possible questions:

- How do you collaborate with QA?
- How do you work with BA or Product Owner?
- Have you worked with international teams?
- How do you handle communication across time zones?

Recommended answer strategy:

- Nêu cụ thể cách phối hợp:
  - clarify requirement với BA/PO.
  - review acceptance criteria.
  - support QA test data, edge cases.
  - communicate status/blockers early.
  - document decisions.
- Với international team, nhấn mạnh clear written communication.

Answer template:

```text
I try to collaborate early, not only at the end of development.
With BA and PO, I clarify requirements, edge cases, and acceptance criteria before implementation.
With QA, I share technical notes, test data suggestions, and risky areas to focus on.
For international teams, I prefer clear written communication, meeting notes, and early updates about blockers because time zone differences can slow down feedback loops.
```

### Possible Interview Questions

- Tell me about a time you had a conflict with a teammate.
- How do you deal with disagreement on technical solutions?
- What if your tech lead chooses a solution you disagree with?
- How do you handle pressure near deadline?
- Tell me about a time requirements changed late.
- Tell me about a production issue you handled.
- How do you ensure quality while delivering fast?
- Are you willing to learn frontend or other technologies?
- How do you collaborate with QA and BA?
- What kind of team culture do you prefer?

### Recommended Answer Strategy

- Luôn dùng STAR.
- Không blame cá nhân.
- Không trả lời quá lý thuyết; cần ví dụ thật.
- Tập trung vào action của bạn.
- Kết thúc bằng result hoặc lesson learned.
- Với NAB, nhấn mạnh security, customer impact, reliability và teamwork.

### Priority

**High**

Behavioral có thể quyết định level senior. Technical tốt nhưng behavioral yếu sẽ gây lo ngại về khả năng làm việc trong team lớn hoặc môi trường banking.

### Suggested Preparation Time

- **3 - 4 giờ:** Chuẩn bị 6 - 8 STAR stories.
- **1 - 2 giờ:** Luyện nói thành tiếng bằng English.
- **30 phút:** Chuẩn bị keywords cho từng tình huống để nhớ nhanh khi phỏng vấn.

---

## Phase 4: Q&A with Interviewer

### Goal

Mục tiêu của phase này là thể hiện bạn nghiêm túc với vị trí, hiểu môi trường ngân hàng số và quan tâm đến cách team vận hành. Đây cũng là cơ hội để bạn đánh giá team có phù hợp với mình không.

Một Senior candidate nên hỏi câu hỏi về:

- Team structure.
- Engineering practices.
- Tech stack.
- Delivery process.
- Quality and production ownership.
- Growth expectations.

### What to Prepare

Chuẩn bị 5 - 7 câu hỏi, nhưng chỉ cần hỏi 2 - 4 câu tùy thời gian. Nên chọn câu hỏi thể hiện senior mindset, tránh hỏi quá sớm về benefit/salary nếu chưa đến vòng phù hợp.

### Key Points to Learn

- Câu hỏi về project/team hiện tại.
- Câu hỏi về tech stack backend.
- Câu hỏi về CI/CD, testing, release process.
- Câu hỏi về production support và ownership.
- Câu hỏi về expectation cho Senior Engineer.
- Câu hỏi về fullstack direction tại NAB.

### Possible Questions to Ask Interviewer

#### About role expectation

- For this Senior Java Developer role, what are the most important expectations in the first 3 to 6 months?
- What does success look like for this role after the first 6 months?
- Is the role more focused on feature delivery, platform improvement, production support, or system modernization?

#### About team and collaboration

- Could you share more about the team structure and how developers collaborate with BA, QA, Product Owner, and other teams?
- Does the team work in a fully agile setup? How are requirements usually refined before development?
- How does the team handle technical decision-making and architecture review?

#### About technical stack

- What are the main technologies currently used in the backend systems?
- Is the architecture mainly microservices, modular monolith, or a mix of both?
- Are there any major modernization or migration initiatives happening in the team?

#### About quality and delivery

- What quality gates are usually required before production release?
- How does the team approach automated testing, code review, and CI/CD?
- How does the team manage technical debt while delivering business features?

#### About production and reliability

- How is production support handled within the team?
- What observability tools does the team use for logging, monitoring, and tracing?
- For critical banking flows, how does the team ensure reliability and auditability?

#### About fullstack direction

- I heard NAB values fullstack capability. For this team, how much frontend contribution is expected from backend engineers?
- Are there opportunities for backend engineers to gradually contribute to frontend or cloud/platform work?

### Recommended Answer Strategy

Khi interviewer hỏi `Do you have any questions for us?`, không nên nói `No`.

Cách mở đầu:

```text
Yes, thank you. I would like to understand more about the team and the expectations for this role.
```

Chọn 2 - 3 câu hỏi tốt nhất:

1. Role expectation:

```text
For this Senior Java Developer role, what would be the most important expectations in the first 3 to 6 months?
```

2. Technical stack / architecture:

```text
Could you share more about the backend architecture? Is the team mainly working with microservices, or is it a mix of microservices and existing systems?
```

3. Engineering practices:

```text
How does the team approach code quality, automated testing, and production reliability for critical banking services?
```

4. Fullstack:

```text
I understand NAB values fullstack capability. How much frontend contribution is usually expected from backend engineers in this team?
```

### Priority

**Medium**

Phase này thường ngắn nhưng có thể giúp tạo ấn tượng tốt cuối buổi. Câu hỏi tốt cho thấy bạn suy nghĩ như một senior engineer, không chỉ là người tìm việc.

### Suggested Preparation Time

- **30 - 45 phút:** Chọn và luyện 5 câu hỏi.
- **15 phút trước interview:** Xem lại danh sách và chọn câu phù hợp với flow buổi phỏng vấn.

---

## Suggested Study Schedule

### Nếu còn 7 ngày

| Ngày | Nội dung chính | Mục tiêu |
|---|---|---|
| Day 1 | Phase 1 + Core Java | Hoàn thành self-introduction, project script, ôn OOP/Collections/Exception |
| Day 2 | Java concurrency + Stream API | Nắm multithreading, ExecutorService, CompletableFuture, Stream API |
| Day 3 | Spring Boot + REST API | Ôn DI, bean, transaction, validation, exception handling, REST design |
| Day 4 | Microservices + Design Patterns | Ôn service communication, resilience, Saga, Strategy, Factory, Adapter |
| Day 5 | Database + Caching | Ôn SQL optimization, index, transaction, locking, Redis strategy |
| Day 6 | Security + Testing + DevOps | Ôn JWT/OAuth2, Spring Security, unit/integration/E2E, Docker/CI-CD basics |
| Day 7 | System Design + Behavioral mock | Luyện design banking backend flow, luyện STAR stories, mock interview |

### Nếu chỉ còn 3 ngày

| Ngày | Nội dung chính | Mục tiêu |
|---|---|---|
| Day 1 | Phase 1 + Core Java + Spring Boot | Nói tốt project, ôn topic xác suất hỏi cao nhất |
| Day 2 | REST + Microservices + Database + Security | Cover backend senior core topics |
| Day 3 | Behavioral + System Design + Q&A | Luyện STAR, banking design, câu hỏi cho interviewer |

### Nếu chỉ còn 1 ngày

Ưu tiên theo thứ tự:

1. Self-introduction và project sharing.
2. Core Java: OOP, Collections, Exception, Multithreading.
3. Spring Boot: DI, REST, transaction, exception handling.
4. Database: index, transaction, SQL optimization.
5. Security: authentication vs authorization, JWT, 401 vs 403.
6. Behavioral STAR stories.
7. Q&A questions.

---

## Final Checklist Before Interview

### Phase 1 Checklist

- [ ] Tôi có thể giới thiệu bản thân bằng tiếng Anh trong 2 phút.
- [ ] Tôi đã chọn 1 project hiểu sâu nhất để chia sẻ.
- [ ] Tôi có thể giải thích business context của project.
- [ ] Tôi có thể vẽ/giải thích architecture đơn giản của project.
- [ ] Tôi có thể nói rõ role và contribution của mình.
- [ ] Tôi có 2 - 3 technical challenges và solutions từ project.
- [ ] Tôi đã luyện nói project sharing thành tiếng ít nhất 3 lần.

### Phase 2 Checklist

- [ ] Tôi giải thích được OOP, SOLID và ví dụ thực tế.
- [ ] Tôi hiểu HashMap, equals/hashCode, List/Set/Map.
- [ ] Tôi phân biệt được checked và unchecked exception.
- [ ] Tôi biết Stream API cơ bản: map/filter/flatMap/reduce/collect.
- [ ] Tôi giải thích được race condition, deadlock, ExecutorService, CompletableFuture.
- [ ] Tôi hiểu Dependency Injection, bean, auto-configuration trong Spring Boot.
- [ ] Tôi biết cách design REST API tốt, status code, idempotency, versioning.
- [ ] Tôi hiểu microservices trade-off, timeout, retry, circuit breaker, eventual consistency.
- [ ] Tôi có ví dụ dùng ít nhất 2 design patterns trong backend.
- [ ] Tôi biết cách troubleshoot slow SQL query.
- [ ] Tôi hiểu index, transaction ACID, isolation level, optimistic/pessimistic locking.
- [ ] Tôi biết cache-aside, TTL, invalidation, Redis use cases.
- [ ] Tôi phân biệt authentication và authorization.
- [ ] Tôi hiểu JWT, OAuth2 basic, access token vs refresh token.
- [ ] Tôi biết basic security: HTTPS, CORS, SQL injection, sensitive logging.
- [ ] Tôi có testing mindset: unit, integration, E2E, test pyramid.
- [ ] Tôi có thể design basic banking backend flow như payment transfer hoặc transaction history.
- [ ] Tôi hiểu Docker và CI/CD flow cơ bản.

### Phase 3 Checklist

- [ ] Tôi có STAR story cho conflict với developer cùng level.
- [ ] Tôi có câu trả lời cho conflict với tech lead.
- [ ] Tôi có câu trả lời cho deadline gấp vs code quality.
- [ ] Tôi có câu trả lời cho requirement chưa rõ.
- [ ] Tôi có câu chuyện production issue hoặc severe bug.
- [ ] Tôi có ví dụ học công nghệ mới.
- [ ] Tôi có câu trả lời tích cực về fullstack.
- [ ] Tôi có ví dụ collaboration với BA/QA/PO/team quốc tế.
- [ ] Tôi tránh blame người khác trong mọi câu trả lời.
- [ ] Tôi luôn kết thúc bằng result hoặc lesson learned.

### Phase 4 Checklist

- [ ] Tôi đã chuẩn bị ít nhất 5 câu hỏi cho interviewer.
- [ ] Tôi ưu tiên hỏi về expectation, team structure, architecture, quality process.
- [ ] Tôi có câu hỏi về fullstack direction tại NAB.
- [ ] Tôi không kết thúc bằng `No questions`.

### Final Mindset

- [ ] Trả lời ngắn gọn trước, sau đó mở rộng nếu interviewer hỏi thêm.
- [ ] Nếu không biết, thừa nhận ngắn gọn và nói cách mình sẽ tìm hiểu.
- [ ] Luôn liên hệ câu trả lời với production, security, reliability, maintainability.
- [ ] Với banking domain, nhấn mạnh data correctness, auditability và customer trust.
- [ ] Giữ thái độ calm, collaborative và senior-level ownership.

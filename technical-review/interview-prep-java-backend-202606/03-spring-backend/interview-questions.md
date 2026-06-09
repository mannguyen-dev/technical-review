# Spring Backend — Tổng hợp câu hỏi phỏng vấn kèm câu trả lời

## Spring Boot

### Easy

#### 1. Spring Boot là gì?

**Trả lời:** Spring Boot là framework giúp tạo ứng dụng Spring nhanh hơn bằng auto-configuration, starter dependencies, embedded server và cấu hình production-ready như Actuator.

#### 2. `@SpringBootApplication` gồm gì?

**Trả lời:** Gồm `@Configuration`, `@EnableAutoConfiguration` và `@ComponentScan`.

#### 3. Starter dependency là gì?

**Trả lời:** Starter là dependency bundle cho một use case. Ví dụ `spring-boot-starter-web` kéo Spring MVC, Jackson, validation, embedded Tomcat.

### Medium

#### 1. Auto-configuration hoạt động thế nào?

**Trả lời:** Spring Boot kiểm tra classpath, properties và bean hiện có để tự cấu hình bean mặc định. Nếu developer tự định nghĩa bean, auto-config thường back off.

#### 2. Profile dùng để làm gì?

**Trả lời:** Profile tách config theo môi trường như dev, test, staging, prod. Ví dụ DB URL, log level, feature flag. Sensitive config nên dùng env/secrets, không hardcode.

#### 3. Actuator dùng trong production như thế nào?

**Trả lời:** Actuator cung cấp health check, metrics, info, prometheus endpoint. Dùng cho readiness/liveness, monitoring, alerting và troubleshooting.

### Hard

#### 1. Startup chậm điều tra thế nào?

**Trả lời:** Kiểm tra dependency quá nhiều, component scan rộng, bean init gọi network/DB, migration startup, logging, classpath, auto-config report. Có thể dùng actuator startup endpoint hoặc profiling.

#### 2. Khi nào microservice không phải lựa chọn tốt?

**Trả lời:** Khi domain chưa rõ boundary, team nhỏ, deployment/observability chưa trưởng thành, consistency cross-service phức tạp, hoặc monolith/modular monolith vẫn đáp ứng tốt.

#### 3. Làm sao organize codebase theo Clean Architecture/DDD?

**Trả lời:** Tách domain model/business rules khỏi framework. Có layers như domain, application/use case, adapter/infrastructure, web. Dependency hướng vào domain. Repository/client là interface ở domain/application và implementation ở infrastructure.

## Dependency Injection

### Easy

#### 1. IoC và DI là gì?

**Trả lời:** IoC là framework quản lý object lifecycle thay vì object tự tạo dependency. DI là cách inject dependency từ bên ngoài vào class.

#### 2. Bean là gì?

**Trả lời:** Bean là object được Spring container tạo, quản lý lifecycle và inject vào nơi cần dùng.

#### 3. `@Component`, `@Service`, `@Repository` khác nhau thế nào?

**Trả lời:** Cả ba đều đăng ký bean. `@Service` biểu thị business service, `@Repository` biểu thị persistence layer và hỗ trợ exception translation, `@Component` là generic stereotype.

### Medium

#### 1. Vì sao constructor injection tốt hơn field injection?

**Trả lời:** Constructor injection làm dependency rõ ràng, hỗ trợ `final`, dễ unit test, fail fast khi thiếu dependency và tránh object ở trạng thái chưa đủ dependency.

#### 2. Bean scope singleton có rủi ro gì?

**Trả lời:** Singleton bean dùng chung nhiều request thread. Nếu chứa mutable shared state, có thể race condition. Service nên stateless hoặc bảo vệ state đúng cách.

#### 3. Có nhiều implementation thì inject thế nào?

**Trả lời:** Dùng `@Qualifier`, `@Primary`, inject `Map<String, Interface>` cho strategy pattern, hoặc factory chọn implementation theo business type.

### Hard

#### 1. Circular dependency xử lý thế nào?

**Trả lời:** Tốt nhất refactor design, tách responsibility hoặc dùng event/domain service. `@Lazy` chỉ là workaround, không nên là giải pháp chính.

#### 2. Bean lifecycle gồm những bước nào?

**Trả lời:** Instantiate → populate dependencies → BeanPostProcessor before init → `@PostConstruct`/init → ready → destroy callback/`@PreDestroy` khi shutdown.

#### 3. DI giúp unit test như thế nào?

**Trả lời:** Class phụ thuộc abstraction nên có thể inject mock/stub trong test, test business logic độc lập khỏi DB/external services.

## REST API

### Easy

#### 1. REST là gì?

**Trả lời:** REST là architectural style thiết kế API quanh resource, dùng HTTP method/status code, stateless request và representation như JSON.

#### 2. PUT vs PATCH?

**Trả lời:** PUT thường replace toàn bộ resource và idempotent. PATCH update một phần resource.

#### 3. 401 vs 403?

**Trả lời:** 401 là chưa authenticated hoặc token không hợp lệ. 403 là đã authenticated nhưng không có quyền.

### Medium

#### 1. Error handling API thiết kế thế nào?

**Trả lời:** Dùng global exception handler `@RestControllerAdvice`, error response thống nhất gồm code/message/traceId/details, mapping HTTP status đúng và không expose stack trace.

#### 2. Pagination và sorting nên làm thế nào?

**Trả lời:** Dùng query params như `page`, `size`, `sort`, giới hạn max size, trả metadata total/hasNext nếu cần. Với data lớn có thể dùng cursor/keyset pagination.

#### 3. Khi nào dùng 409 Conflict?

**Trả lời:** Khi request hợp lệ về syntax nhưng conflict với state hiện tại, ví dụ duplicate idempotency key với payload khác, version conflict, account đã closed.

### Hard

#### 1. Idempotency cho POST API xử lý thế nào?

**Trả lời:** Client gửi `Idempotency-Key`. Server lưu key, request hash, status/result. Request lặp cùng key/payload trả result cũ; cùng key khác payload trả 409. Lưu bằng unique constraint/atomic insert.

#### 2. API versioning cho client cũ như thế nào?

**Trả lời:** Có thể version qua URL `/api/v1`, header hoặc media type. Giữ backward compatibility, deprecate có lộ trình, không break contract đột ngột.

#### 3. Timeout/retry khi gọi downstream service thiết kế thế nào?

**Trả lời:** Set timeout ngắn, retry có giới hạn/backoff/jitter cho lỗi transient, không retry lỗi 4xx validation, dùng circuit breaker/fallback nếu cần, log correlationId và metrics.

## JPA/Hibernate

### Easy

#### 1. JPA và Hibernate khác nhau thế nào?

**Trả lời:** JPA là specification/standard ORM API. Hibernate là implementation phổ biến của JPA.

#### 2. Entity là gì?

**Trả lời:** Entity là object map với table trong database, có identity như primary key và lifecycle được persistence context quản lý.

#### 3. Lazy vs eager loading?

**Trả lời:** Lazy load khi truy cập association; eager load ngay khi load entity. Eager dễ gây load thừa; lazy cần chú ý transaction/session.

### Medium

#### 1. N+1 query là gì?

**Trả lời:** Khi load N parent records rồi mỗi parent trigger thêm query lấy child, tạo 1+N queries. Fix bằng fetch join, entity graph, projection hoặc batch size.

#### 2. Persistence context là gì?

**Trả lời:** Persistence context là first-level cache quản lý entity trong một transaction/session, theo dõi dirty checking và đảm bảo identity cho entity managed.

#### 3. Transaction boundary đặt ở đâu?

**Trả lời:** Thường đặt ở service/application layer bao quanh một business use case, không đặt rải rác tùy tiện ở controller.

### Hard

#### 1. Optimistic locking hoạt động thế nào?

**Trả lời:** Dùng field `@Version`. Khi update, Hibernate check version trong DB. Nếu version đã đổi, update fail với optimistic locking exception, tránh lost update.

#### 2. LazyInitializationException xảy ra khi nào?

**Trả lời:** Khi truy cập lazy association sau khi persistence context/session đã đóng. Fix bằng fetch join/projection/transaction boundary phù hợp, không nên dựa vào Open Session in View cho mọi thứ.

#### 3. Vì sao không expose entity trực tiếp ra API?

**Trả lời:** Dễ leak internal fields, gây lazy loading/N+1, coupling API với DB schema và khó kiểm soát response. Nên dùng DTO.

## Testing

### Easy

#### 1. Unit test vs integration test?

**Trả lời:** Unit test kiểm tra một unit nhỏ với dependency mock. Integration test kiểm tra nhiều component thật cùng nhau như Spring context, DB, API.

#### 2. Mockito dùng để làm gì?

**Trả lời:** Mockito dùng để mock dependency, define behavior và verify interaction trong unit test.

#### 3. Code coverage có ý nghĩa gì?

**Trả lời:** Coverage cho biết bao nhiêu code được test chạy qua, nhưng không đảm bảo test đúng/chất lượng. Cần test meaningful assertions và edge cases.

### Medium

#### 1. Test service layer như thế nào?

**Trả lời:** Mock repository/external clients, test business logic, happy path, validation, exception, transaction-related behavior nếu cần integration test.

#### 2. Khi nào dùng `@SpringBootTest`?

**Trả lời:** Khi cần load full Spring context để test integration. Không nên dùng cho mọi unit test vì chậm.

#### 3. SonarQube issue xử lý thế nào?

**Trả lời:** Phân loại severity/type: bug, vulnerability, code smell, duplication, coverage. Ưu tiên security/bug trước, fix root cause thay vì suppress tùy tiện.

### Hard

#### 1. Test event-driven flow thế nào?

**Trả lời:** Unit test producer/consumer logic riêng, integration test với embedded broker/Testcontainers nếu có, verify serialization/schema, retry/DLQ/idempotency và side effects.

#### 2. Test transaction rollback thế nào?

**Trả lời:** Integration test với DB thật/test container, trigger exception trong transactional method, sau đó assert DB không có partial changes.

#### 3. Fix flaky test trên CI như thế nào?

**Trả lời:** Xác định nguyên nhân: timing, shared state, order dependency, async wait, external dependency. Isolate test data, dùng deterministic wait, cleanup state, tránh sleep cứng.

## Reactive Programming / WebFlux

### Easy

#### 1. Reactive Programming là gì?

**Trả lời:** Reactive programming là mô hình xử lý dữ liệu bất đồng bộ dạng stream, non-blocking và hỗ trợ backpressure. Trong Spring dùng WebFlux và Reactor.

#### 2. Mono và Flux khác nhau thế nào?

**Trả lời:** `Mono` phát 0 hoặc 1 item. `Flux` phát 0 đến N item.

#### 3. Spring MVC và Spring WebFlux khác nhau thế nào?

**Trả lời:** MVC blocking/thread-per-request. WebFlux non-blocking/event-loop, phù hợp high concurrency I/O-bound, streaming, WebSocket.

### Medium

#### 1. map và flatMap khác nhau thế nào trong Reactor?

**Trả lời:** `map` dùng khi function trả object thường. `flatMap` dùng khi function trả `Mono`/`Flux`, giúp tránh `Mono<Mono<T>>`.

#### 2. Backpressure là gì và vì sao quan trọng?

**Trả lời:** Backpressure cho phép consumer kiểm soát tốc độ producer để tránh quá tải/OOM khi producer phát nhanh hơn consumer xử lý.

#### 3. Vì sao không nên gọi `.block()` trong reactive pipeline?

**Trả lời:** `.block()` chặn thread, có thể block event loop và làm mất lợi ích non-blocking, giảm throughput hoặc gây deadlock.

#### 4. WebClient khác RestTemplate như thế nào?

**Trả lời:** WebClient là HTTP client non-blocking/reactive. RestTemplate là blocking và đã ở maintenance mode.

### Hard

#### 1. Nếu phải gọi blocking repository trong WebFlux, bạn xử lý thế nào?

**Trả lời:** Bọc bằng `Mono.fromCallable()` và `subscribeOn(Schedulers.boundedElastic())`, nhưng xem là workaround. Nếu blocking nhiều, cân nhắc dùng MVC hoặc reactive driver.

#### 2. WebFlux có luôn nhanh hơn Spring MVC không? Vì sao?

**Trả lời:** Không. WebFlux tốt cho I/O-bound high concurrency. Với CRUD JPA blocking thông thường, MVC có thể đơn giản và hiệu quả hơn.

#### 3. Reactive transaction khác transaction thường như thế nào?

**Trả lời:** Reactive transaction dùng Reactor context, không dựa vào ThreadLocal như transaction imperative. Cần reactive transaction manager như R2DBC.

#### 4. Trong hệ thống WebSocket dual-screen như OCBC, reactive programming giúp gì và cần lưu ý gì?

**Trả lời:** Reactive giúp xử lý nhiều long-lived WebSocket connections hiệu quả. Nhưng backend vẫn phải là source of truth, lưu session/transaction state persistent, xử lý reconnect, idempotency và audit.

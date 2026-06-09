# Reactive Programming với Spring Boot — Kiến thức cần nhớ

## 1. Reactive Programming là gì?

**Reactive Programming** là mô hình lập trình xử lý dữ liệu bất đồng bộ theo dạng stream, nơi hệ thống phản ứng với dữ liệu/event khi chúng xuất hiện.

Trong Java Spring Boot, reactive programming thường gắn với:

- **Spring WebFlux**
- **Project Reactor**
- `Mono<T>`
- `Flux<T>`
- Non-blocking I/O
- Backpressure
- Reactive Streams

Nói ngắn gọn:

> Reactive programming giúp xử lý nhiều request/connections đồng thời với ít thread hơn bằng cách không block thread khi chờ I/O như database, network hoặc external API.

---

## 2. Vì sao cần Reactive Programming?

Trong mô hình Spring MVC truyền thống:

```text
1 request → 1 thread
```

Nếu request gọi DB/external API mất 2 giây, thread bị block trong lúc chờ.

Trong mô hình reactive/non-blocking:

```text
1 event loop thread → xử lý nhiều request
```

Khi chờ I/O, thread không bị block mà có thể xử lý việc khác. Khi I/O hoàn tất, callback/pipeline tiếp tục xử lý.

## Khi reactive hữu ích

Reactive phù hợp khi hệ thống có nhiều I/O-bound operations:

- Gọi nhiều external APIs.
- Streaming data.
- WebSocket.
- Server-Sent Events.
- Long-lived connections.
- High concurrency với request chủ yếu chờ I/O.
- API gateway/proxy/aggregation service.
- Real-time notification/event systems.

## Khi reactive không thật sự cần thiết

Reactive không phải lúc nào cũng tốt hơn.

Không nên dùng chỉ vì “modern” nếu:

- Logic chủ yếu CPU-bound.
- Team chưa quen reactive debugging/testing.
- Database driver vẫn blocking.
- Codebase đang Spring MVC truyền thống và không có concurrency/I/O pressure rõ ràng.
- Cần transaction phức tạp kiểu blocking JPA/Hibernate.

---

## 3. Spring MVC vs Spring WebFlux

| Tiêu chí | Spring MVC | Spring WebFlux |
|---|---|---|
| Programming model | Imperative/blocking | Reactive/non-blocking |
| Thread model | Thread-per-request | Event loop + worker threads |
| Server phổ biến | Tomcat/Servlet | Netty, Servlet 3.1+ |
| Return type | Object, ResponseEntity | Mono, Flux |
| DB thường dùng | JPA/JDBC | R2DBC, reactive MongoDB, reactive Redis |
| Dễ học/debug | Dễ hơn | Khó hơn |
| Phù hợp | CRUD enterprise thông thường | High concurrency, streaming, async I/O |

## Câu trả lời phỏng vấn tốt

> Spring MVC phù hợp với phần lớn ứng dụng CRUD/backend enterprise truyền thống, đặc biệt khi dùng JPA/Hibernate blocking. Spring WebFlux phù hợp hơn khi workload có nhiều I/O non-blocking, cần xử lý concurrency cao, streaming hoặc WebSocket. Không nên mix reactive với blocking driver mà không kiểm soát, vì sẽ mất lợi ích non-blocking.

---

## 4. Project Reactor: Mono và Flux

Spring WebFlux dùng **Project Reactor**.

## Mono

`Mono<T>` đại diện cho stream có **0 hoặc 1 item**.

Ví dụ:

```java
Mono<User> user = userService.findById(userId);
```

Dùng cho:

- Get one record.
- Create/update/delete result.
- Single async response.

## Flux

`Flux<T>` đại diện cho stream có **0 đến N items**.

Ví dụ:

```java
Flux<Order> orders = orderService.findByUserId(userId);
```

Dùng cho:

- List data.
- Streaming data.
- Event stream.

## Quy tắc nhớ nhanh

```text
Mono = 0..1
Flux = 0..N
```

---

## 5. Cold Publisher vs Hot Publisher

## Cold Publisher

Mỗi subscriber sẽ trigger pipeline riêng.

Ví dụ:

```java
Mono<User> mono = userRepository.findById("1");
```

Nếu subscribe 2 lần, có thể query DB 2 lần.

## Hot Publisher

Data phát ra độc lập với subscriber. Subscriber đến sau có thể bỏ lỡ data trước đó.

Ví dụ:

- Live event stream.
- WebSocket event.
- Kafka-like event bridge trong memory.

## Câu hỏi phỏng vấn

> Mono/Flux có chạy ngay khi được tạo không?

Trả lời:

> Không nhất thiết. Reactor pipeline thường lazy. Nó chỉ chạy khi có subscriber. Trong WebFlux, framework sẽ subscribe khi xử lý request/response.

---

## 6. Operator quan trọng cần nhớ

## map

Biến đổi item đồng bộ.

```java
Mono<UserDto> dto = userMono.map(user -> toDto(user));
```

Dùng khi function trả object thường.

## flatMap

Biến đổi bất đồng bộ, function trả `Mono` hoặc `Flux`.

```java
Mono<Order> order = userMono.flatMap(user -> orderService.findLatestOrder(user.id()));
```

Dùng khi gọi tiếp API/DB reactive.

## map vs flatMap

| Operator | Function trả về | Kết quả |
|---|---|---|
| map | `T` | `Mono<R>` |
| flatMap | `Mono<R>` | `Mono<R>` |

Lỗi phổ biến:

```java
Mono<Mono<Order>> wrong = userMono.map(user -> orderService.findLatestOrder(user.id()));
```

Đúng:

```java
Mono<Order> correct = userMono.flatMap(user -> orderService.findLatestOrder(user.id()));
```

## filter

```java
Flux<Order> paidOrders = orders.filter(order -> order.isPaid());
```

## switchIfEmpty

```java
return userRepository.findById(id)
    .switchIfEmpty(Mono.error(new UserNotFoundException(id)));
```

## defaultIfEmpty

```java
Mono<String> name = userMono.map(User::name).defaultIfEmpty("Unknown");
```

## zip

Kết hợp nhiều Mono chạy song song.

```java
Mono<UserProfile> profile = Mono.zip(userMono, orderMono, paymentMono)
    .map(tuple -> new UserProfile(tuple.getT1(), tuple.getT2(), tuple.getT3()));
```

## concatMap vs flatMap

- `flatMap`: xử lý concurrent, không đảm bảo order.
- `concatMap`: xử lý tuần tự, giữ order.

## collectList

Convert Flux thành Mono<List<T>>.

```java
Mono<List<Order>> list = orderFlux.collectList();
```

Cẩn thận: nếu stream lớn, `collectList()` có thể tốn memory.

---

## 7. Error Handling trong Reactor

## onErrorReturn

Trả fallback static value.

```java
return userService.findUser(id)
    .onErrorReturn(new User("unknown"));
```

## onErrorResume

Fallback bằng pipeline khác.

```java
return userService.findUser(id)
    .onErrorResume(ex -> fallbackUserService.findUser(id));
```

## onErrorMap

Map exception.

```java
return paymentService.pay(request)
    .onErrorMap(TimeoutException.class, ex -> new PaymentUnavailableException());
```

## doOnError

Log side-effect, không recover.

```java
return service.call()
    .doOnError(ex -> log.error("Call failed", ex));
```

## retry

```java
return externalClient.call()
    .retry(3);
```

## retryWhen với backoff

```java
return externalClient.call()
    .retryWhen(Retry.backoff(3, Duration.ofMillis(200)));
```

## Lỗi cần tránh

- Retry vô hạn.
- Retry lỗi validation/permanent.
- Swallow error bằng fallback không rõ ràng.
- Log sensitive data.

---

## 8. Backpressure

**Backpressure** là cơ chế để consumer báo với producer rằng nó không xử lý kịp, từ đó producer giảm tốc hoặc buffer/drop theo strategy.

Nếu producer phát quá nhanh còn consumer xử lý chậm:

- Memory tăng.
- Latency tăng.
- App có thể OOM.

## Các strategy thường gặp

- Buffer.
- Drop.
- Latest.
- Error.
- Throttle/rate limit.

Ví dụ:

```java
flux.onBackpressureBuffer(1000)
```

## Câu trả lời phỏng vấn

> Backpressure giúp hệ thống reactive kiểm soát tốc độ dữ liệu giữa producer và consumer. Nó đặc biệt quan trọng với stream lớn hoặc real-time events. Tuy nhiên, strategy phải chọn theo business: buffer có thể tăng memory, drop có thể mất dữ liệu, error cần retry/recovery.

---

## 9. Non-blocking không có nghĩa là nhanh hơn mọi trường hợp

Reactive giúp sử dụng thread hiệu quả hơn khi workload I/O-bound. Nhưng nếu bạn đưa blocking code vào reactive pipeline, lợi ích giảm mạnh.

## Blocking code cần tránh

```java
Thread.sleep(1000);
```

```java
jdbcTemplate.queryForObject(...)
```

```java
jpaRepository.findById(...)
```

```java
restTemplate.getForObject(...)
```

## Thay thế reactive

- WebClient thay cho RestTemplate.
- R2DBC thay cho JDBC/JPA nếu muốn full reactive SQL.
- Reactive MongoDB driver.
- Reactive Redis.

## Nếu bắt buộc gọi blocking code

Có thể offload sang bounded elastic scheduler:

```java
Mono.fromCallable(() -> blockingRepository.findById(id))
    .subscribeOn(Schedulers.boundedElastic());
```

Nhưng đây là workaround, không nên lạm dụng.

---

## 10. Scheduler trong Reactor

Scheduler quyết định pipeline chạy trên thread nào.

## Các scheduler phổ biến

- `immediate`: chạy trên thread hiện tại.
- `single`: một thread duy nhất.
- `parallel`: dùng cho CPU-bound task ngắn, số thread thường bằng số CPU cores.
- `boundedElastic`: dùng cho blocking I/O task cần offload.

## publishOn vs subscribeOn

### subscribeOn

Ảnh hưởng nơi subscription bắt đầu.

```java
mono.subscribeOn(Schedulers.boundedElastic())
```

### publishOn

Chuyển execution context cho các operator phía sau.

```java
mono
  .map(this::step1)
  .publishOn(Schedulers.parallel())
  .map(this::cpuStep)
```

## Câu hỏi phỏng vấn

> subscribeOn và publishOn khác nhau thế nào?

Trả lời ngắn:

> `subscribeOn` ảnh hưởng thread dùng để subscribe upstream, thường đặt gần source. `publishOn` chuyển execution context cho các operator downstream phía sau nó.

---

## 11. WebClient

Trong reactive Spring, dùng `WebClient` để gọi HTTP non-blocking.

```java
Mono<UserDto> user = webClient.get()
    .uri("/users/{id}", id)
    .retrieve()
    .bodyToMono(UserDto.class);
```

## Error handling với WebClient

```java
return webClient.get()
    .uri("/users/{id}", id)
    .retrieve()
    .onStatus(HttpStatusCode::is4xxClientError,
        response -> Mono.error(new ClientException()))
    .onStatus(HttpStatusCode::is5xxServerError,
        response -> Mono.error(new ServerException()))
    .bodyToMono(UserDto.class);
```

## Timeout

```java
return webClient.get()
    .uri("/external")
    .retrieve()
    .bodyToMono(Response.class)
    .timeout(Duration.ofSeconds(2));
```

## Best practices

- Set timeout.
- Handle 4xx/5xx rõ ràng.
- Retry có backoff cho lỗi transient.
- Không `.block()` trong reactive flow.
- Log correlationId.

---

## 12. Reactive Database

## Reactive MongoDB

Spring hỗ trợ reactive MongoDB:

```java
public interface UserRepository extends ReactiveMongoRepository<User, String> {
    Flux<User> findByStatus(String status);
}
```

Phù hợp nếu bạn dùng MongoDB và muốn non-blocking end-to-end.

## R2DBC

R2DBC là reactive driver cho relational database.

Lưu ý:

- Không giống JPA/Hibernate.
- Không có full ORM mạnh như Hibernate.
- Transaction vẫn có nhưng model khác.
- Migration từ JPA sang R2DBC không đơn giản.

## JPA trong WebFlux

JPA là blocking. Nếu dùng JPA trong WebFlux mà không offload, event loop có thể bị block.

Câu trả lời tốt:

> Nếu muốn reactive end-to-end, cần dùng reactive database driver như Reactive MongoDB hoặc R2DBC. Nếu vẫn dùng JPA/JDBC blocking, có thể dùng boundedElastic để offload, nhưng khi đó hệ thống không còn reactive hoàn toàn và cần cân nhắc có đáng dùng WebFlux không.

---

## 13. WebSocket với Spring WebFlux

Reactive programming rất phù hợp với WebSocket vì WebSocket là long-lived, event-driven, bidirectional connection.

Ví dụ use case từ OCBC dual-screen project:

```text
Teller Screen ←→ Backend WebSocket ←→ Customer Screen
```

Các điểm cần nhớ:

- Mỗi session có `sessionId`.
- Backend giữ source of truth cho state.
- Client reconnect cần fetch latest state.
- Message có `messageId`, `type`, `sequence`, `correlationId`.
- Cần timeout, heartbeat/ping-pong.
- Không lưu state quan trọng chỉ trong WebSocket connection memory.

## Spring WebFlux WebSocket handler concept

```java
@Component
public class MyWebSocketHandler implements WebSocketHandler {
    @Override
    public Mono<Void> handle(WebSocketSession session) {
        Flux<WebSocketMessage> input = session.receive();
        Flux<WebSocketMessage> output = ...;
        return session.send(output);
    }
}
```

---

## 14. Testing Reactive Code

Dùng `StepVerifier`.

```java
StepVerifier.create(userService.findById("1"))
    .expectNextMatches(user -> user.getId().equals("1"))
    .verifyComplete();
```

Test error:

```java
StepVerifier.create(userService.findById("missing"))
    .expectError(UserNotFoundException.class)
    .verify();
```

Test Flux:

```java
StepVerifier.create(orderService.findOrders())
    .expectNextCount(3)
    .verifyComplete();
```

## Lỗi test thường gặp

- Gọi `.block()` trong test quá nhiều thay vì StepVerifier.
- Không test error path.
- Không test timeout/retry.

---

## 15. Các lỗi phổ biến khi dùng Reactive

## 1. Gọi `.block()` trong reactive pipeline

Sai:

```java
return userMono.map(user -> externalClient.call(user).block());
```

Đúng:

```java
return userMono.flatMap(user -> externalClient.call(user));
```

## 2. Dùng map thay vì flatMap

Sai:

```java
Mono<Mono<Order>> result = userMono.map(user -> orderService.find(user));
```

Đúng:

```java
Mono<Order> result = userMono.flatMap(user -> orderService.find(user));
```

## 3. Blocking event loop

Sai:

```java
Thread.sleep(1000);
```

## 4. Không subscribe nên pipeline không chạy

Trong service/controller WebFlux, framework subscribe giúp bạn. Nhưng nếu tự tạo pipeline trong background mà không subscribe hoặc không return publisher, nó có thể không chạy.

## 5. Dùng reactive nửa vời

WebFlux + JPA blocking + `.block()` khắp nơi thường làm code phức tạp hơn mà không có lợi ích rõ.

## 6. Không kiểm soát retry

Retry vô hạn có thể làm downstream overload.

---

## 16. Reactive và Transaction

Reactive transaction khác imperative transaction.

Với R2DBC có thể dùng `TransactionalOperator`:

```java
return transactionalOperator.execute(status ->
    accountRepository.debit(from, amount)
        .then(accountRepository.credit(to, amount))
).then();
```

Hoặc annotation nếu config reactive transaction manager phù hợp:

```java
@Transactional
public Mono<Void> process() {
    return repository.save(...).then(...);
}
```

Lưu ý:

- Không dùng blocking transaction manager cho reactive flow.
- Transaction context truyền qua Reactor context, không giống ThreadLocal truyền thống.
- Mixing reactive + blocking transaction dễ gây bug.

---

## 17. Reactive trong Microservices

Reactive hữu ích trong service aggregator:

```java
Mono<User> user = userClient.getUser(id);
Mono<List<Order>> orders = orderClient.getOrders(id).collectList();
Mono<PaymentSummary> payment = paymentClient.getSummary(id);

return Mono.zip(user, orders, payment)
    .map(tuple -> buildProfile(tuple.getT1(), tuple.getT2(), tuple.getT3()));
```

Ưu điểm:

- Gọi nhiều downstream concurrently.
- Không block thread trong lúc chờ.
- Dễ compose timeout/fallback/retry.

Cần chú ý:

- Timeout từng downstream.
- Fallback hợp lý.
- Circuit breaker nếu cần.
- Không retry mọi lỗi.
- Observability/tracing.

---

## 18. Reactive và Project của bạn

## OCBC dual-screen WebSocket

Reactive/WebFlux rất liên quan vì:

- WebSocket là long-lived connection.
- Teller và customer screen cần real-time state sync.
- Có nhiều sessions/counters/branches đồng thời.
- Non-blocking I/O giúp xử lý nhiều connections hiệu quả.

Câu trả lời phỏng vấn:

> Với dual-screen system, reactive programming hoặc WebFlux phù hợp cho WebSocket layer vì connection long-lived và event-driven. Tuy nhiên transaction processing phía sau vẫn cần đảm bảo consistency bằng database transaction/idempotency. Em sẽ tách real-time transport layer khỏi business transaction layer, backend vẫn là source of truth cho state.

## Cox Automotive notification system

Reactive có thể liên quan nếu có:

- Streaming notification status.
- Non-blocking external provider calls.
- API gateway/aggregation.

Nhưng AWS Lambda/SQS/EventBridge không bắt buộc phải dùng Spring reactive.

## Zurich batch jobs

Reactive thường không phải ưu tiên chính nếu batch chủ yếu CPU/DB processing theo schedule. Quan trọng hơn là chunking, checkpoint, idempotent rerun, MongoDB indexes. Nếu dùng reactive MongoDB, có thể giúp non-blocking I/O, nhưng cần cân nhắc complexity.

---

## 19. Câu hỏi phỏng vấn thường gặp

## Easy

### 1. Reactive Programming là gì?

Reactive programming là mô hình xử lý dữ liệu bất đồng bộ dạng stream, non-blocking và hỗ trợ backpressure. Trong Spring Boot, nó thường dùng Spring WebFlux và Project Reactor với `Mono` và `Flux`.

### 2. Mono và Flux khác nhau thế nào?

- Mono: 0 hoặc 1 item.
- Flux: 0 đến N item.

### 3. Spring MVC và WebFlux khác nhau thế nào?

- MVC blocking/thread-per-request.
- WebFlux non-blocking/event-loop.

## Medium

### 4. map và flatMap khác nhau thế nào?

- `map`: function trả object thường.
- `flatMap`: function trả Publisher như Mono/Flux.

### 5. Backpressure là gì?

Backpressure là cơ chế để consumer kiểm soát tốc độ producer, tránh quá tải/memory issue khi producer phát dữ liệu nhanh hơn khả năng xử lý.

### 6. Vì sao không nên gọi `.block()` trong reactive pipeline?

Vì `.block()` làm block thread, có thể block event loop và làm mất lợi ích non-blocking, gây giảm throughput hoặc deadlock trong một số trường hợp.

### 7. Khi nào nên dùng WebClient?

Dùng WebClient để gọi HTTP non-blocking trong ứng dụng reactive, thay cho RestTemplate.

## Hard

### 8. Nếu phải gọi blocking repository trong reactive pipeline, bạn xử lý thế nào?

Có thể bọc bằng `Mono.fromCallable()` và `subscribeOn(Schedulers.boundedElastic())`, nhưng nên xem đây là workaround. Nếu blocking dependency nhiều, cần cân nhắc dùng Spring MVC thay vì WebFlux hoặc chuyển sang reactive driver.

### 9. WebFlux có luôn nhanh hơn Spring MVC không?

Không. WebFlux hiệu quả hơn cho high concurrency I/O-bound workload. Với CRUD thông thường dùng JPA blocking, MVC có thể đơn giản và phù hợp hơn.

### 10. Reactive transaction khác gì transaction thường?

Reactive transaction không dựa trên ThreadLocal giống imperative transaction, mà context truyền qua Reactor context. Cần reactive transaction manager như R2DBC transaction manager hoặc `TransactionalOperator`.

### 11. Làm sao debug reactive pipeline?

- Dùng meaningful operator chain.
- Log với `doOnNext`, `doOnError`, `doFinally`.
- Dùng correlationId/Reactor context.
- Dùng StepVerifier test.
- Tránh chain quá dài khó đọc.

### 12. Trong WebSocket dual-screen system, reactive giúp gì và vẫn cần lưu ý gì?

Reactive giúp xử lý nhiều long-lived WebSocket connections hiệu quả. Nhưng business transaction vẫn cần source of truth ở backend, session state persistent/distributed, idempotency cho customer confirmation, reconnect handling và audit log.

---

## 20. Cheat Sheet nhanh

```text
Reactive Programming = async + non-blocking + stream + backpressure
Spring WebFlux = reactive web framework
Project Reactor = Mono + Flux
Mono = 0..1 item
Flux = 0..N items
map = sync transform
flatMap = async transform returning Mono/Flux
WebClient = non-blocking HTTP client
Backpressure = consumer controls producer speed
.block() = avoid inside reactive pipeline
JPA/JDBC = blocking
R2DBC/Reactive MongoDB = reactive DB drivers
boundedElastic = offload blocking work if unavoidable
subscribeOn = affects subscription/upstream
publishOn = changes downstream execution context
StepVerifier = test reactive stream
```

## 21. Một câu trả lời tổng hợp nên nhớ

> Reactive programming in Spring Boot is mainly about building asynchronous, non-blocking applications using Spring WebFlux and Project Reactor. The key types are Mono for zero-or-one result and Flux for zero-to-many results. It is useful for I/O-bound and high-concurrency workloads such as WebSocket, streaming, API aggregation, or many external calls. However, it is not automatically faster than Spring MVC. To get the benefit, the whole chain should be non-blocking, including HTTP clients and database drivers. If we mix WebFlux with blocking JPA or call `.block()` inside the pipeline, we can lose the benefits and make the system harder to maintain.

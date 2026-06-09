# Java Core — Multithreading và Concurrency

## Mức độ ưu tiên

**Rất cao cho senior**. Dù project dùng Spring/Kafka/Lambda, interviewer vẫn có thể hỏi thread-safety, race condition, ExecutorService.

## Process vs Thread

- Process có memory space riêng.
- Thread chia sẻ memory trong cùng process.
- Thread nhẹ hơn process nhưng dễ gặp shared-state bugs.

## Cách tạo thread

```java
new Thread(() -> doWork()).start();
```

Trong production Java backend, thường dùng `ExecutorService` thay vì tạo thread thủ công.

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(() -> processEvent(event));
```

## synchronized

Dùng để đảm bảo tại một thời điểm chỉ một thread vào critical section.

```java
public synchronized void increment() {
    count++;
}
```

## volatile

Đảm bảo visibility giữa threads, nhưng không đảm bảo atomicity.

```java
private volatile boolean running = true;
```

`volatile int count; count++` vẫn không thread-safe.

## Race condition

Xảy ra khi nhiều thread đọc/ghi shared state không đồng bộ.

Ví dụ: hai request cùng update balance nếu không lock/transaction đúng.

## Deadlock

Hai hoặc nhiều thread chờ lock lẫn nhau.

Cách giảm rủi ro:

- Lock theo thứ tự cố định.
- Giữ lock trong thời gian ngắn.
- Dùng timeout nếu có thể.

## ConcurrentHashMap

Dùng khi cần map thread-safe với concurrent read/write.

Nhưng trong backend service, nên ưu tiên stateless service. Nếu cần cache, cân nhắc Caffeine/Redis thay vì tự build map phức tạp.

## CompletableFuture

Dùng để xử lý async tasks.

```java
CompletableFuture<User> userFuture = CompletableFuture.supplyAsync(() -> userService.getUser(id));
CompletableFuture<Order> orderFuture = CompletableFuture.supplyAsync(() -> orderService.getOrder(id));
CompletableFuture.allOf(userFuture, orderFuture).join();
```

## Câu hỏi phỏng vấn

### Easy

1. Thread khác process thế nào?
2. synchronized dùng để làm gì?
3. volatile dùng để làm gì?

### Medium

1. Race condition là gì? Cho ví dụ.
2. ExecutorService tốt hơn tạo Thread thủ công ở điểm nào?
3. ConcurrentHashMap khác HashMap thế nào?

### Hard

1. volatile có thay thế synchronized được không?
2. Làm sao debug deadlock?
3. Trong Spring Boot, singleton bean có thread-safe không?
4. Nếu nhiều consumer xử lý cùng một event, làm sao đảm bảo idempotency?

## Gợi ý trả lời theo backend

> Trong Spring Boot, service bean mặc định là singleton và được nhiều request thread gọi đồng thời. Vì vậy em tránh lưu mutable state trong service. Nếu bắt buộc dùng shared state như cache local, em dùng thread-safe structure hoặc cache library, đồng thời cân nhắc Redis để share state giữa nhiều instance.

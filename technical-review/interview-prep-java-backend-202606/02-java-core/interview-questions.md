# Java Core — Tổng hợp câu hỏi phỏng vấn kèm câu trả lời

## OOP

### Easy

#### 1. OOP là gì? 4 tính chất chính?

**Trả lời:** OOP là lập trình hướng đối tượng, tổ chức code quanh object gồm data và behavior. 4 tính chất chính là encapsulation, inheritance, polymorphism và abstraction.

**Ý cần nhấn mạnh:** Trong backend, OOP giúp mô hình hóa domain, tách responsibility và làm code dễ maintain/test.

#### 2. Encapsulation giúp gì trong backend domain model?

**Trả lời:** Encapsulation che giấu state bên trong object và chỉ cho phép thay đổi qua method có rule rõ ràng. Ví dụ `Account.withdraw()` kiểm tra số tiền hợp lệ và balance trước khi trừ tiền.

**Tránh:** Expose setter tùy tiện cho các field quan trọng như balance/status.

#### 3. Overloading và overriding khác nhau thế nào?

**Trả lời:** Overloading là cùng tên method nhưng khác parameter trong cùng class; quyết định lúc compile-time. Overriding là subclass định nghĩa lại method của parent/interface; quyết định lúc runtime theo polymorphism.

### Medium

#### 1. Interface và abstract class khác nhau thế nào?

**Trả lời:** Interface định nghĩa contract, một class có thể implement nhiều interface. Abstract class dùng khi muốn chia sẻ behavior/state chung, nhưng Java chỉ cho extend một class.

**Ví dụ:** `NotificationSender` nên là interface; `BaseBatchJob` có logic chung có thể là abstract class.

#### 2. Composition over inheritance nghĩa là gì?

**Trả lời:** Ưu tiên kết hợp object qua dependency thay vì kế thừa sâu. Composition giảm coupling, dễ test và dễ thay đổi behavior.

**Ví dụ:** `PaymentService` dùng `FeeCalculator` thay vì kế thừa nhiều loại payment service.

#### 3. SOLID principle nào bạn dùng nhiều nhất?

**Trả lời gợi ý:** Single Responsibility và Dependency Inversion. Service nên có responsibility rõ; high-level logic phụ thuộc interface, không phụ thuộc implementation cụ thể.

**Liên hệ CV:** Trong Spring Boot, inject repository/client/publisher qua interface giúp unit test bằng Mockito và thay implementation dễ hơn.

### Hard

#### 1. Thiết kế notification sender hỗ trợ email/SMS/push sao cho dễ mở rộng.

**Trả lời:** Tạo interface `NotificationSender`, mỗi channel là một implementation: `EmailSender`, `SmsSender`, `PushSender`. Dùng factory/strategy map chọn sender theo channel. Request flow: validate → resolve sender → send → update status/audit.

**Điểm senior:** Thêm idempotency, retry, provider error mapping, rate limit và metrics theo channel.

#### 2. Trong DDD, entity và value object khác nhau thế nào?

**Trả lời:** Entity có identity riêng và vòng đời độc lập, ví dụ `Transaction` có transactionId. Value object được định nghĩa bởi giá trị, immutable, không có identity riêng, ví dụ `Money(amount, currency)` hoặc `Address`.

**Tránh:** Dùng primitive string/number cho mọi domain concept quan trọng.

#### 3. Làm sao refactor service class quá lớn?

**Trả lời:** Xác định responsibility, tách validation, domain logic, integration, persistence, notification/audit thành component riêng. Dùng interface cho dependency, viết test trước/sau refactor, refactor từng bước để tránh thay đổi behavior.

## Collections

### Easy

#### 1. List/Set/Map khác nhau thế nào?

**Trả lời:** List có thứ tự và cho phép duplicate. Set không cho duplicate. Map lưu key-value, key là unique.

#### 2. ArrayList và LinkedList khác nhau thế nào?

**Trả lời:** ArrayList truy cập theo index nhanh O(1), phù hợp đa số use case. LinkedList insert/delete ở giữa về lý thuyết tốt khi đã có node, nhưng truy cập O(n) và overhead memory lớn, ít dùng hơn trong backend thông thường.

#### 3. HashSet dựa vào gì để loại duplicate?

**Trả lời:** HashSet dựa vào `hashCode()` để tìm bucket và `equals()` để xác định object có thật sự trùng không.

### Medium

#### 1. HashMap hoạt động thế nào?

**Trả lời:** HashMap dùng array of buckets. Key được tính hash để chọn bucket. Nếu collision, Java dùng linked list hoặc tree bin khi collision nhiều. Khi size vượt threshold theo load factor, HashMap resize.

#### 2. equals/hashCode contract là gì?

**Trả lời:** Nếu `a.equals(b)` true thì `a.hashCode()` phải bằng `b.hashCode()`. HashCode bằng nhau chưa chắc equals true. Nếu dùng object làm key trong HashMap/HashSet, phải override đúng cả hai.

#### 3. Khi nào dùng ConcurrentHashMap?

**Trả lời:** Khi nhiều thread cùng đọc/ghi map. Ví dụ cache local hoặc registry trong service multi-thread. Tuy nhiên trong Spring singleton service nên tránh mutable shared state nếu không thật sự cần.

### Hard

#### 1. Vì sao HashMap không thread-safe?

**Trả lời:** Nhiều thread update cùng lúc có thể gây lost update, inconsistent internal structure hoặc lỗi trong quá trình resize. Với multi-thread, dùng ConcurrentHashMap hoặc đồng bộ bên ngoài.

#### 2. Resize của HashMap ảnh hưởng performance thế nào?

**Trả lời:** Resize tạo bucket array mới và rehash/move entries, tốn CPU. Nếu biết trước size lớn, nên set initial capacity phù hợp để giảm resize.

#### 3. Nếu xử lý batch 100K records, bạn dùng collection thế nào để tránh O(n²)?

**Trả lời:** Convert list lookup sang `Map<key, value>` hoặc `Set<key>` để tra cứu O(1), group data bằng `Map<K, List<V>>`, xử lý theo chunk để tránh memory lớn, và tránh nested loop không cần thiết.

## Exception

### Easy

#### 1. Checked vs unchecked exception?

**Trả lời:** Checked exception bắt buộc catch/throws, ví dụ IOException. Unchecked exception kế thừa RuntimeException, không bắt buộc catch, thường dùng cho programming error hoặc business exception trong Spring.

#### 2. throw vs throws?

**Trả lời:** `throw` dùng để ném exception cụ thể trong method. `throws` khai báo method có thể ném exception.

#### 3. finally có luôn chạy không?

**Trả lời:** Thường có, dù try/catch return hoặc throw. Nhưng không chạy nếu JVM bị kill, `System.exit()`, crash nghiêm trọng hoặc process bị terminate.

### Medium

#### 1. REST API error response nên thiết kế thế nào?

**Trả lời:** Nên có format thống nhất gồm `code`, `message`, `traceId`, optional `details`. Dùng HTTP status phù hợp và `@RestControllerAdvice` để map exception tập trung.

#### 2. Khi nào dùng custom exception?

**Trả lời:** Khi cần biểu diễn business error rõ ràng như `AccountClosedException`, `InsufficientBalanceException`, `NotificationAlreadyProcessedException`, giúp mapping error code/status dễ hơn.

#### 3. Spring transaction rollback với exception nào?

**Trả lời:** Mặc định rollback với unchecked exception (`RuntimeException`) và `Error`, không rollback checked exception trừ khi cấu hình `rollbackFor`.

### Hard

#### 1. Catch exception trong `@Transactional` method có thể gây bug gì?

**Trả lời:** Nếu catch exception rồi không throw lại, Spring có thể xem method thành công và commit transaction, dù business operation đã fail. Nếu cần rollback, throw lại exception hoặc mark rollback-only.

#### 2. Retry exception trong event processing cần lưu ý gì?

**Trả lời:** Phân biệt transient và permanent errors, retry có giới hạn/backoff, dùng DLQ cho poison message, đảm bảo idempotency để retry không tạo side effect trùng.

#### 3. Làm sao tránh log sensitive data khi exception?

**Trả lời:** Không log full request/payload chứa PII, account/card/token. Mask dữ liệu nhạy cảm, log correlationId/transactionId/errorCode đủ debug và kiểm soát access log.

## Multithreading

### Easy

#### 1. Thread và process khác nhau thế nào?

**Trả lời:** Process có memory space riêng; thread chạy trong process và chia sẻ memory với thread khác cùng process. Thread nhẹ hơn nhưng dễ gặp race condition khi share state.

#### 2. synchronized dùng để làm gì?

**Trả lời:** `synchronized` đảm bảo tại một thời điểm chỉ một thread vào critical section và cung cấp visibility cho thay đổi dữ liệu.

#### 3. volatile dùng để làm gì?

**Trả lời:** `volatile` đảm bảo visibility giữa threads cho biến, nhưng không đảm bảo atomicity cho thao tác như `count++`.

### Medium

#### 1. Race condition là gì?

**Trả lời:** Race condition xảy ra khi kết quả phụ thuộc vào timing giữa các thread cùng đọc/ghi shared data. Ví dụ hai request cùng update balance mà không lock/transaction đúng.

#### 2. ExecutorService dùng để làm gì?

**Trả lời:** Quản lý thread pool và submit task async thay vì tạo thread thủ công. Giúp kiểm soát số thread, lifecycle và queue task.

#### 3. Deadlock là gì và tránh thế nào?

**Trả lời:** Deadlock xảy ra khi các thread giữ lock và chờ lock lẫn nhau. Tránh bằng thứ tự lock cố định, giữ lock ngắn, timeout, giảm nested locks.

### Hard

#### 1. volatile có đảm bảo atomicity không?

**Trả lời:** Không. `volatile` chỉ đảm bảo visibility và ordering nhất định. `count++` vẫn gồm read-modify-write nên cần synchronized, Lock hoặc AtomicInteger.

#### 2. Singleton Spring bean có thread-safe không?

**Trả lời:** Singleton chỉ nghĩa là một instance trong container. Thread-safe hay không phụ thuộc bean có mutable shared state không. Stateless service thường an toàn; state mutable cần bảo vệ.

#### 3. Idempotency trong concurrent event processing xử lý thế nào?

**Trả lời:** Dùng eventId/idempotency key, atomic insert/update với unique constraint hoặc conditional write, xử lý state transition trong transaction, duplicate event trả cùng result hoặc skip.

## JVM/Performance

### Easy

#### 1. Heap và stack khác nhau thế nào?

**Trả lời:** Heap chứa object dùng chung bởi threads và được GC quản lý. Stack là vùng nhớ riêng của mỗi thread chứa frame method, local variables và call stack.

#### 2. GC là gì?

**Trả lời:** Garbage Collection tự động giải phóng object không còn được reference, giúp quản lý memory nhưng có thể tạo pause ảnh hưởng latency.

#### 3. Memory leak trong Java có thể xảy ra không?

**Trả lời:** Có. Dù có GC, leak xảy ra khi object không còn cần nhưng vẫn bị reference, ví dụ static collection, cache không eviction, ThreadLocal không clear.

### Medium

#### 1. Làm sao điều tra API chậm?

**Trả lời:** Xác định endpoint và p95/p99 latency, kiểm tra logs/tracing, DB query time, external call, thread/connection pool, CPU/memory, recent deployment/config change. Tối ưu dựa trên bottleneck đo được.

#### 2. N+1 query ảnh hưởng performance ra sao?

**Trả lời:** ORM chạy 1 query lấy parent rồi N query lấy child, gây nhiều round-trip DB và latency cao. Fix bằng fetch join, entity graph, DTO projection hoặc batch size.

#### 3. Cache có rủi ro gì?

**Trả lời:** Stale data, invalidation phức tạp, memory pressure, cache stampede, consistency issue. Với banking không nên cache dữ liệu financial critical nếu không có strategy rõ.

### Hard

#### 1. ThreadLocal gây memory leak như thế nào?

**Trả lời:** Trong thread pool, thread sống lâu. Nếu ThreadLocal giữ object lớn và không remove, data có thể tồn tại qua nhiều request và không được GC.

#### 2. GC pause ảnh hưởng latency ra sao?

**Trả lời:** Một số GC phase dừng application threads, làm request latency tăng, đặc biệt p99. Cần monitor heap, allocation rate, GC logs và chọn/tune GC phù hợp.

#### 3. Bạn optimize service high throughput như thế nào?

**Trả lời:** Đo bottleneck trước, tối ưu DB/index/query, giảm blocking/external latency, dùng connection pool đúng, cache reference data, async/event-driven cho task lâu, batch/bulk operations, scale horizontal và monitor p95/p99/error rate.

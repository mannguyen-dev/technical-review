# Spring — Dependency Injection và IoC

## Mức độ ưu tiên

**Rất cao**. DI là nền tảng của Spring và liên quan tới testability/clean architecture.

## IoC là gì?

Inversion of Control nghĩa là object không tự tạo dependency mà framework quản lý lifecycle và inject dependency.

## Dependency Injection là gì?

DI là cách cung cấp dependency cho class từ bên ngoài.

Ví dụ:

```java
@Service
public class OrderService {
    private final OrderRepository orderRepository;

    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }
}
```

## Constructor Injection vs Field Injection

Nên ưu tiên constructor injection.

| Tiêu chí | Constructor Injection | Field Injection |
|---|---|---|
| Immutability | Tốt với `final` | Không |
| Testability | Dễ unit test | Khó hơn |
| Required dependencies | Rõ ràng | Ẩn |
| Khuyến nghị | Nên dùng | Hạn chế |

## Bean scope

- `singleton`: mặc định, một instance trong Spring container.
- `prototype`: tạo instance mới mỗi lần request bean.
- `request`, `session`: web scopes.

Điểm cần nhớ: singleton bean được nhiều request thread sử dụng, nên tránh mutable shared state.

## Bean lifecycle ngắn gọn

1. Instantiate.
2. Populate dependencies.
3. BeanPostProcessor before initialization.
4. `@PostConstruct`.
5. Bean ready.
6. `@PreDestroy` khi shutdown.

## Circular dependency

Xảy ra khi A phụ thuộc B, B phụ thuộc A.

Cách xử lý:

- Refactor design.
- Tách responsibility.
- Dùng event/domain service.
- Tránh dùng `@Lazy` như giải pháp chính.

## Câu hỏi phỏng vấn

### Easy

1. DI là gì?
2. Bean là gì?
3. `@Component`, `@Service`, `@Repository` khác nhau thế nào?

### Medium

1. Vì sao nên dùng constructor injection?
2. Bean scope singleton có rủi ro gì?
3. Circular dependency xử lý thế nào?

### Hard

1. Nếu có nhiều implementation của một interface, inject thế nào?
2. Spring quản lý bean lifecycle ra sao?
3. DI giúp gì cho clean architecture và unit testing?

## Gợi ý trả lời mạnh

> Em thường dùng constructor injection vì dependency rõ ràng, hỗ trợ immutable field và dễ viết unit test. Với nhiều implementation, em dùng `@Qualifier` hoặc thiết kế strategy map nếu cần chọn implementation theo business type. Em cũng tránh state mutable trong singleton bean để đảm bảo thread-safety.

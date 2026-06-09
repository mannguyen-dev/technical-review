# Java Core — OOP

## Mức độ ưu tiên

**Rất cao**. Vì vị trí Java SE/Java Backend thường bắt đầu bằng OOP. Với kinh nghiệm senior, bạn cần giải thích được concept và áp dụng trong Spring/backend design.

## Khái niệm chính

### Encapsulation — Đóng gói

Ẩn dữ liệu nội bộ, chỉ expose behavior cần thiết qua method.

Ví dụ:

```java
public class Account {
    private BigDecimal balance;

    public void withdraw(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        if (balance.compareTo(amount) < 0) {
            throw new InsufficientBalanceException();
        }
        balance = balance.subtract(amount);
    }
}
```

Điểm phỏng vấn: không nên expose setter tùy tiện cho domain object quan trọng như Account.

### Inheritance — Kế thừa

Cho phép class con tái sử dụng behavior của class cha. Nhưng trong backend hiện đại nên cẩn thận vì inheritance sâu dễ khó maintain.

Nên nói:

> Em ưu tiên composition hơn inheritance nếu quan hệ không thật sự là “is-a”.

### Polymorphism — Đa hình

Cùng interface, nhiều implementation khác nhau.

Ví dụ thực tế:

```java
public interface NotificationSender {
    void send(Notification notification);
}

public class EmailSender implements NotificationSender { ... }
public class SmsSender implements NotificationSender { ... }
```

Trong Spring, có thể inject theo interface để dễ test và mở rộng.

### Abstraction — Trừu tượng hóa

Che giấu chi tiết implementation, expose contract.

Ví dụ: `PaymentGateway`, `MessagePublisher`, `UserRepository`.

## Interface vs Abstract Class

| Tiêu chí | Interface | Abstract class |
|---|---|---|
| Mục đích | Định nghĩa contract | Chia sẻ behavior/state chung |
| Multiple inheritance | Có thể implement nhiều interface | Chỉ extend một class |
| State | Không giữ instance state trực tiếp | Có thể giữ state |
| Khi dùng | Service contract, strategy | Base class có logic chung |

## SOLID cần nhớ

### S — Single Responsibility

Mỗi class nên có một lý do để thay đổi.

Ví dụ không tốt: `UserService` vừa validate, save DB, send email, write audit.

Cải thiện: tách `UserValidator`, `UserRepository`, `NotificationService`, `AuditService`.

### O — Open/Closed

Mở rộng bằng implementation mới, hạn chế sửa code cũ.

Ví dụ: thêm `PushNotificationSender` thay vì sửa if/else lớn.

### L — Liskov Substitution

Class con phải thay thế được class cha mà không phá behavior.

### I — Interface Segregation

Interface nhỏ, đúng nhu cầu client.

### D — Dependency Inversion

High-level module phụ thuộc abstraction, không phụ thuộc implementation.

## Câu hỏi phỏng vấn

### Easy

1. OOP có 4 tính chất nào?
2. Interface khác abstract class như thế nào?
3. Overloading và overriding khác nhau thế nào?

### Medium

1. Khi nào nên dùng composition thay vì inheritance?
2. SOLID giúp ích gì trong microservices/Spring Boot?
3. Bạn đã áp dụng design pattern nào trong project?

### Hard

1. Nếu một service có quá nhiều responsibility, bạn refactor như thế nào?
2. Trong DDD, entity khác value object thế nào?
3. Làm sao thiết kế notification sender dễ mở rộng cho email/SMS/push?

## Gợi ý trả lời mạnh

> Trong các project Spring Boot, em thường áp dụng OOP thông qua việc tách layer và abstraction rõ ràng. Ví dụ controller chỉ handle HTTP request/response, service chứa business logic, repository chịu trách nhiệm persistence. Với các phần có nhiều implementation như message publisher hoặc notification sender, em dùng interface để giảm coupling và dễ unit test bằng Mockito.

# Java Core — Exception Handling

## Mức độ ưu tiên

**Cao**. Exception liên quan trực tiếp đến REST API, transaction rollback và reliability.

## Checked vs Unchecked Exception

### Checked exception

- Kế thừa `Exception` nhưng không phải `RuntimeException`.
- Bắt buộc handle hoặc throws.
- Ví dụ: `IOException`, `SQLException`.

### Unchecked exception

- Kế thừa `RuntimeException`.
- Không bắt buộc handle.
- Ví dụ: `NullPointerException`, `IllegalArgumentException`.

Trong Spring Boot business logic, custom exception thường là unchecked để code gọn và xử lý tập trung bằng `@ControllerAdvice`.

## Best practices

- Không catch `Exception` quá rộng nếu không cần.
- Không swallow exception.
- Log exception đủ context, không log sensitive data.
- Dùng custom exception cho business error.
- REST API nên trả error response nhất quán.

## Spring Transaction và Exception

Mặc định `@Transactional` rollback với unchecked exception, không rollback với checked exception trừ khi cấu hình:

```java
@Transactional(rollbackFor = Exception.class)
public void process() { ... }
```

Điểm hay bị hỏi: nếu catch exception bên trong transactional method và không throw lại, transaction có thể commit.

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("NOT_FOUND", ex.getMessage()));
    }
}
```

## Câu hỏi phỏng vấn

### Easy

1. Checked và unchecked exception khác nhau thế nào?
2. finally có luôn chạy không?
3. throw và throws khác nhau thế nào?

### Medium

1. Bạn thiết kế error response cho REST API như thế nào?
2. Trong Spring transaction, exception nào trigger rollback mặc định?
3. Khi nào tạo custom exception?

### Hard

1. Nếu một method `@Transactional` catch exception rồi return false, transaction rollback không?
2. Làm sao xử lý exception trong async/event-driven system?
3. Retry exception thế nào để tránh duplicate processing?

## Gợi ý trả lời mạnh

> Với REST API, em thường không handle exception rải rác trong controller mà dùng `@RestControllerAdvice` để mapping business exception sang HTTP status và error code thống nhất. Với transaction, em chú ý rollback behavior của Spring, đặc biệt là unchecked exception và trường hợp catch exception bên trong method làm transaction không rollback như mong muốn.

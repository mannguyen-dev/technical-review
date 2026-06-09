# REST API Design

## Mức độ ưu tiên

**Rất cao**. Java backend interview gần như chắc chắn hỏi REST API.

## REST principles cơ bản

- Resource-based URL.
- HTTP method đúng ý nghĩa.
- Stateless request.
- Status code phù hợp.
- Request/response JSON rõ ràng.

## HTTP Methods

- `GET /users/{id}`: lấy resource.
- `POST /users`: tạo mới.
- `PUT /users/{id}`: replace toàn bộ.
- `PATCH /users/{id}`: update một phần.
- `DELETE /users/{id}`: xóa.

## Status codes thường dùng

- `200 OK`: thành công.
- `201 Created`: tạo mới thành công.
- `204 No Content`: thành công không body.
- `400 Bad Request`: input sai.
- `401 Unauthorized`: chưa xác thực.
- `403 Forbidden`: không có quyền.
- `404 Not Found`: không tìm thấy.
- `409 Conflict`: conflict business/state.
- `500 Internal Server Error`: lỗi server.

## Validation

```java
public record CreateUserRequest(
    @NotBlank String name,
    @Email String email
) {}
```

Kết hợp `@Valid` ở controller.

## Error response chuẩn

Ví dụ:

```json
{
  "code": "USER_NOT_FOUND",
  "message": "User not found",
  "traceId": "abc-123"
}
```

Nên có error code ổn định để frontend/client xử lý.

## Pagination

Không trả toàn bộ dữ liệu:

```text
GET /transactions?page=0&size=20&sort=createdAt,desc
```

## API versioning

- URL versioning: `/api/v1/users`.
- Header versioning.

Nên chọn đơn giản, nhất quán với team.

## Idempotency

Quan trọng trong payment, notification, event processing.

Ví dụ `POST /notifications` có thể nhận `Idempotency-Key` để tránh gửi trùng khi client retry.

## Câu hỏi phỏng vấn

### Easy

1. REST là gì?
2. PUT và PATCH khác nhau thế nào?
3. 401 và 403 khác nhau thế nào?

### Medium

1. Bạn thiết kế error handling cho API như thế nào?
2. Pagination nên làm thế nào?
3. Khi nào trả 409 Conflict?

### Hard

1. Làm sao đảm bảo idempotency cho API tạo transaction/notification?
2. Versioning API như thế nào khi có client cũ?
3. Nếu API gọi downstream service chậm, bạn xử lý timeout/retry thế nào?

## Gợi ý trả lời theo CV

> Với banking hoặc notification system, em chú ý idempotency và error code rõ ràng vì client có thể retry. Em thường validate input ở boundary, mapping business exception sang HTTP status bằng global exception handler, dùng pagination cho list API và log kèm traceId để hỗ trợ troubleshooting production.

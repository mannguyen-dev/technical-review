# NoSQL, MongoDB và DynamoDB

## Mức độ ưu tiên

**Cao** vì CV có MongoDB và DynamoDB trong các project thực tế.

## MongoDB

Phù hợp với document data, schema linh hoạt.

Ưu điểm:

- Lưu document JSON-like.
- Dễ thay đổi schema.
- Tốt cho read/write document theo aggregate.

Lưu ý:

- Không nên lạm dụng nếu cần join phức tạp.
- Cần index đúng.
- Transaction có nhưng không nên dùng như relational DB cho mọi thứ.

## DynamoDB

DynamoDB là NoSQL key-value/document database managed của AWS.

Khái niệm chính:

- Partition key.
- Sort key.
- GSI/LSI.
- RCU/WCU hoặc on-demand capacity.
- TTL.
- Conditional writes.

## Thiết kế DynamoDB

Không bắt đầu bằng ERD. Bắt đầu bằng access patterns.

Ví dụ notification system:

- Get notification by id.
- Query notifications by user and time.
- Query failed events for retry.
- Store idempotency key.

Thiết kế key có thể là:

```text
PK = USER#<userId>
SK = NOTIFICATION#<timestamp>#<notificationId>
```

Hoặc cho event processing:

```text
PK = EVENT#<eventId>
SK = METADATA
```

## Idempotency với DynamoDB

Dùng conditional write:

- Nếu eventId chưa tồn tại thì insert và process.
- Nếu đã tồn tại thì bỏ qua hoặc return previous result.

## Câu hỏi phỏng vấn

### Easy

1. MongoDB khác SQL database thế nào?
2. DynamoDB partition key là gì?
3. GSI là gì?

### Medium

1. Vì sao DynamoDB phải thiết kế theo access pattern?
2. Khi nào chọn MongoDB thay vì PostgreSQL?
3. TTL trong DynamoDB dùng để làm gì?

### Hard

1. Thiết kế DynamoDB table cho notification events 1M+/day như thế nào?
2. Làm sao tránh hot partition?
3. Làm sao đảm bảo idempotency trong event processing bằng DynamoDB?

## Gợi ý trả lời theo CV

> Ở notification platform, DynamoDB phù hợp vì workload event-driven có scale lớn, access pattern rõ và cần managed scaling. Em sẽ thiết kế partition key để phân tán tải, dùng sort key cho query theo thời gian nếu cần, GSI cho access pattern phụ, TTL cho data tạm/idempotency record, và conditional write để tránh xử lý trùng event.

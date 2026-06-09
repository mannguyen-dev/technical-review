# Database — Transactions, Locking và Consistency

## Mức độ ưu tiên

**Rất cao** cho banking/backend senior.

## ACID

- **Atomicity**: tất cả thành công hoặc rollback.
- **Consistency**: dữ liệu chuyển từ trạng thái hợp lệ sang hợp lệ.
- **Isolation**: transaction không ảnh hưởng sai lẫn nhau.
- **Durability**: commit rồi thì bền vững.

## Isolation levels

### Read Uncommitted

Có thể dirty read. Ít dùng.

### Read Committed

Không đọc data chưa commit. Có thể non-repeatable read.

### Repeatable Read

Đọc cùng row nhiều lần trong transaction cho kết quả nhất quán. Có thể phantom tùy DB.

### Serializable

Isolation cao nhất, giống chạy tuần tự, nhưng performance thấp hơn.

## Các hiện tượng

- **Dirty read**: đọc data chưa commit.
- **Non-repeatable read**: đọc cùng row hai lần ra kết quả khác.
- **Phantom read**: cùng điều kiện query nhưng có thêm/mất row.

## Locking

### Optimistic locking

- Dùng version.
- Phù hợp conflict thấp.
- Khi update, nếu version đổi thì fail.

### Pessimistic locking

- Lock record khi đọc/update.
- Phù hợp conflict cao nhưng giảm concurrency.

## Distributed transaction

Trong microservices, tránh distributed transaction 2PC nếu có thể. Thay vào đó dùng:

- Saga pattern.
- Outbox pattern.
- Idempotency.
- Eventual consistency.

## Transaction trong Spring

```java
@Transactional
public void transferMoney(...) {
    debit(...);
    credit(...);
}
```

Lưu ý:

- Rollback mặc định với unchecked exception.
- Self-invocation có thể làm `@Transactional` không hoạt động do proxy.
- Transaction boundary nên ở service layer.

## Câu hỏi phỏng vấn

### Easy

1. ACID là gì?
2. Transaction dùng để làm gì?
3. Commit và rollback khác nhau thế nào?

### Medium

1. Isolation levels khác nhau thế nào?
2. Optimistic locking và pessimistic locking khác nhau thế nào?
3. Spring `@Transactional` rollback khi nào?

### Hard

1. Chuyển tiền giữa hai account xử lý transaction thế nào?
2. Microservices làm sao đảm bảo consistency không dùng distributed transaction?
3. Outbox pattern giải quyết vấn đề gì?

## Gợi ý trả lời theo banking

> Với banking, consistency và audit rất quan trọng. Với operation như transfer, em đảm bảo debit và credit nằm trong cùng transaction nếu cùng database. Nếu qua nhiều microservice, em tránh giả định transaction distributed đơn giản mà dùng saga/outbox, idempotency key và audit log để đảm bảo eventual consistency có kiểm soát.

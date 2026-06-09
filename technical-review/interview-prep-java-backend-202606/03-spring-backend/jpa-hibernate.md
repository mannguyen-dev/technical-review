# JPA / Hibernate

## Mức độ ưu tiên

**Cao**. CV có JPA/Hibernate, banking/insurance database-heavy.

## Entity và Repository

Entity mapping tới table:

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
}
```

Repository:

```java
public interface CustomerRepository extends JpaRepository<Customer, Long> {}
```

## Entity lifecycle

- Transient: object mới, chưa gắn persistence context.
- Managed: được EntityManager quản lý.
- Detached: từng managed nhưng đã rời context.
- Removed: đánh dấu xóa.

## Lazy vs Eager

- Lazy: load khi truy cập.
- Eager: load ngay.

Best practice: cẩn thận với eager vì có thể load quá nhiều data. Dùng fetch join/entity graph/DTO projection khi cần.

## N+1 query

Ví dụ lấy 100 orders, mỗi order lazy load customer → 1 query orders + 100 query customers.

Cách xử lý:

- `JOIN FETCH`.
- EntityGraph.
- DTO projection.
- Batch size.

## Transaction boundary

Service layer thường là nơi đặt `@Transactional`.

```java
@Transactional
public void transfer(...) { ... }
```

## Optimistic locking

Dùng `@Version` để tránh lost update.

```java
@Version
private Long version;
```

Phù hợp khi conflict ít.

## Câu hỏi phỏng vấn

### Easy

1. JPA khác Hibernate thế nào?
2. `@Entity` và `@Table` dùng làm gì?
3. Lazy và eager loading khác nhau thế nào?

### Medium

1. N+1 query là gì? Cách xử lý?
2. Persistence context là gì?
3. Transaction nên đặt ở layer nào?

### Hard

1. Optimistic locking hoạt động thế nào?
2. Vì sao không nên expose entity trực tiếp ra API response?
3. LazyInitializationException xảy ra khi nào?

## Gợi ý trả lời mạnh

> Em thường tránh expose entity trực tiếp ra API mà dùng DTO để kiểm soát data trả về và tránh lazy loading ngoài ý muốn. Khi gặp performance issue, em kiểm tra query log/explain plan, đặc biệt là N+1 query, và dùng fetch join hoặc projection tùy use case.

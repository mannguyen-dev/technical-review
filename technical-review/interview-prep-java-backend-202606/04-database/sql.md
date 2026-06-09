# Database — SQL và Query Optimization

## Mức độ ưu tiên

**Rất cao**. CV có PostgreSQL, MySQL, SQL Server, MongoDB, DynamoDB và các hệ thống banking/insurance.

## SQL basics cần chắc

- `SELECT`, `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING`.
- `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`.
- Aggregate: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.
- Subquery, CTE cơ bản.

## JOIN

### INNER JOIN

Chỉ lấy record match cả hai bảng.

### LEFT JOIN

Lấy tất cả record bên trái, bên phải không có thì null.

Câu hỏi hay gặp: tìm customer chưa có order → dùng LEFT JOIN + WHERE right side IS NULL hoặc NOT EXISTS.

## Index

Index giúp query nhanh hơn nhưng làm write chậm hơn và tốn storage.

### Khi nào index hiệu quả?

- Column hay dùng trong `WHERE`, `JOIN`, `ORDER BY`.
- Selectivity tốt.
- Composite index đúng thứ tự column.

### Composite index

Index `(branch_id, created_at)` hữu ích cho query filter theo `branch_id` và sort/filter theo `created_at`.

## EXPLAIN PLAN

Dùng để xem database execute query như thế nào:

- Full table scan?
- Index scan?
- Join strategy?
- Estimated rows?

## Query optimization checklist

1. Có index phù hợp không?
2. Query có select quá nhiều column không?
3. Có N+1 query từ ORM không?
4. Có pagination không?
5. Có function trên indexed column làm mất index không?
6. Data volume và cardinality thế nào?

## SQL vs NoSQL

### SQL phù hợp khi

- Data relational.
- Cần transaction mạnh.
- Query linh hoạt.
- Banking/financial consistency.

### NoSQL phù hợp khi

- Access pattern rõ.
- Scale lớn.
- Schema linh hoạt.
- Event/log/document data.

DynamoDB cần thiết kế theo access pattern, không thiết kế như relational DB.

## Câu hỏi phỏng vấn

### Easy

1. INNER JOIN và LEFT JOIN khác nhau thế nào?
2. WHERE và HAVING khác nhau thế nào?
3. Index là gì?

### Medium

1. Composite index hoạt động thế nào?
2. Làm sao tối ưu query chậm?
3. SQL và NoSQL khác nhau thế nào?

### Hard

1. Thiết kế schema cho banking transactions cần lưu ý gì?
2. DynamoDB partition key/sort key chọn thế nào cho notification events?
3. Nếu query dùng index nhưng vẫn chậm, nguyên nhân có thể là gì?

## Gợi ý trả lời theo CV

> Trong các hệ thống banking/insurance, em ưu tiên consistency, auditability và query correctness. Khi tối ưu performance, em bắt đầu từ actual query và execution plan, sau đó kiểm tra index, data volume, ORM-generated query và pagination. Với NoSQL như DynamoDB, em thiết kế theo access pattern thay vì normalize như SQL.

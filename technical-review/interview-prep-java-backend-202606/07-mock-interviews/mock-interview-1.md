# Mock Interview 1 — Java Backend / Java SE Focus

## Mục tiêu

Buổi mock này mô phỏng vòng technical đầu tiên cho Java Backend/Java SE. Trọng tâm: Java Core, OOP, Collections, Exception, Spring basics, SQL.

Thời lượng gợi ý: 45–60 phút.

---

## Phần 1: Warm-up và CV

### Q1 [Easy] — Tell me about yourself.

**Framework trả lời:**

- 5 năm Java/Spring Backend.
- Domain: banking, insurance, automotive.
- Strength: microservices, event-driven, cloud.
- Highlight project gần nhất.
- Career objective.

### Q2 [Easy] — Which project in your CV is most relevant to this Java Backend role?

**Gợi ý:** Chọn Banking Teller Platform nếu role enterprise Java/Spring; chọn Notification System nếu role cloud/distributed.

---

## Phần 2: Java Core

### Q3 [Easy] — OOP có 4 tính chất nào?

**Answer outline:**

- Encapsulation.
- Inheritance.
- Polymorphism.
- Abstraction.
- Liên hệ Spring: interface/service abstraction.

### Q4 [Medium] — Interface khác abstract class như thế nào?

**Answer outline:**

- Interface là contract; abstract class chia sẻ behavior/state chung.
- Java class implement nhiều interface nhưng chỉ extend một class.
- Trong Spring, thường inject interface để giảm coupling.

### Q5 [Medium] — HashMap hoạt động như thế nào?

**Answer outline:**

- Bucket array.
- hashCode xác định bucket.
- equals xác định key thật sự trùng.
- Collision: linked list/tree bin.
- Resize theo load factor.

### Q6 [Hard] — Vì sao HashMap không thread-safe? Nếu cần thread-safe map thì làm gì?

**Answer outline:**

- Concurrent put/resize có thể gây inconsistent state/lost update.
- Dùng ConcurrentHashMap.
- Hoặc tránh shared mutable state trong singleton service.

### Q7 [Medium] — Checked và unchecked exception khác nhau thế nào?

**Answer outline:**

- Checked bắt buộc handle/throws.
- Unchecked kế thừa RuntimeException.
- Business exception trong Spring thường unchecked và handle tập trung.

### Q8 [Hard] — Trong Spring `@Transactional`, exception nào rollback mặc định?

**Answer outline:**

- RuntimeException/Error rollback mặc định.
- Checked exception không rollback mặc định.
- Có thể dùng `rollbackFor`.
- Nếu catch exception và không throw lại, transaction có thể commit.

### Q9 [Hard] — Spring singleton bean có thread-safe không?

**Answer outline:**

- Singleton chỉ nghĩa là một instance trong container.
- Thread-safe phụ thuộc state bên trong.
- Stateless service thường safe.
- Mutable shared state cần synchronization/thread-safe structure hoặc tránh dùng.

---

## Phần 3: Spring Boot

### Q10 [Easy] — Spring Boot khác Spring Framework như thế nào?

**Answer outline:**

- Boot simplify setup.
- Auto-configuration, starters, embedded server, actuator.

### Q11 [Medium] — Constructor injection tốt hơn field injection ở điểm nào?

**Answer outline:**

- Required dependency rõ ràng.
- `final`, immutability.
- Dễ unit test.
- Fail fast khi thiếu dependency.

### Q12 [Medium] — REST API error handling bạn thiết kế thế nào?

**Answer outline:**

- Validate request bằng Bean Validation.
- Custom business exception.
- `@RestControllerAdvice` mapping status/error code.
- TraceId/logging.

### Q13 [Hard] — N+1 query là gì và xử lý như thế nào?

**Answer outline:**

- 1 query parent + N query child.
- Detect qua SQL logs/APM.
- Fix bằng fetch join, entity graph, projection, batch size.

---

## Phần 4: SQL và Database

### Q14 [Easy] — INNER JOIN và LEFT JOIN khác nhau thế nào?

**Answer outline:**

- INNER: match cả hai.
- LEFT: giữ toàn bộ bên trái.

### Q15 [Medium] — Index giúp gì và trade-off là gì?

**Answer outline:**

- Tăng tốc read/filter/sort/join.
- Tốn storage, giảm tốc write.
- Cần chọn column theo query pattern/selectivity.

### Q16 [Hard] — Isolation levels và các hiện tượng dirty/non-repeatable/phantom read?

**Answer outline:**

- Giải thích 4 isolation levels.
- Liên hệ banking cần consistency.

---

## Phần 5: Candidate Questions

Bạn nên hỏi:

1. Team đang dùng Java/Spring version nào?
2. Service hiện tại là monolith hay microservices?
3. Team có challenge gì về performance/reliability không?
4. Quy trình CI/CD và code review như thế nào?

## Tự chấm điểm

- Có trả lời bằng ví dụ từ CV không?
- Có nói trade-off không?
- Có tránh trả lời quá dài không?
- Có thành thật về phần mình trực tiếp làm không?

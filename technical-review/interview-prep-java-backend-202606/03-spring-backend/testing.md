# Testing Backend — JUnit, Mockito, Integration Test

## Mức độ ưu tiên

**Cao**. CV có JUnit, Mockito, Postman, JMeter, SonarQube, Veracode.

## Unit test

Test một unit nhỏ, mock dependency.

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock OrderRepository orderRepository;
    @InjectMocks OrderService orderService;
}
```

## Integration test

Test nhiều layer/component cùng nhau. Có thể dùng:

- `@SpringBootTest`.
- `@WebMvcTest` cho controller.
- Testcontainers cho DB thật nếu project dùng.

## Mockito

- `when(...).thenReturn(...)`.
- `verify(...)`.
- `ArgumentCaptor`.

## Test tốt nên có

- Arrange / Act / Assert rõ ràng.
- Test tên mô tả behavior.
- Cover happy path và edge cases.
- Không phụ thuộc order không cần thiết.
- Không mock quá sâu làm test brittle.

## SonarQube / Veracode

- SonarQube: code smell, bug, coverage, duplication, maintainability.
- Veracode: security scan, vulnerability.

Trong CV bạn có resolved 100+ security/code quality issues — cần chuẩn bị ví dụ.

## Câu hỏi phỏng vấn

### Easy

1. Unit test khác integration test thế nào?
2. Mockito dùng để làm gì?
3. Code coverage cao có đảm bảo chất lượng không?

### Medium

1. Bạn test service layer như thế nào?
2. Khi nào dùng `@SpringBootTest`?
3. Bạn xử lý SonarQube issue như thế nào?

### Hard

1. Làm sao test event-driven flow?
2. Làm sao test transaction rollback?
3. Nếu test flaky trên CI, bạn xử lý thế nào?

## Gợi ý trả lời theo CV

> Em ưu tiên unit test cho business logic ở service/domain layer, mock repository hoặc external clients bằng Mockito. Với API quan trọng, em dùng integration test hoặc Postman collection để verify end-to-end. Với SonarQube/Veracode, em không chỉ fix để pass scan mà phân loại issue: security, maintainability, duplication, deprecated dependency, rồi ưu tiên theo severity và impact.

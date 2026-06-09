# Java Core — Collections

## Mức độ ưu tiên

**Rất cao**. Collections là chủ đề Java Core gần như chắc chắn được hỏi.

## List, Set, Map

### List

- Có thứ tự.
- Cho phép duplicate.
- Implementations thường gặp: `ArrayList`, `LinkedList`.

`ArrayList` phù hợp khi read theo index nhiều. `LinkedList` ít dùng trong backend thông thường vì overhead node và cache locality kém.

### Set

- Không cho duplicate.
- `HashSet`: không đảm bảo thứ tự.
- `LinkedHashSet`: giữ insertion order.
- `TreeSet`: sorted order.

### Map

- Key-value.
- `HashMap`, `LinkedHashMap`, `TreeMap`, `ConcurrentHashMap`.

## HashMap hoạt động thế nào?

Ý chính cần nói:

1. HashMap lưu dữ liệu theo bucket array.
2. Key được tính `hashCode()` để xác định bucket.
3. Nếu collision, Java dùng linked list hoặc tree bin khi collision nhiều.
4. Khi số phần tử vượt threshold = capacity × load factor, HashMap resize.
5. `equals()` dùng để xác định key có thật sự giống nhau không.

## equals và hashCode

Rule quan trọng:

- Nếu `a.equals(b) == true` thì `a.hashCode() == b.hashCode()` phải true.
- Nếu hashCode giống nhau chưa chắc equals true.

Ví dụ lỗi thường gặp: dùng object làm key nhưng không override equals/hashCode.

## ArrayList vs LinkedList

| Tiêu chí | ArrayList | LinkedList |
|---|---|---|
| Access by index | Nhanh O(1) | Chậm O(n) |
| Add cuối list | Thường nhanh | Nhanh |
| Insert giữa list | Có thể phải shift | Tìm vị trí O(n), insert O(1) |
| Memory | Ít overhead hơn | Nhiều overhead node |

Trong backend, `ArrayList` thường là default tốt hơn.

## HashMap vs ConcurrentHashMap

- `HashMap` không thread-safe.
- `ConcurrentHashMap` hỗ trợ concurrent access tốt hơn.
- Không nên dùng `Collections.synchronizedMap` nếu workload concurrent lớn.

Trong Spring service singleton, nếu có mutable shared map thì cần thread-safety. Nhưng tốt nhất hạn chế mutable shared state.

## Câu hỏi phỏng vấn

### Easy

1. List, Set, Map khác nhau thế nào?
2. ArrayList và LinkedList khác nhau thế nào?
3. HashSet làm sao biết object bị duplicate?

### Medium

1. HashMap hoạt động thế nào bên trong?
2. Vì sao phải override cả equals và hashCode?
3. Khi nào dùng ConcurrentHashMap?

### Hard

1. Điều gì xảy ra khi HashMap resize?
2. Vì sao HashMap không thread-safe?
3. Nếu cache in-memory trong Spring Boot service, bạn chọn structure nào và cần lưu ý gì?

## Gợi ý trả lời theo CV

> Trong các hệ thống backend em từng làm, collections thường được dùng để group data, deduplicate records hoặc build lookup map trước khi xử lý batch/event. Với data lớn, em chú ý tránh nested loop O(n²) bằng cách convert list sang map theo key, đồng thời cẩn thận memory usage và thread-safety nếu dữ liệu được share giữa nhiều request.

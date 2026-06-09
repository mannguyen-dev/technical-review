# Java Core — JVM, Memory và Performance

## Mức độ ưu tiên

**Trung bình đến cao** cho senior. Không cần quá sâu như JVM engineer, nhưng cần hiểu memory, GC, performance basics.

## JVM Memory Areas

- **Heap**: chứa object, chia young/old generation tùy GC.
- **Stack**: mỗi thread có stack riêng, chứa local variables, method calls.
- **Metaspace**: metadata của class.
- **PC Register / Native Stack**: phục vụ execution internals.

## Garbage Collection

GC giải phóng object không còn reference.

Các ý cần nhớ:

- Object sống ngắn thường ở young generation.
- Object sống lâu được promote sang old generation.
- GC pause có thể ảnh hưởng latency.
- Memory leak trong Java thường là object còn reference không cần thiết.

## Memory leak thường gặp

- Static collection giữ object mãi.
- Cache không có eviction.
- Listener/callback không unregister.
- ThreadLocal không clear trong thread pool.

## Performance trong backend

Checklist:

- Tránh N+1 query.
- Dùng pagination cho API list.
- Dùng index đúng cho query.
- Tránh load quá nhiều data vào memory.
- Dùng async/event-driven cho task lâu.
- Cache dữ liệu read-heavy nhưng cần TTL/invalidation.
- Đo bằng metric/profiling, không optimize theo cảm tính.

## Câu hỏi phỏng vấn

### Easy

1. Heap và stack khác nhau thế nào?
2. Garbage Collection là gì?
3. Memory leak trong Java có thể xảy ra không?

### Medium

1. Bạn từng optimize performance như thế nào?
2. Làm sao phát hiện memory leak?
3. Vì sao cần pagination?

### Hard

1. GC pause ảnh hưởng service latency thế nào?
2. ThreadLocal có thể gây memory leak ra sao?
3. Nếu API chậm, bạn điều tra theo thứ tự nào?

## Gợi ý trả lời mạnh

> Khi gặp API chậm, em không tối ưu ngay mà kiểm tra metric trước: latency theo endpoint, DB query time, external call time, CPU/memory, log và tracing nếu có. Trong các hệ thống em từng làm, nguyên nhân phổ biến là query chưa tối ưu, N+1 query, thiếu index hoặc xử lý synchronous cho tác vụ nên đưa sang async/event-driven.

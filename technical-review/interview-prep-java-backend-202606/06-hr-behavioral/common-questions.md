# HR & Behavioral Interview — Câu hỏi thường gặp

## Cách trả lời behavioral

Dùng STAR:

- **Situation**: Bối cảnh.
- **Task**: Nhiệm vụ/trách nhiệm.
- **Action**: Bạn đã làm gì.
- **Result**: Kết quả đo được.

Với CV của bạn, nên chuẩn bị ví dụ từ:

- Notification system modernization.
- Banking teller platform.
- Batch modernization.
- Mentoring junior.
- Code review/quality improvement.
- Incident handling.

---

## 1. Tell me about yourself

### Gợi ý trả lời

> Em là Senior Backend Engineer với khoảng 5 năm kinh nghiệm Java/Spring Boot, tập trung vào backend systems, microservices và cloud-native architecture. Em từng làm trong domain banking, insurance và automotive, với các hệ thống như banking teller platform, batch modernization và AWS notification platform xử lý hơn 1 triệu events/ngày.
>
> Ngoài technical implementation, em cũng có kinh nghiệm technical ownership, code review, mentoring junior và phối hợp với international Product/QA/Engineering teams. Em đang tìm cơ hội backend role nơi em có thể đóng góp sâu hơn vào system design, reliability và maintainability.

---

## 2. Why do you want to leave your current company?

### Gợi ý trả lời an toàn

> Em muốn tìm một cơ hội có scope phù hợp hơn với định hướng phát triển của mình trong Java backend, distributed systems và system design. Ở các project trước em đã có kinh nghiệm tốt về enterprise backend và cloud-native systems, nên hiện tại em muốn một môi trường nơi em có thể có nhiều ownership hơn, tham gia sâu hơn vào architecture và tạo impact dài hạn cho sản phẩm.

Tránh:

- Nói xấu công ty/team hiện tại.
- Chỉ nói vì tiền.
- Nói quá chung “em muốn thử thách mới” mà không giải thích.

---

## 3. Strengths

### Điểm mạnh phù hợp CV

1. Strong Java/Spring backend foundation.
2. Ownership và problem-solving trong production systems.
3. Khả năng làm việc với distributed/event-driven systems.
4. Collaboration với international teams.
5. Code quality mindset: review, SonarQube, Veracode.

### Câu trả lời mẫu

> Một điểm mạnh của em là khả năng đi từ business requirement đến backend implementation có tính maintainable. Ví dụ trong notification system, em không chỉ implement Lambda hoặc queue, mà còn chú ý retry, idempotency, monitoring và CI/CD để hệ thống vận hành ổn định. Em cũng có thói quen review code theo correctness, maintainability, performance và security, nên có thể hỗ trợ team giảm rework và production issues.

---

## 4. Weaknesses

### Câu trả lời mẫu

> Trước đây em đôi khi dành nhiều thời gian để làm solution quá hoàn chỉnh ngay từ đầu. Sau một số project Agile, em học cách chia solution thành increment nhỏ hơn, clarify requirement sớm với BA/Product và ưu tiên deliver phần có value trước, sau đó refactor/mở rộng khi requirement rõ hơn.

Điểm tốt: weakness thật nhưng có cải thiện.

---

## 5. Conflict với teammate

### STAR outline

- S: Có disagreement về design hoặc deadline.
- T: Cần chọn giải pháp vừa kịp delivery vừa maintainable.
- A: Đưa ra trade-off, so sánh option, prototype nếu cần, align với team lead/product.
- R: Team chọn solution phù hợp, giảm rủi ro release.

### Câu trả lời mẫu

> Trong một lần thảo luận technical design, team có hai hướng: implement nhanh trong service hiện tại hoặc tách component riêng để dễ maintain. Em không phản đối trực tiếp mà đưa ra trade-off về delivery time, testing effort và future change. Sau đó team thống nhất làm version đầu theo scope nhỏ nhưng vẫn giữ abstraction để sau này tách ra dễ hơn. Kết quả là vẫn kịp release và không tạo coupling quá lớn.

---

## 6. Production incident

### STAR outline

- S: Alert/log error tăng sau release hoặc downstream issue.
- T: Khôi phục service và giảm impact.
- A: Check logs/metrics, identify root cause, rollback/hotfix/retry, communicate.
- R: Service ổn định, thêm monitoring/test/prevention.

### Câu trả lời mẫu

> Khi xử lý incident, em ưu tiên giảm impact trước rồi mới root cause sâu. Em kiểm tra dashboard/log để xác định component lỗi, xem có liên quan release gần nhất không, sau đó phối hợp team để rollback hoặc hotfix nếu cần. Sau incident, em thường bổ sung alert, log context hoặc test case để tránh lặp lại.

---

## 7. Mentoring junior

### Câu trả lời mẫu

> Khi mentor junior, em không chỉ comment “sửa cái này” mà giải thích lý do phía sau, ví dụ vì sao cần transaction boundary ở service layer, vì sao không expose entity trực tiếp, hoặc vì sao cần test edge case. Em cũng thường pair review một vài PR đầu để junior hiểu coding standard và domain nhanh hơn.

---

## 8. Working with international teams

### Câu trả lời mẫu

> Em có kinh nghiệm làm việc hằng ngày bằng tiếng Anh với Product, QA và Engineering teams ở nhiều timezone. Em cố gắng communication rõ ràng bằng cách summarize decision sau meeting, confirm requirement bằng example/test case và raise blocker sớm thay vì chờ đến deadline.

---

## 9. Questions to ask interviewer

- Team backend hiện đang giải quyết challenge lớn nhất là performance, reliability, maintainability hay delivery speed?
- Team đang dùng architecture nào: monolith, modular monolith, microservices hay serverless?
- Quy trình CI/CD, testing và code review hiện tại như thế nào?
- Senior engineer trong team được kỳ vọng ownership ở mức nào?
- Có cơ hội tham gia system design/architecture decision không?

# Mock Interview 2 — Senior Backend / Project & System Design Focus

## Mục tiêu

Buổi mock này mô phỏng vòng technical sâu/senior hoặc hiring manager. Trọng tâm: project ownership, system design, microservices, cloud, Kafka/SQS, production experience.

Thời lượng gợi ý: 60–75 phút.

---

## Phần 1: Deep Dive Project

### Q1 [Easy] — Describe your User Notification System Modernization project.

**Answer outline:**

- Context: automotive notification platform modernization.
- Architecture: EventBridge → SQS → Lambda → DynamoDB/provider.
- Role: design/develop, CI/CD, monitoring, quality/security fixes.
- Impact: 1M+ events/day, manual processing -70%, deploy <30 min, detection -50%.

### Q2 [Medium] — Why event-driven architecture?

**Answer outline:**

- Decouple producer/consumer.
- Buffer spike traffic.
- Async notification does not block main flow.
- Retry/DLQ.
- Trade-off: eventual consistency, duplicate events, harder tracing.

### Q3 [Hard] — How do you guarantee idempotency in notification processing?

**Answer outline:**

- eventId/idempotency key.
- DynamoDB conditional write.
- Store processing status.
- Safe retry.
- Provider-level idempotency if supported.

### Q4 [Hard] — What metrics would you monitor?

**Answer outline:**

- SQS queue depth/age of oldest message.
- Lambda error rate/duration/throttle/concurrency.
- DLQ count.
- Provider response error rate/latency.
- DynamoDB throttling.
- Business metrics: sent/failed notification count.

---

## Phần 2: Banking Project

### Q5 [Medium] — How did you use Kafka in the banking teller platform?

**Answer outline:**

- Real-time cash flow, ATM, audit events.
- Decoupling and scalable consumers.
- Consumer group.
- Offset commit and retry.
- Idempotent consumer.

### Q6 [Hard] — If a Kafka consumer writes to DB then crashes before committing offset, what happens?

**Answer outline:**

- Message may be reprocessed.
- At-least-once semantics.
- Need idempotency/unique constraint/processed event table.
- Commit offset after successful processing.

### Q7 [Hard] — How would you design audit logging for banking transactions?

**Answer outline:**

- Audit event generated for every critical action.
- Immutable append-only log.
- Include actor, timestamp, action, correlationId, before/after if allowed.
- Reliable publishing using outbox pattern.
- Secure access and retention policy.

---

## Phần 3: Batch Modernization

### Q8 [Medium] — How did you migrate legacy batch modules to Spring Boot?

**Answer outline:**

- Understand legacy logic with BA/QA.
- Identify module boundaries.
- Implement Spring Boot service/job.
- Regression test with historical data.
- Parallel run/reconciliation.
- Jenkins/Kubernetes deployment.

### Q9 [Hard] — How do you make batch jobs rerunnable?

**Answer outline:**

- Job execution ID.
- Record-level status.
- Idempotency key.
- Upsert/unique constraint.
- Checkpoint and retry failed records.
- Reconciliation report.

---

## Phần 4: System Design Exercise

### Q10 [Hard] — Design a notification system supporting 1M events/day.

**Strong answer structure:**

1. Clarify:
   - Channels: email/SMS/push?
   - Latency SLA?
   - Ordering required?
   - Provider limitations?
2. Estimate:
   - 1M/day average ~12/sec, but design for peak.
3. Architecture:
   - API/Event producer → EventBridge → SQS → Lambda workers → Provider.
   - DynamoDB for status/idempotency.
   - DLQ for failures.
4. Reliability:
   - Retry with backoff.
   - DLQ replay process.
   - Idempotency key.
5. Observability:
   - Metrics/logs/tracing/alerts.
6. Trade-off:
   - SQS/EventBridge vs Kafka.
   - Lambda vs ECS/Kubernetes.

### Q11 [Hard] — Compare Kafka and SQS.

**Answer outline:**

- Kafka: streaming platform, replay, high throughput, ordering per partition, more operational complexity.
- SQS: managed queue, simple retry/DLQ, AWS serverless integration, at-least-once, limited replay model.
- Choose based on event streaming/replay needs vs simple decoupling.

### Q12 [Hard] — How do you handle eventual consistency in microservices?

**Answer outline:**

- Accept that cross-service state may be temporarily inconsistent.
- Use events, saga, outbox, compensation.
- Idempotent consumers.
- Monitoring and reconciliation.
- UX/API should reflect pending state when needed.

---

## Phần 5: Leadership / Managerial

### Q13 [Medium] — How do you conduct code reviews?

**Answer outline:**

- Correctness and business logic.
- Security/sensitive data.
- Transaction/error handling.
- Performance/query.
- Test coverage.
- Maintainability and consistency.
- Feedback respectful with explanation.

### Q14 [Medium] — Tell me about mentoring junior engineers.

**Answer outline:**

- Pair review.
- Explain reasoning behind comments.
- Create guideline/examples.
- Gradually increase task ownership.

### Q15 [Hard] — Tell me about a time you disagreed with an architecture decision.

**Answer framework:**

- Situation: two options.
- Task: align team.
- Action: compare trade-offs, risk, delivery, maintainability.
- Result: team decision, release impact.

---

## Kết thúc interview

Câu hỏi nên hỏi ngược:

1. What are the biggest backend challenges your team is facing now?
2. How much ownership is expected from a senior backend engineer?
3. How does the team handle incidents and postmortems?
4. Are there ongoing modernization or cloud migration initiatives?

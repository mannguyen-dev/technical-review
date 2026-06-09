# System Design Interview Package

Bộ tài liệu này nằm trong package chuẩn bị phỏng vấn Java Backend của bạn và tập trung riêng vào **System Design dựa trên CV/project thực tế**.

## Cấu trúc file

```text
system-design-interview/
  README.md
  00-overview.md
  01-project-questions.md
  02-api-database-design.md
  03-scalability-performance.md
  04-answer-frameworks.md
  05-quick-review-checklist.md
  06-customer-context-system-design.md
  07-ocbc-dual-screen-websocket.md
  08-zurich-batch-job-modernization.md
```

## Nên học theo thứ tự

1. [00-overview.md](00-overview.md) — hiểu các project và thành phần có thể bị hỏi.
2. [01-project-questions.md](01-project-questions.md) — luyện câu hỏi theo từng project.
3. [02-api-database-design.md](02-api-database-design.md) — ôn API, database, transaction, security.
4. [03-scalability-performance.md](03-scalability-performance.md) — ôn scalability, performance, reliability.
5. [04-answer-frameworks.md](04-answer-frameworks.md) — học cách trả lời có cấu trúc.
6. [05-quick-review-checklist.md](05-quick-review-checklist.md) — review nhanh trước phỏng vấn.

## Project trọng tâm

- AWS Notification System: event-driven, SQS, EventBridge, Lambda, DynamoDB, CloudWatch.
- Banking Teller Platform: Java Spring Boot, DDD, Kafka, transaction, audit, OpenShift.
- Batch Modernization: Spring Boot, SQL Server, MongoDB, chunk processing, rerun, reconciliation.

## Chủ đề bắt buộc phải nắm

- Idempotency.
- Retry/DLQ.
- Outbox pattern.
- Transaction boundary.
- Kafka/SQS trade-off.
- DynamoDB access pattern.
- API design cho banking/notification.
- Batch checkpoint/rerun.
- Observability: metrics, logs, correlationId.
- Deployment: CI/CD, rolling deploy, rollback.

## Bổ sung customer context

- [06-customer-context-system-design.md](06-customer-context-system-design.md) — Tổng hợp system design theo customer/domain: Cox Automotive, OCBC Bank, Zurich Insurance.

- [07-ocbc-dual-screen-websocket.md](07-ocbc-dual-screen-websocket.md) — Deep dive OCBC dual-screen teller/customer system với WebSocket và 5 module Secure Inventory, Payment Insurance, Cheque Service, Account Closure, Remittance.

- [08-zurich-batch-job-modernization.md](08-zurich-batch-job-modernization.md) — Deep dive Zurich Batch Job modernization từ VB6/Lotus Notes sang Java/MongoDB, xử lý Surveyor data, day/night scheduled jobs.

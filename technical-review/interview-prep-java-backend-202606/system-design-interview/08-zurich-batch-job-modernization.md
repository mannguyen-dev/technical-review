# Zurich Insurance Batch Job Modernization — Surveyor Data, Java, MongoDB

## 1. Bối cảnh project

Trong dự án Zurich Insurance, team modernize một hệ thống **Batch Job legacy** chạy trên **VB6** và **Lotus Notes**, chuyển sang sử dụng **Java** và **MongoDB**.

Các batch job chủ yếu xử lý thông tin mà **Surveyor** thu thập được trong ngày. Dữ liệu này có thể là dữ liệu khảo sát/inspection, form nghiệp vụ, metadata, trạng thái xử lý, thông tin phục vụ downstream/reporting hoặc reconciliation.

Đặc điểm quan trọng:

- Batch jobs chạy theo lịch cố định.
- Có job ban ngày và job ban đêm.
- Các job chạy theo thứ tự giờ giấc.
- Một số job có dependency: job sau phụ thuộc output/trạng thái của job trước.
- Cần đảm bảo rerun an toàn, không duplicate output.
- Cần migration chính xác từ logic VB6/Lotus Notes sang Java.

## 2. High-level architecture

```text
Surveyor Data Sources / Lotus Notes Legacy Data
        ↓
Time-based Scheduler
        ↓
Batch Orchestrator
        ↓
Job Sequence Manager
   ┌───────────────┬────────────────┐
   ↓               ↓                ↓
Day Jobs       Night Jobs       Retry/Recovery Jobs
   ↓               ↓                ↓
Java Batch Processors / Workers
        ↓
MongoDB
  - surveyor raw documents
  - normalized processing data
  - job execution status
  - record-level status
  - checkpoints
        ↓
Reconciliation / Reports / Downstream Output
        ↓
Monitoring / Alerts / Operation Dashboard
```

## 3. Day jobs vs Night jobs

## Day jobs

Có thể dùng để:

- Ingest dữ liệu Surveyor thu thập trong ngày.
- Validate dữ liệu đầu vào.
- Normalize/transform dữ liệu.
- Cập nhật trạng thái record.
- Chuẩn bị dữ liệu cho job ban đêm.

## Night jobs

Có thể dùng để:

- Tổng hợp dữ liệu ngày.
- Reconciliation.
- Generate report/output.
- Đồng bộ downstream systems.
- Cleanup/archive.
- Retry các record fail nếu business cho phép.

## Điểm cần nói trong phỏng vấn

> Em sẽ không thiết kế các job chạy rời rạc không kiểm soát. Em sẽ có scheduler/orchestrator quản lý job sequence, dependency, job status, record status và alert. Job ban đêm có thể phụ thuộc kết quả job ban ngày, nên nếu day job fail, hệ thống cần quyết định dừng night job, chạy partial hay chuyển sang retry tùy rule nghiệp vụ.

## 4. Job orchestration design

### Job sequence config

Có thể cấu hình:

```yaml
businessDate: 2026-06-06
jobs:
  - name: ingest-surveyor-data
    schedule: "09:00"
    type: DAY
    dependsOn: []
  - name: validate-surveyor-data
    schedule: "12:00"
    type: DAY
    dependsOn: [ingest-surveyor-data]
  - name: transform-surveyor-data
    schedule: "17:00"
    type: DAY
    dependsOn: [validate-surveyor-data]
  - name: nightly-reconciliation
    schedule: "23:00"
    type: NIGHT
    dependsOn: [transform-surveyor-data]
  - name: generate-report
    schedule: "02:00"
    type: NIGHT
    dependsOn: [nightly-reconciliation]
```

### Job states

```text
PENDING
READY
RUNNING
SUCCESS
PARTIAL_SUCCESS
FAILED
SKIPPED
CANCELLED
RETRYING
```

### Dependency behavior

Nếu job A fail và job B phụ thuộc A:

- B không nên tự động chạy nếu input không đảm bảo.
- Orchestrator đánh dấu B là `SKIPPED` hoặc `BLOCKED`.
- Alert operation team.
- Sau khi fix/rerun A thành công, B có thể được trigger lại.

## 5. MongoDB data model

## `surveyor_records`

```json
{
  "_id": "recordId",
  "businessDate": "2026-06-06",
  "surveyorId": "SV001",
  "surveyType": "PROPERTY_INSPECTION",
  "source": "LOTUS_NOTES_LEGACY",
  "rawPayload": {},
  "normalizedData": {},
  "status": "VALIDATED",
  "createdAt": "...",
  "updatedAt": "..."
}
```

## `batch_job_executions`

```json
{
  "_id": "jobExecutionId",
  "jobName": "validate-surveyor-data",
  "businessDate": "2026-06-06",
  "jobType": "DAY",
  "status": "SUCCESS",
  "startedAt": "...",
  "endedAt": "...",
  "totalCount": 100000,
  "successCount": 99800,
  "failedCount": 200,
  "dependsOn": ["ingest-surveyor-data"]
}
```

## `batch_record_executions`

```json
{
  "_id": "jobExecutionId:recordId",
  "jobExecutionId": "job-123",
  "recordId": "record-456",
  "businessDate": "2026-06-06",
  "status": "FAILED",
  "retryCount": 2,
  "errorCode": "INVALID_REQUIRED_FIELD",
  "errorMessage": "Missing survey date",
  "updatedAt": "..."
}
```

## `batch_checkpoints`

```json
{
  "_id": "jobExecutionId:partition-01",
  "jobExecutionId": "job-123",
  "partitionId": "partition-01",
  "lastProcessedRecordId": "record-999",
  "updatedAt": "..."
}
```

## Index gợi ý

```text
surveyor_records: businessDate + status
surveyor_records: surveyorId + businessDate
batch_job_executions: businessDate + jobName
batch_job_executions: status + startedAt
batch_record_executions: jobExecutionId + status
```

## 6. Idempotent rerun

Batch job cần rerun an toàn vì job có thể fail giữa chừng hoặc cần chạy lại cho một business date.

Cách thiết kế:

- Mỗi record có business key ổn định.
- Output dùng upsert thay vì insert mù.
- Có unique key cho output/business result.
- Record-level status để biết record nào đã success.
- Khi rerun, chỉ xử lý record failed/pending hoặc toàn bộ nhưng operation phải idempotent.
- Checkpoint theo chunk/partition.

Câu trả lời mẫu:

> Nếu batch fail ở giữa, em không muốn rerun mù và tạo duplicate. Em sẽ lưu job execution status và record-level status trong MongoDB. Khi rerun theo businessDate/jobName, processor sẽ skip hoặc upsert các record đã xử lý thành công, chỉ retry failed/pending records nếu phù hợp. Output phải có business key/unique constraint để rerun không tạo duplicate.

## 7. Handling failure scenarios

## Day job fail, night job depends on it

Ý chính:

- Mark day job `FAILED` hoặc `PARTIAL_SUCCESS`.
- Night job chuyển `BLOCKED/SKIPPED` nếu dependency chưa đạt.
- Alert operation team.
- Cho phép rerun day job sau khi fix.
- Sau khi dependency success, trigger night job thủ công hoặc tự động theo rule.

## Job chạy quá SLA

Ý chính:

- Monitor duration per job.
- Alert nếu vượt threshold.
- Check bottleneck: MongoDB query, CPU, IO, external dependency.
- Tune chunk size, index, bulk operations, parallelism có kiểm soát.

## Record invalid

Ý chính:

- Không retry vô hạn nếu lỗi data validation.
- Mark record failed với error code.
- Generate exception report cho operation/BA.
- Cho phép sửa data và rerun record.

## MongoDB unavailable

Ý chính:

- Job fail fast hoặc retry với backoff tùy loại lỗi.
- Không mark success nếu write chưa thành công.
- Alert.
- Resume từ checkpoint sau khi DB recover.

## 8. Migration từ VB6/Lotus Notes sang Java/MongoDB

## Strategy

- Inventory toàn bộ legacy jobs.
- Xác định input/output/dependency/schedule của từng job.
- Reverse engineer business rules từ VB6/Lotus Notes.
- Làm việc với BA/QA để confirm rules.
- Tạo golden datasets.
- Implement Java job tương ứng.
- Run parallel old vs new nếu có thể.
- Reconciliation output.
- Cutover theo từng job/module thay vì big bang nếu được.

## Lỗi cần tránh

- Rewrite theo cảm tính khi chưa hiểu legacy behavior.
- Không document job dependency.
- Không có golden dataset.
- Không có rollback/cutover plan.

## 9. Interview questions theo mức độ

## Easy

### Q1. Hệ thống Zurich batch job xử lý dữ liệu gì?

**Mục đích:** kiểm tra bạn hiểu domain.

**Ý chính:** xử lý dữ liệu Surveyor thu thập trong ngày; dữ liệu dạng survey/inspection/form/document metadata; migrate từ VB6/Lotus Notes sang Java/MongoDB.

**Tránh:** nói chung chung “xử lý batch data” mà không nhắc Surveyor và day/night jobs.

### Q2. Vì sao chọn MongoDB cho Surveyor data?

**Mục đích:** kiểm tra database choice.

**Ý chính:** document/form data linh hoạt, raw payload, schema thay đổi theo survey type, index theo access pattern.

**Tránh:** nói MongoDB luôn nhanh hơn SQL.

## Medium

### Q3. Thiết kế scheduler cho day jobs và night jobs.

**Mục đích:** kiểm tra orchestration/dependency.

**Ý chính:** time-based scheduler, orchestrator, job sequence config, dependency, status, alert.

**Tránh:** cron rời rạc không quản lý dependency/status.

### Q4. Thiết kế MongoDB collections cho job status và record status.

**Mục đích:** kiểm tra operability/retry.

**Ý chính:** surveyor_records, batch_job_executions, batch_record_executions, checkpoints, indexes.

**Tránh:** chỉ log file, không có persistent status.

### Q5. Nếu job ban ngày fail, job ban đêm phụ thuộc xử lý thế nào?

**Mục đích:** kiểm tra failure handling.

**Ý chính:** block/skip dependent jobs, alert, rerun dependency, then resume downstream.

**Tránh:** vẫn chạy job sau với input thiếu/sai.

## Hard

### Q6. Batch fail ở 60% records thì recovery thế nào?

**Mục đích:** kiểm tra resumability.

**Ý chính:** checkpoint, record-level status, idempotent upsert, retry failed/pending, no duplicate output.

**Tránh:** rerun toàn bộ không idempotent.

### Q7. Làm sao đảm bảo job chạy đúng thứ tự khi deploy nhiều pod Kubernetes?

**Mục đích:** kiểm tra distributed scheduling/concurrency.

**Ý chính:** single scheduler leader, distributed lock hoặc external scheduler, job execution lock theo jobName/businessDate, unique constraint, status transition atomic.

**Tránh:** nhiều pod cùng trigger một job gây duplicate processing.

### Q8. Làm sao optimize batch 100K+ records/day?

**Mục đích:** kiểm tra performance.

**Ý chính:** measure bottleneck, indexes, bulk operations, chunking, streaming, partitioning, controlled parallelism, avoid loading all data.

**Tránh:** tăng thread vô tội vạ.

### Q9. Làm sao verify Java job mới tương thích VB6/Lotus Notes legacy?

**Mục đích:** kiểm tra migration quality.

**Ý chính:** golden dataset, old vs new output comparison, reconciliation, BA/QA sign-off, parallel run.

**Tránh:** chỉ unit test code mới mà không compare legacy behavior.

### Q10. Nếu một record Surveyor có schema mới chưa được Java job support thì xử lý thế nào?

**Mục đích:** kiểm tra schema evolution.

**Ý chính:** schema version, validation, unknown field handling, fail with clear error hoặc backward-compatible processing, alert/report, update mapping.

**Tránh:** crash toàn bộ job vì một record lạ.

## 10. Answer pitch ngắn

> In the Zurich Insurance project, we modernized legacy batch jobs from VB6 and Lotus Notes to Java and MongoDB. These jobs mainly processed data collected by Surveyors during the day. The jobs were scheduled in ordered sequences, including day jobs and night jobs, so orchestration and dependency management were important. My design focus would be job execution tracking, record-level status, MongoDB document modeling for flexible survey data, checkpointing, idempotent rerun, failure handling, and reconciliation to ensure the new Java jobs matched the legacy behavior.

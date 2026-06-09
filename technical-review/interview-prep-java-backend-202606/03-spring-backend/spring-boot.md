# Spring Boot — Tổng quan và câu hỏi trọng tâm

## Mức độ ưu tiên

**Rất cao**. CV của bạn xoay quanh Java Spring Boot microservices.

## Spring Boot giải quyết vấn đề gì?

Spring Boot giúp tạo ứng dụng Spring nhanh hơn bằng:

- Auto-configuration.
- Embedded server như Tomcat.
- Starter dependencies.
- Externalized configuration.
- Production-ready features qua Actuator.

## Spring Boot Application Flow

Thông thường:

```text
Client → Controller → Service → Repository → Database
                         ↓
                    Message broker / External API
```

## Annotation quan trọng

- `@SpringBootApplication`: kết hợp `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`.
- `@RestController`: controller trả JSON.
- `@Service`: business logic.
- `@Repository`: persistence layer.
- `@Component`: generic bean.
- `@Configuration`: khai báo bean config.
- `@Value`, `@ConfigurationProperties`: đọc config.

## Auto-configuration

Spring Boot kiểm tra classpath, properties và beans hiện có để tự cấu hình.

Ví dụ: nếu có `spring-boot-starter-web`, Boot auto-config Tomcat, DispatcherServlet, Jackson.

## Profiles

Dùng để tách config theo môi trường:

- `application-dev.yml`
- `application-uat.yml`
- `application-prod.yml`

Câu trả lời tốt: không hardcode config; dùng environment variables/secrets manager cho thông tin nhạy cảm.

## Actuator

Dùng cho production readiness:

- `/actuator/health`
- `/actuator/metrics`
- Prometheus/Grafana integration nếu có.

Trong CV, bạn có CloudWatch/Kibana monitoring; nên liên hệ observability.

## Câu hỏi phỏng vấn

### Easy

1. Spring Boot khác Spring Framework ở điểm nào?
2. `@SpringBootApplication` gồm những gì?
3. Starter dependency là gì?

### Medium

1. Auto-configuration hoạt động như thế nào?
2. Bạn quản lý config theo environment ra sao?
3. Bạn tổ chức layer trong Spring Boot service như thế nào?

### Hard

1. Nếu application startup chậm, bạn kiểm tra gì?
2. Làm sao thiết kế service dễ test và maintain?
3. Khi nào nên tách microservice, khi nào không?

## Gợi ý trả lời theo CV

> Trong các project Spring Boot, em thường tổ chức code theo layer hoặc theo domain tùy độ phức tạp. Với service enterprise, em ưu tiên tách rõ controller, application service, domain logic và repository. Em cũng dùng profile/config để tách môi trường, CI/CD để deploy ổn định, và monitoring để quan sát health/metrics sau release.

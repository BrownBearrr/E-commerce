# Tech Stack và Scope

Stack được trích từ Master Plan. Đây là công nghệ dự kiến theo round, không có nghĩa tất cả phải đưa vào ngay Round 1.

## Frontend — một React app cho Customer và Admin

- ReactJS, JavaScript, React Router, Axios
- Redux Toolkit, TanStack Query
- Ant Design, CSS/SCSS
- WebSocket
- Vitest, React Testing Library

Customer và Admin dùng chung một frontend project/build; React Router tách route tree/layout (ví dụ `/products/**` và `/admin/**`). Backend vẫn phải enforce authorization theo role; route guard frontend không thay thế bảo mật API.

## Backend

- Java, Spring Boot, Spring MVC
- Spring Data JPA, Hibernate, Maven
- Spring Security
- SLF4J, Logback

## Identity và bảo mật

- Keycloak, OAuth 2.0, OpenID Connect (OIDC), JWT
- Roles: `USER`, `ADMIN`

## Data và messaging

- MySQL, Flyway
- Redis
- Apache Kafka
- WebSocket cho realtime

## Testing

- Backend: JUnit 5, Mockito, Spring Boot Test, MockMvc, Testcontainers
- Frontend: Vitest, React Testing Library

## Container, deploy và operations — sau local complete

- Docker, Docker Compose
- Linux, SSH, Nginx, HTTPS/SSL
- GitHub Actions, Docker Registry
- Prometheus, Grafana

## Version control

- Git, GitHub

## Thứ tự áp dụng

- Round 0–1: design/Git, React → Spring Boot → MySQL.
- Round 2–6: business, authentication, admin và COD.
- Round 7–10: Redis, microservices, Kafka/WebSocket, testing/optimization.
- Round 11–14: Docker, Linux/Nginx, CI/CD, logging/monitoring.

## Ngoài scope hiện tại

Không đưa vào implementation nếu chưa được yêu cầu/duyệt thay đổi Master Plan: TypeScript, Kubernetes, Terraform, AWS/GCP/Azure, GraphQL, gRPC, MongoDB, RabbitMQ, Eureka, Spring Cloud Config, ELK, OpenTelemetry, Jaeger, Kafka Schema Registry, MoMo, VNPay và online payment gateway.

Khi nhắc công nghệ ngoài scope để đối chiếu, ghi rõ đó chỉ là so sánh và không thêm vào dependency, architecture hay roadmap.

# Roadmap hiện tại

Nguồn: E-commerce Project — Master Plan. Tổng cộng **3 phase, 15 round (0–14)**. Round 0 đã hoàn tất (🟢); các round còn lại giữ trạng thái 🔴 cho tới khi checkpoint thực tế được hoàn thành.

## Phase 1 — Build complete system on local

Mục tiêu: hoàn thành business trên local. Thứ tự là monolith trước, sau đó mới refactor microservices. Khi hoàn thành Round 10, đánh dấu **LOCAL PROJECT COMPLETE**.

| Round | Nội dung | Phạm vi/checkpoint |
|---|---|---|
| 0 🟢 | Requirement & Architecture | Requirement, customer/admin, backend, database/ERD, API, project structure, Git, Knowledge Map — design baseline complete |
| 1 | Project Foundation | ReactJS → Spring Boot REST API → MySQL; frontend gọi backend và thao tác DB end-to-end |
| 2 | Product + Category | CRUD, ảnh, giá, variants, stock, search/filter/sort/pagination; admin tạo sản phẩm và customer xem được |
| 3 | User + Authentication | Keycloak, OAuth 2.0/OIDC, JWT, Spring Security, USER/ADMIN; bảo vệ API theo role |
| 4 | Cart + Checkout | Giỏ hàng, địa chỉ, tổng tiền, checkout COD, validation và transaction |
| 5 | Order + COD | Vòng đời đơn hàng, payment COD, customer theo dõi đơn, admin cập nhật trạng thái |
| 6 | Admin Website | Dashboard, product/category/variant/inventory/order/customer/statistics |
| 7 | Inventory + Redis | Điều chỉnh và lịch sử stock, cache, concurrency, query optimization |
| 8 | Microservices | Refactor monolith hiện có; API Gateway, User/Product/Order/Inventory Service |
| 9 | Kafka + WebSocket | Event-driven flow, realtime thông báo admin/customer |
| 10 | Testing + Optimization | Backend/frontend tests, security, concurrency, SQL/cache optimization; toàn hệ thống local hoàn chỉnh |

### Phase 1 checkpoints theo round

- **Round 0:** bộ thiết kế nền tảng được chốt.
- **Round 1:** frontend → backend → database chạy end-to-end.
- **Round 2:** admin tạo product/category; customer tìm và xem sản phẩm.
- **Round 3:** customer/admin đăng nhập; API được bảo vệ theo role.
- **Round 4–5:** luồng product → cart → checkout → order COD và theo dõi đơn hoạt động.
- **Round 6:** admin quản lý các hoạt động cơ bản của shop.
- **Round 7:** stock, concurrency và cache được xử lý.
- **Round 8–9:** business chạy qua microservices, Kafka và realtime notification.
- **Round 10:** test, security và optimization; đánh dấu local complete.

## Phase 2 — Deployment & DevOps

Chỉ bắt đầu sau Phase 1.

| Round | Nội dung | Checkpoint |
|---|---|---|
| 11 | Docker | Containerize frontend app, gateway/services và hạ tầng; toàn hệ thống chạy bằng Docker Compose |
| 12 | Linux Server + Nginx | Deploy thủ công lên Linux; Nginx reverse proxy, domain, HTTPS/SSL, WebSocket proxy |
| 13 | CI/CD | Sau deploy thủ công: GitHub Actions build/test/image/push/deploy/health check |

## Phase 3 — Operations

| Round | Nội dung | Checkpoint |
|---|---|---|
| 14 | Logging + Monitoring | Dùng SLF4J/Logback, Prometheus/Grafana để theo dõi hệ thống và debug theo layer |

## Nguyên tắc thứ tự

Build → Understand → Test → Refactor → Containerize → Deploy → Automate → Monitor.
Không làm DevOps sớm hơn checkpoint local; không viết lại business từ đầu khi refactor sang microservices.

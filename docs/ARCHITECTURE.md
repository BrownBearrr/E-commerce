# Architecture

## Mục tiêu và ranh giới

Hệ thống bán quần áo có hai bề mặt sử dụng: Customer Website và Admin Website, được triển khai trong **một React app** với các route/layout riêng. Thanh toán chỉ ship COD; trạng thái payment gồm `UNPAID`/`PAID`. Không tích hợp cổng thanh toán tiền thật.

## Phase 1 — Local-first monolith

```text
React App
 ├── Customer routes
 └── Admin routes ───── Spring Boot Monolith ── MySQL
                       │
         Keycloak / Redis / WebSocket (theo round)
```

- Backend ban đầu là Spring Boot monolith, chia layer Controller → Service → Repository.
- MySQL lưu domain; Flyway quản lý migration.
- Keycloak cấp danh tính/token; Spring Security bảo vệ API với USER và ADMIN.
- Redis và WebSocket chỉ được bổ sung khi tới round tương ứng.
- Product/Category, User/UserProfile, Cart, Order, Variant stock và InventoryTransaction được thiết kế ở Round 0; xem [DATABASE_DESIGN.md](DATABASE_DESIGN.md).
- Quy ước ERD, table contract, migration và ánh xạ JPA nằm trong [DATABASE_DESIGN.md](DATABASE_DESIGN.md); hoàn tất các quyết định cần chốt trước migration đầu tiên.

### Cấu trúc mã nguồn và API baseline

- Dùng một Git repository (monorepo), gồm `e_commerce_fe/` (một React app cho customer/admin) và `e_commerce_be/`.
- Customer/Admin có route tree và layout riêng trong cùng app; role/route guard phía frontend chỉ phục vụ điều hướng và trải nghiệm. Spring Security ở backend mới là ranh giới bảo vệ API theo role.
- Ảnh sản phẩm lưu trên filesystem ngoài classpath. Local root mặc định `e_commerce_be/uploads/products/`; DB giữ storage key tương đối; backend phục vụ file qua API. Production cấu hình root tới persistent directory/volume. Không lưu binary ảnh trong DB/JAR.
- REST API dùng JSON dưới `/api/v1`; pagination dùng `page` và `size`.
- Error body chuẩn gồm `timestamp`, `status`, `code`, `message`, `path`; thêm `fieldErrors` khi validation lỗi.
- Endpoint/role/error baseline nằm ở [API_DESIGN.md](API_DESIGN.md); monorepo/package layout nằm ở [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md). DTO validation chi tiết được triển khai theo từng feature.

### Domain chính

`User`, `UserProfile`, `Category`, `Product`, `ProductImage`, `Color`, `Size`, `ProductVariant`, `Cart`, `CartItem`, `Order`, `OrderItem`, `InventoryTransaction`.

Quan hệ đã chốt: Category một cấp; mỗi Product thuộc một Category và có ảnh/variants; mỗi variant là tổ hợp Product + Color + Size và giữ stock hiện tại. User có hồ sơ riêng 1:1 (name, phone, address), tối đa một Cart và có thể có nhiều Order. Order lưu snapshot địa chỉ giao hàng; OrderItem lưu snapshot sản phẩm/variant/giá. Soft delete áp dụng cho xóa dữ liệu nghiệp vụ. Chi tiết cột và migration theo [DATABASE_DESIGN.md](DATABASE_DESIGN.md).

### Order/COD

Order: `PENDING → CONFIRMED → PROCESSING → SHIPPING → DELIVERED`, có thể `CANCELLED`. Payment method là `COD`; payment status `UNPAID` hoặc `PAID`. Khi giao thành công, order là `DELIVERED` và payment là `PAID` theo Master Plan.

## Round 8 — Refactor sang microservices

Chỉ thực hiện sau khi business monolith hoàn thiện. Tái cấu trúc business hiện có, không viết lại từ đầu.

```text
Single React app (customer/admin routes)
              ↓
          API Gateway
     ┌────────┼────────┬───────────┐
 User Service Product Service Order Service Inventory Service
                   │
            product image storage
     └────────┴────────┴───────────┘
        MySQL / Redis / Keycloak
```

Frontend vẫn là một React app; nó gọi API Gateway thay vì gọi từng service trực tiếp. Admin/customer route trees không phụ thuộc topology backend.

Product Service sở hữu metadata ảnh và file ảnh sản phẩm. Ở monolith, API ảnh/file adapter cùng storage root vẫn nằm trong backend monolith. Khi tách service, đưa metadata, upload/serve endpoint và storage root cùng sang Product Service; API Gateway proxy đường dẫn công khai ổn định (ví dụ `/api/v1/images/**`) tới Product Service. Storage key tương đối tiếp tục dùng được; chuyển file sang persistent storage do Product Service quản lý. Không cho các service khác truy cập trực tiếp filesystem của Product Service.

Notification Service có thể được cân nhắc nếu cần, không mặc định bắt buộc. Thiết kế boundary, giao tiếp, database ownership, consistency và failure handling trong round này.

### Cách chuyển đổi dự kiến (để thiết kế chi tiết ở Round 8)

Chuyển từng domain theo kiểu strangler: monolith tiếp tục chạy trong khi một nhóm API được chuyển sang service mới; API Gateway định tuyến nhóm API đó tới service mới, rồi mới ngừng phần tương ứng trong monolith. Tránh viết lại toàn bộ hệ thống cùng lúc.

1. **Chuẩn bị monolith trước:** chia package/module theo User, Product, Inventory, Order; giữ dependency một chiều, giao tiếp qua application service/interface thay vì gọi repository/bảng của domain khác trực tiếp. Ghi rõ API và dữ liệu mỗi domain sở hữu.
2. **Đặt API Gateway và hợp đồng API:** frontend tiếp tục gọi một base URL. Gateway ban đầu chuyển tiếp phần lớn request về monolith; cấu hình route có thể chuyển từng domain khi service sẵn sàng. Token Keycloak vẫn là identity chung; service/API kiểm tra quyền phù hợp.
3. **Tách Product Service:** chuyển catalog/product/category/variant/color/size và ProductImage metadata cùng file ảnh sang Product Service. Chuyển dữ liệu và file có đối soát; gateway proxy cả API catalog lẫn URL ảnh. Frontend giữ URL gateway nên không phải biết service nằm ở đâu.
4. **Tách User Service:** chuyển hồ sơ và dữ liệu user-owned của ứng dụng; Keycloak vẫn là identity provider, local user liên kết bằng Keycloak subject. Xác định rõ dữ liệu nào thuộc User Service và cách các service tham chiếu user.
5. **Tách Inventory Service:** chuyển stock hiện tại và InventoryTransaction; chuyển luồng cập nhật tồn kho để chỉ Inventory Service được ghi stock. Phối hợp Order qua hợp đồng API/event đã thiết kế, xử lý retry/idempotency và nhất quán theo Round 9.
6. **Tách Order Service:** chuyển Order/OrderItem và trạng thái đơn. Order giữ snapshot để lịch sử không phụ thuộc dữ liệu catalog/user có thể đổi. Hoàn thiện luồng checkout, hủy đơn và phối hợp tồn kho.
7. **Đóng phần monolith đã rút:** kiểm tra dữ liệu, quyền truy cập, lỗi liên service, logging/monitoring; chỉ xóa module cũ sau khi traffic và dữ liệu đã được xác minh.

Thứ tự service trên là phương án triển khai ban đầu dựa trên domain/service names của Master Plan, không phải quyết định Round 0 bất biến. Round 8 phải xác nhận dependency thực tế, API/event contracts, database ownership, cách migrate/rollback và tiêu chí hoàn tất trước mỗi lần cắt traffic. Không cho service mới đọc/ghi trực tiếp bảng sở hữu domain khác; database-per-service/topology cụ thể sẽ được quyết định trong Round 8 thay vì chốt sớm ở Round 0.

## Round 9 — Events và realtime

```text
Order Service → Kafka (OrderCreated) → Inventory / Notification / Analytics
Admin hoặc Customer ← WebSocket ← cập nhật sự kiện/trạng thái
```

Luồng cụ thể, ownership sự kiện, retry/idempotency và thông báo được thiết kế khi triển khai. WebSocket hỗ trợ thông báo đơn mới cho Admin và thay đổi trạng thái cho Customer.

## Phase 2 — Deployment topology

Sau khi Phase 1 local complete: Docker Compose đóng gói frontends, gateway, services và hạ tầng. Khi deploy, Nginx đứng trước API Gateway và services; domain/HTTPS/SSL và proxy WebSocket thuộc Round 12. CI/CD bắt đầu sau khi đã deploy thủ công.

## Quyết định và điểm cần chốt

- Thứ tự: monolith → local complete → microservices → Docker/deploy/CI-CD → logging/monitoring.
- Scope payment: COD only.
- Database contract được chốt trước khi code feature; Flyway là nguồn triển khai schema, JPA map theo schema đã duyệt.
- Round 0 dùng Master Plan làm baseline; theo quyết định cụ thể hóa của user, customer và admin dùng chung một React app trong monorepo (`e_commerce_fe/` và `e_commerce_be/`).
- Ảnh lưu tại `e_commerce_be/uploads/products/` khi chạy local; database giữ `storage_key` tương đối; backend upload/serve ảnh qua API. Production phải cấu hình filesystem root có persistent storage.
- REST API baseline nằm tại [API_DESIGN.md](API_DESIGN.md). Inter-service boundaries và data ownership được chốt ở Round 8; Kafka/event contracts được chốt ở Round 9.


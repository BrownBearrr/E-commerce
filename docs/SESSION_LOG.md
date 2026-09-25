# Session Log

Thêm một mục mới ở đầu phần `Sessions` cho mỗi buổi/feature. Không ghi thông tin bí mật như token, password hoặc secret.

## Template

```markdown
## YYYY-MM-DD — [Round N] Feature / Session title

### Mục tiêu và phạm vi
- Feature / user flow:
- Acceptance criteria / checkpoint:

### Đã thực hiện
-

### Kiến thức
- Kiến thức trực tiếp đã dùng:
- Kiến thức đã ôn:
- Related concepts / internals / alternatives đã mở rộng:
- Điều đã hiểu về vì sao hệ thống hoạt động:

### Thiết kế và quyết định
- API / data / UI flow:
- Quyết định và lý do:
- Trade-off / điểm còn mở:

### Debug / Test
- Bug hoặc trường hợp biên:
- Root cause:
- Cách sửa và cách xác minh:

### Interview / tự kiểm tra
- Câu hỏi:
- Ý trả lời / phần cần ôn:

### Knowledge Map
- Area và trạng thái trước → sau:
- Bằng chứng:

### Next step
-
```

## Sessions

<!-- Thêm session mới bên dưới dòng này. -->

## 2026-09-25 — [Round 1] Preflight workspace và Git

### Mục tiêu và phạm vi
- Đọc shared docs và kiểm tra workspace/Git trước khi bắt đầu tạo base project; chưa tạo code.

### Đã thực hiện
- Đọc `PROJECT_STATUS.md`, `README.md`, `ROADMAP.md`, `LEARNING_WORKFLOW.md`, `TECH_STACK.md`, `PROJECT_STRUCTURE.md`, `DATABASE_DESIGN.md`, `API_DESIGN.md`, `ARCHITECTURE.md` từ `C:\Users\Public\Learn\E-comerce\docs`.
- Kiểm tra workspace hiện tại: đây là ChatGPT project mirror, chưa có mã nguồn hoặc Git repository. `sources/` là read-only theo `AGENTS.md`.

### Kết luận
- Không khởi tạo project trong mirror để tránh đặt code sai workspace hoặc sửa synced files.
- Cần mở/chọn thư mục code thật trong Codex; sau đó kiểm tra Git/files đang có và cùng người học tạo skeleton theo checkpoint.

### Knowledge Map
- Không đổi trạng thái; chưa có bằng chứng học tập/triển khai.

### Next step
- Mở thư mục code thật của E-commerce làm workspace (hoặc chọn/tạo project root), rồi bắt đầu Checkpoint 1: xác minh repository, ignore rules và tạo cấu trúc monorepo.

## 2026-09-25 — [Round 0] Hoàn tất bộ thiết kế nền tảng

### Mục tiêu và phạm vi
- Hoàn tất tài liệu Round 0 dựa trên Master Plan và các quyết định đã chốt, để phiên Round 1 có thể bắt đầu từ shared docs mà không phụ thuộc lịch sử chat.

### Đã thực hiện
- Chuẩn hóa `DATABASE_DESIGN.md` thành ERD và physical schema contract ban đầu cho catalog, identity/profile, cart, order và inventory.
- Tạo `API_DESIGN.md` với routes, role/access, pagination, error envelope và upload/serve ảnh.
- Tạo `PROJECT_STRUCTURE.md` cho monorepo, một React app customer/admin, backend modular monolith, uploads, secrets và Git.
- Đồng bộ `PROJECT_STATUS.md`, `README.md`; cập nhật trạng thái Round 0 complete.
- Ghi lộ trình strangler dự kiến Round 8 trong `ARCHITECTURE.md`; không coi thứ tự service/database topology là quyết định nghiệp vụ Round 0.

### Thiết kế và quyết định
- Customer/Admin dùng một React app; Spring Security enforce role ở backend.
- Local ảnh nằm ngoài classpath tại `backend/uploads/products/`, DB lưu storage key tương đối, API phục vụ ảnh. Khi tách service, Product Service nhận ownership ảnh và Gateway route request.
- Round 0 artifacts là baseline thiết kế; Round 1 vẫn phải pin dependency versions và xác minh Hibernate/Flyway/MySQL generated DDL bằng project thật.

### Knowledge Map
- Không đổi trạng thái; tạo tài liệu thiết kế không được tính là bằng chứng người học tự giải thích/triển khai/debug.

### Next step
- Bắt đầu Round 1: kiểm tra hoặc khởi tạo repo, pin Java/Spring Boot/MySQL tương thích, dựng React + Spring Boot + MySQL skeleton, rồi tạo migration đầu tiên khớp contract.

## 2026-09-24 — [Round 0] Chốt image storage và một frontend app

### Mục tiêu và phạm vi
- Chốt nơi lưu ảnh sản phẩm, cách frontend hiển thị ảnh và packaging Customer/Admin; bảo đảm có đường chuyển tiếp khi backend tách microservices.

### Đã chốt
- Một React app tại `frontend/` chứa customer/admin route trees và dùng chung build.
- Local image root là `backend/uploads/products/`, nằm ngoài classpath và được loại khỏi Git.
- Database giữ `storage_key` tương đối như `products/<productUuid>/<imageUuid>.webp`; không giữ binary, absolute filesystem path hoặc URL.
- Backend nhận upload admin và phục vụ ảnh qua API. Production cấu hình image root tới persistent directory/volume.
- Khi tách microservices, Product Service sở hữu metadata, upload/serve API và storage của ảnh. API Gateway proxy URL ảnh ổn định; frontend không cần tách và các service khác không truy cập trực tiếp filesystem Product Service.

### Lý do / kiến thức
- Đường dẫn trên ổ đĩa backend không tự truy cập được từ browser. Browser dùng API URL; backend tìm storage key dưới configured root rồi trả file cùng content type.
- Đường dẫn tương đối cho phép đổi filesystem root khi chuyển từ monolith sang Product Service mà không phải lưu đường dẫn máy local trong DB.
- Filesystem production cần persistent storage; storage tạm của container có thể mất dữ liệu khi container bị thay thế.

### Next step
- Trong Round 0 API Design, chốt method/route upload và serve, role, giới hạn file type/size; trong project structure, cấu hình root và `.gitignore`.
- Round 8 thiết kế persistent storage cho Product Service và Gateway routing; giữ một frontend app trừ khi có yêu cầu vận hành mới.

## 2026-09-24 — [Round 0] Chốt Color/Size, soft-delete reuse, stock log, API

### Đã chốt
- Color/Size nhập tự do vào các record dùng chung toàn hệ thống; Variant tham chiếu mỗi loại.
- SKU của Variant active là duy nhất; SKU được dùng lại sau khi Variant cũ soft delete.
- Inventory history gồm Variant, quantity delta, stock trước/sau, lý do, actor, thời điểm và Order reference nếu có.
- API lỗi dùng fields `timestamp`, `status`, `code`, `message`, `path`, thêm `fieldErrors` cho validation.

### Đang thảo luận
- Ảnh: filesystem root, đường dẫn tương đối trong DB và cách API phục vụ file.
- Frontend: một React app với customer/admin routes hay hai frontend apps; một Git repository đã chốt.

### Next step
- Thiết kế luồng upload/serve ảnh local và so sánh packaging frontend trước khi sửa kiến trúc baseline.

## 2026-09-24 — [Round 0] Chốt SKU, snapshot, delete, Git và API

### Mục tiêu và phạm vi
- Cập nhật database/architecture theo câu trả lời chốt Round 0 của user; dùng Master Plan hiện tại làm baseline.

### Đã thực hiện
- Cập nhật `DATABASE_DESIGN.md`, `ARCHITECTURE.md` và `PROJECT_STATUS.md`.

### Thiết kế và quyết định
- Mỗi ProductVariant có SKU duy nhất toàn hệ thống.
- Color/Size do admin nhập tự do, lưu record riêng để Variant tham chiếu; không dùng enum/danh sách cố định.
- User có một địa chỉ; Order snapshot người nhận, số điện thoại, địa chỉ và ghi chú.
- OrderItem snapshot tên sản phẩm, SKU, Color, Size, giá và quantity; không lưu image path.
- Soft delete theo phạm vi đã gợi ý: Category/Product/Variant/User/Profile; Order và InventoryTransaction được giữ như lịch sử.
- Mỗi Cart chỉ có một dòng cho một Variant; quantity cập nhật trên dòng hiện có.
- Một Git repository (monorepo) cho Customer Website, Admin Website và Backend.
- API JSON `/api/v1`, pagination `page`/`size`, error format thống nhất; chi tiết envelope được chốt trong API Design.
- Master Plan là baseline requirement.

### Next step
- Hoàn thiện Round 0 artifacts: physical schema/table contracts, API endpoint/error specs, project/package structure; resolve Color/Size scope, soft-delete unique policy, InventoryTransaction fields và image path.

## 2026-09-24 — Audit DB và hướng dẫn tiếp tục phiên mới

### Mục tiêu và phạm vi
- Kiểm tra mức hoàn chỉnh của database design và khả năng tiếp tục project mà không dựa vào lịch sử chat.

### Kết quả
- Database business rules đã được ghi nhận, nhưng physical schema chưa migration-ready: còn thiếu đặc tả cột đầy đủ, snapshot fields, soft-delete/unique behavior, inventory transaction details, path format, indexes và constraints.
- Learning workflow đã có; thêm `PROJECT_STATUS.md` làm bootstrap/state snapshot cho phiên mới và cập nhật README về thứ tự đọc docs, cập nhật docs, cùng yêu cầu thêm file vào ChatGPT Project sources.

### Next step
- Hoàn tất các mục mở trong `PROJECT_STATUS.md`/`DATABASE_DESIGN.md`, vẽ schema column-by-column và review trước Flyway migration.

## 2026-09-24 — [Round 0] Chốt UUID theo mặc định Spring/JPA

### Mục tiêu và phạm vi
- Chốt cách sinh và lưu UUID để migration và JPA entity thống nhất.

### Đã thực hiện
- Cập nhật `DATABASE_DESIGN.md` theo JPA `GenerationType.UUID` và mapping binary mặc định của Hibernate/MySQL.

### Thiết kế và quyết định
- Dùng kiểu `java.util.UUID`, `@GeneratedValue(strategy = GenerationType.UUID)`; không tự viết generator.
- Giữ Hibernate UUID mapping mặc định, dự kiến MySQL `BINARY(16)` khi database không có native UUID type.
- Flyway phải khai báo kiểu cột khớp dialect/provider thực tế.

### Knowledge Map
- Không đổi trạng thái; quyết định này chưa phải bằng chứng năng lực đã học/áp dụng.

### Next step
- Khi chốt phiên bản Spring Boot/Hibernate ở Round 1, xác nhận generated DDL của MySQL dialect khớp `BINARY(16)` trước migration đầu tiên.

## 2026-09-24 — [Round 0] Chốt tiền, ảnh, enum và thời gian

### Mục tiêu và phạm vi
- Tiếp tục chốt quy ước kiểu dữ liệu cho schema MySQL và Spring Boot.

### Đã thực hiện
- Cập nhật `DATABASE_DESIGN.md` với VND nguyên đồng, image path, enum String và timestamp theo giờ Việt Nam.

### Thiết kế và quyết định
- Đã chốt: tiền VND lưu số đồng nguyên (`100.000` hiển thị → `100000` trong `BIGINT`); ảnh lưu đường dẫn/reference; enum lưu string (`VARCHAR`/JPA `EnumType.STRING`); thời gian lưu giờ Việt Nam UTC+07:00 trong MySQL `DATETIME(6)`, API mang offset `+07:00`.
- `DATETIME` không giữ timezone; backend thống nhất diễn giải theo `Asia/Ho_Chi_Minh`, không dùng timezone mặc định của máy.
- Cách biểu diễn UUID vật lý trong MySQL vẫn chưa chốt.

### Knowledge Map
- Không đổi trạng thái; các nội dung trên là quyết định thiết kế.

### Next step
- Chốt UUID physical representation trước migration đầu tiên.

## 2026-09-24 — [Round 0] Chốt ID, tên bảng và tiền tệ

### Mục tiêu và phạm vi
- Ghi các quyết định schema tiếp theo và đề xuất quy ước lưu thời gian.

### Đã thực hiện
- Cập nhật `DATABASE_DESIGN.md` về UUID, tên bảng số ít, VND nguyên đồng và lưu thời gian.

### Thiết kế và quyết định
- Đã chốt: UUID làm ID strategy; tên bảng số ít; tiền lưu theo đồng VND nguyên, ví dụ `100.000 VND` lưu `100000` trong signed `BIGINT`.
- Cần chốt thêm: UUID lưu vật lý dưới dạng `BINARY(16)` hay `CHAR(36)`.
- Đề xuất chờ xác nhận: lưu event/audit time UTC với `DATETIME(6)`, API ISO 8601 có `Z`, ứng dụng dùng `Instant`.

### Knowledge Map
- Không đổi trạng thái; đây là các quyết định schema, chưa phải bằng chứng năng lực đã học/áp dụng.

### Next step
- Xác nhận quy ước thời gian và cách encode UUID trong MySQL trước khi chốt migration đầu tiên.

## 2026-09-24 — [Round 0] Chốt domain rules cho database

### Mục tiêu và phạm vi
- Ghi các quyết định của user để hoàn thiện database contract trước khi thiết kế schema vật lý.

### Đã thực hiện
- Cập nhật `DATABASE_DESIGN.md` và `ARCHITECTURE.md` theo category, product variant, stock, cart, user profile, order snapshots, soft delete và Keycloak mapping.

### Thiết kế và quyết định
- Category chỉ một cấp; Product thuộc một Category.
- ProductVariant là tổ hợp Color + Size trong một Product; stock hiện tại đặt trực tiếp trên ProductVariant.
- Mỗi User có tối đa một Cart.
- User có profile riêng 1:1 gồm name, phone number và address; User lưu Keycloak ID duy nhất.
- Order lưu snapshot địa chỉ giao hàng; OrderItem lưu snapshot sản phẩm/variant/giá tại thời điểm mua.
- Xóa dùng soft delete; không xóa vật lý dây chuyền.
- Còn cần chốt: ID type/generation, table naming, monetary precision/currency, timezone/audit columns, ảnh, enum storage và quy tắc unique khi record đã soft delete.

### Knowledge Map
- Không đổi trạng thái; đây là quyết định thiết kế do user xác nhận, chưa phải bằng chứng về năng lực đã học/áp dụng.

### Next step
- Hoàn thiện table/column contract và ERD vật lý; sau đó mới tạo Flyway migration và JPA entities.

## 2026-09-24 — [Round 0] Database contract cho Spring Boot

### Mục tiêu và phạm vi
- Phân tích bộ tài liệu hiện có và xác định thông tin cần có để database design dẫn dắt Spring Boot implementation.
- Tạo draft `DATABASE_DESIGN.md`; chưa chốt schema vật lý hoặc tạo migration/entity.

### Đã thực hiện
- Ghi ERD khái niệm từ các domain Master Plan và đánh dấu cardinality cần xác nhận.
- Thêm template table contract và quy tắc Flyway → JPA → Repository/Service → DTO/Controller.
- Nối tài liệu vào README và Architecture.

### Kiến thức
- Kiến thức trực tiếp đã dùng: domain modeling, ERD, MySQL schema, Flyway, Spring Data JPA.
- Kiến thức đã ôn: phân biệt conceptual ERD với schema được duyệt và schema migration thực tế.
- Related concepts / internals / alternatives đã mở rộng: foreign key/delete behavior, constraints/indexes, entity mapping, lazy graph, transaction boundary.
- Điều đã hiểu về vì sao hệ thống hoạt động: migration là schema thực tế; entity phải map theo schema và API nên dùng DTO thay vì serialize entity.

### Thiết kế và quyết định
- Quyết định và lý do: tạo `DATABASE_DESIGN.md` làm hợp đồng giữa database design và Spring Boot; các đề xuất chưa được Master Plan chốt được ghi rõ là cần xác nhận.
- Trade-off / điểm còn mở: cardinality category, ID strategy, variant uniqueness, inventory ownership, cart lifecycle, order snapshots, delete policy, money/time/image/enum conventions.

### Debug / Test
- Chưa có code hoặc migration để kiểm tra; chưa đánh dấu schema là hoàn tất.

### Knowledge Map
- Không đổi trạng thái vì session này tạo tài liệu draft, chưa có bằng chứng năng lực mới được tự giải thích/triển khai/debug.

### Next step
- Chốt các mục `CẦN CHỐT` trong `DATABASE_DESIGN.md` và vẽ ERD/schema hoàn chỉnh trước migration đầu tiên.
## 2026-09-25 — [Round 1] Kiểm tra scaffold FE/BE

### Mục tiêu và phạm vi
- Kiểm tra project React và Spring Boot vừa khởi tạo, đối chiếu với Round 1; chưa sửa code hoặc chạy build.

### Đã thực hiện
- Tìm thấy Spring Boot tại `be/e_commerce_be` với Maven Wrapper, Java 21, Spring Boot 4.1.1, JPA, WebMVC và WAR packaging.
- Tìm thấy React 19 + Vite 6 tại `fe/e_commerce_fe`; `App.jsx` vẫn là màn hình mẫu Vite.
- Backend chưa khai báo MySQL/Flyway và `application.yaml` chưa có datasource config.
- Chưa tìm thấy Git repository ở project root hoặc hai thư mục con.

### Kết luận
- FE và BE đã có scaffold, nhưng Round 1 checkpoint chưa hoàn tất: chưa có monorepo Git, MySQL migration, API gọi DB hay FE gọi BE end-to-end.
- Actual paths dùng `be`/`fe` với project lồng một cấp; baseline `PROJECT_STRUCTURE.md` ghi `backend/` và `frontend/`. Cần xử lý sự khác biệt này trước khi xem cấu trúc là hoàn tất.
- Chưa chạy build/runtime verification.

### Knowledge Map
- Không đổi trạng thái; chỉ kiểm tra scaffold, chưa có bằng chứng năng lực mới.

### Next step
- Xác nhận hoặc cập nhật folder mapping; sau đó tạo Git root/ignore rules, chốt versions, thêm MySQL + Flyway và kiểm tra migration/API/FE→BE theo checkpoint.

## 2026-09-25 — [Round 1] Chốt cấu trúc thư mục theo hiện trạng

### Mục tiêu và phạm vi
- Kiểm tra cấu trúc sau khi người học sắp xếp lại project và chốt tài liệu theo folder hiện tại.

### Đã xác minh
- Project root có `e_commerce_be/`, `e_commerce_fe/` và `docs/`.
- Backend scaffold Spring Boot/Maven; frontend scaffold React/Vite.
- Cập nhật `PROJECT_STRUCTURE.md` để dùng đúng tên folder hiện tại. Giữ một React app Customer/Admin và một Spring Boot monolith trong cùng Git monorepo.
- Chưa có Git repository, cấu hình MySQL/Flyway hoặc FE → BE → DB verification.

### Knowledge Map
- Không đổi trạng thái; đây là checkpoint cấu trúc, chưa có bằng chứng năng lực mới.

### Next step
- Tạo Git repository ở project root và `.gitignore` dùng chung; xác nhận `node_modules/`, `target/`, secrets và uploads được loại khỏi Git. Sau đó xác minh project chạy độc lập trước khi cấu hình MySQL/Flyway.

## 2026-09-25 — [Round 1] Git monorepo đã push

### Mục tiêu và phạm vi
- Tạo commit đầu tiên và đưa monorepo lên GitHub theo repository đã cấu hình.

### Đã xác minh
- Git root là `C:\Users\Public\Learn\E-comerce`; branch local `main` tracking `origin/main`.
- Commit `e236ae0` (`Init`) tồn tại local; `git ls-remote` xác nhận `origin/main` trỏ đúng commit này.
- Working tree sạch sau push.
- `.gitignore` loại dependencies/build output/upload; các file source và docs được commit.

### Next step
- Xác minh FE/BE chạy độc lập; tiếp đó cấu hình MySQL + Flyway và hoàn thành luồng FE → BE → DB.

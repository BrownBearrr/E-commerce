# Project Status — E-commerce

**Cập nhật:** 2026-09-25  
**Trạng thái hiện tại:** **Round 0 — COMPLETE. Round 1 — foundation đang thực hiện.** Đã khởi tạo project React/Vite và Spring Boot tại `e_commerce_fe` và `e_commerce_be` dưới `C:\Users\Public\Learn\E-comerce`. Chưa xác minh Git monorepo, MySQL/Flyway, API kết nối DB hoặc luồng FE → BE → DB.

**Requirement baseline:** Master Plan hiện tại; không tự thêm yêu cầu ngoài Master Plan.

## Đây là gì

Trang này là điểm bắt đầu cho phiên ChatGPT/Codex mới. Dùng nội dung ở đây và tài liệu liên kết để tiếp tục; không cần dựa vào lịch sử chat. Nếu code hoặc migration tồn tại, kiểm tra chúng trước khi sửa docs, vì migration là bằng chứng schema đã triển khai.

## Quyết định database đã chốt

- Category chỉ một cấp; mỗi Product thuộc một Category.
- ProductVariant là tổ hợp Color + Size trong Product; Color/Size được Admin nhập tự do vào record dùng chung toàn hệ thống, mỗi tổ hợp trong Product phải duy nhất.
- Stock hiện tại nằm trên ProductVariant; không tạo bảng current-stock trùng lặp.
- Mỗi User có tối đa một Cart.
- User có hồ sơ riêng 1:1 gồm `name`, `phone_number`, `address`; User lưu Keycloak user ID duy nhất.
- Order lưu snapshot tên người nhận, số điện thoại, địa chỉ và ghi chú; OrderItem lưu snapshot tên sản phẩm, SKU, Color, Size, giá và quantity, không lưu image path.
- SKU unique trong Variant active; sau khi Variant soft delete, SKU được phép tái sử dụng.
- Order snapshot gồm tên người nhận, số điện thoại, địa chỉ và ghi chú giao hàng. OrderItem snapshot gồm tên sản phẩm, SKU, Color, Size, giá và quantity; không lưu image path.
- Xóa dùng soft delete.
- Soft delete áp dụng cho Category/Product/Variant/User/Profile; Order và InventoryTransaction giữ lịch sử, không xóa vật lý.
- InventoryTransaction ghi Variant, quantity delta, stock before/after, reason, actor, time và order reference nếu có.
- Mỗi Cart chỉ có một CartItem cho mỗi Variant; thay đổi số lượng cập nhật cùng dòng.
- Tất cả primary key dùng Java UUID và JPA `GenerationType.UUID`; dùng mapping mặc định Hibernate cho MySQL (dự kiến `BINARY(16)`), xác minh dialect/DDL trước migration.
- Bảng dùng tên số ít; tên bảng/cột vật lý `snake_case`.
- Tiền là VND nguyên đồng: `100.000 VND` hiển thị được lưu là `100000` trong `BIGINT`.
- Thời gian lưu giờ Việt Nam UTC+07:00 bằng `DATETIME(6)`; backend diễn giải theo `Asia/Ho_Chi_Minh`; API có offset `+07:00`.
- Enum/status lưu dạng chuỗi (`VARCHAR`, JPA `EnumType.STRING`).
- Ảnh lưu file trong `e_commerce_be/uploads/products/` ở local, ngoài classpath; DB lưu storage key tương đối dạng `products/<productUuid>/<imageUuid>.<ext>`. Backend nhận upload và phục vụ ảnh qua API; client dùng URL API để hiển thị. Production cấu hình storage root tới thư mục/volume persistent. Khi tách microservices, Product Service nhận metadata/file và Gateway proxy URL ảnh. Không lưu binary ảnh trong DB, absolute path hay public URL.
- Dùng một Git repository (monorepo), một React app tại `e_commerce_fe/` với customer/admin route trees và `e_commerce_be/`. Frontend guard hỗ trợ trải nghiệm; backend Spring Security enforce quyền `USER`/`ADMIN`. API là JSON dưới `/api/v1`, phân trang `page`/`size`; error body có `timestamp`, `status`, `code`, `message`, `path`, thêm `fieldErrors` nếu validation lỗi.
- COD only; không tích hợp cổng thanh toán tiền thật.

## Round 0 — đầu ra hoàn tất

- ERD, table/column contract, FK, unique/check constraints, soft-delete strategy, snapshots, index baseline: [DATABASE_DESIGN.md](DATABASE_DESIGN.md).
- REST routes, access role, pagination, status/error convention, upload/serve ảnh: [API_DESIGN.md](API_DESIGN.md).
- Monorepo `e_commerce_fe/` + `e_commerce_be/`, single React customer/admin app, package/folder boundaries, uploads/security/Git: [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md).
- Local-first monolith, ảnh thuộc Product domain khi tách service, phương án strangler dự kiến Round 8: [ARCHITECTURE.md](ARCHITECTURE.md).
- Round 1 sẽ chọn/pin versions tương thích và xác minh generated DDL UUID/time/check/generated-column. Đây là kiểm tra implementation có bằng chứng, không phải quyết định Round 0 còn chờ user.

## Round 1 — trạng thái hiện tại

- Đã khởi tạo scaffold React/Vite trong `e_commerce_fe/` và Spring Boot trong `e_commerce_be/` tại project root `C:\Users\Public\Learn\E-comerce`; cấu trúc folder đã được chốt theo hiện trạng và ghi trong `PROJECT_STRUCTURE.md`.
- Frontend còn trang mẫu Vite. Backend có Maven, Java 21, Spring Boot 4.1.1, JPA và WebMVC; project khai báo WAR packaging. Chưa có dependency MySQL/Flyway hoặc datasource config trong `application.yaml`.
- Đã khởi tạo Git monorepo tại root, commit `e236ae0` (`Init`) đã push lên `origin/main` và xác minh remote có cùng commit; working tree sạch. Chưa chạy build/runtime check; chưa có DB migration hoặc luồng FE → BE → DB.
- Next: xác minh FE/BE chạy độc lập; sau đó cấu hình MySQL + Flyway, tạo endpoint kiểm tra DB và để FE gọi BE.
## Tài liệu chuẩn và thứ tự ưu tiên

1. `PROJECT_STATUS.md` — trạng thái và các việc tiếp theo hiện tại.
2. `DATABASE_DESIGN.md` — baseline domain/schema và mapping rules.
3. `API_DESIGN.md`, `PROJECT_STRUCTURE.md` — API contract và monorepo structure.
4. `ARCHITECTURE.md`, `TECH_STACK.md`, `ROADMAP.md` — architecture tổng thể, stack và phạm vi roadmap.
5. `LEARNING_WORKFLOW.md` — cách hướng dẫn người học theo feature.
6. `SESSION_LOG.md` — lịch sử quyết định/học; log giải thích lịch sử, không thay thế trạng thái hiện tại.
7. Code và Flyway migrations — nguồn sự thật về implementation/schema đã áp dụng. Khi khác docs, điều tra khác biệt và ghi nhận trước khi đổi.

Các quyết định mới được user xác nhận trong `DATABASE_DESIGN.md` sẽ cụ thể hóa các mô tả tổng quát hơn trong roadmap/Master Plan. Không tự biến gợi ý hoặc mục `CẦN CHỐT` thành quyết định.

## Quy trình phiên mới

1. Bắt đầu bằng `PROJECT_STATUS.md` và `README.md`.
2. Kiểm tra workspace/code và migration hiện có; không giả định dự án còn trắng hoặc trạng thái chat cũ còn đúng.
3. Đọc tài liệu theo feature: DB/schema thì `DATABASE_DESIGN.md`; thay đổi service boundary thì `ARCHITECTURE.md`; học theo quy trình thì `LEARNING_WORKFLOW.md`.
4. Nêu mục tiêu phiên, phần đã có, phần còn thiếu, checkpoint và kiến thức liên quan; hướng dẫn từng bước để người học tự code.
5. Sau khi có quyết định hoặc checkpoint đã xác minh: cập nhật `PROJECT_STATUS.md`, tài liệu domain bị ảnh hưởng và `SESSION_LOG.md`. Chỉ đổi `KNOWLEDGE_MAP.md` khi có bằng chứng người học giải thích/tự code/debug được.
6. Kết phiên bằng trạng thái hiện tại, file docs đã cập nhật và next step có thể đưa nguyên văn vào phiên sau.

## Prompt mở phiên mới

> Tiếp tục dự án E-commerce. Shared project docs nằm tại `C:\Users\Public\Learn\E-comerce\docs`; hãy đọc các tài liệu liên quan từ đúng thư mục này. Round 0 đã complete; Round 1 đang ở preflight. Mở/chọn thư mục code thật làm workspace, kiểm tra nội dung/Git trước khi tạo hoặc sửa; không dùng ChatGPT project mirror làm code repo. Sau đó hướng dẫn tôi từng checkpoint để tôi tự code, học sâu, debug/test và hiểu vì sao hoạt động. Giữ scope trong `TECH_STACK.md`; xác minh dependency versions và migration bằng project/command output. Sau mỗi checkpoint đã xác minh, cập nhật `PROJECT_STATUS.md`, `SESSION_LOG.md`, docs chuyên môn liên quan và `KNOWLEDGE_MAP.md` khi có bằng chứng năng lực. Nếu không truy cập được docs chung, báo rõ và không giả định đã đọc/cập nhật chúng.

## Điều kiện để ChatGPT thấy docs

Các file trên ổ đĩa không tự động xuất hiện trong mọi cuộc trò chuyện ChatGPT. Để bắt đầu phiên trong ChatGPT, mở chat bên trong Project E-commerce và thêm/cập nhật các file docs vào Project sources. Codex cần được mở tại workspace có code/docs hoặc được cấp quyền đọc/ghi thư mục docs chung. Nếu có nhiều bản copy, `C:\Users\Public\Learn\E-comerce\docs` là vị trí shared knowledge được chỉ định.







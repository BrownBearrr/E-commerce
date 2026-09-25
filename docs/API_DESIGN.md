# API Design — E-commerce

**Trạng thái:** Round 0 baseline hoàn tất. Đây là REST contract cho một frontend React gọi Spring Boot monolith; chi tiết DTO/mapping được triển khai ở từng feature và phải cập nhật tài liệu khi thay đổi.

## Quy ước chung

- Base path `/api/v1`; JSON UTF-8 cho request/response, ngoại trừ upload ảnh `multipart/form-data`.
- Resource dùng danh từ số nhiều, kebab-case nếu nhiều từ; ID là UUID dạng chuỗi trong URL/JSON.
- Collection response: `{ "items": [], "page": 0, "size": 20, "totalItems": 0, "totalPages": 0 }`. `page` zero-based, `size` mặc định 20, tối đa 100.
- Error response: `{ "timestamp": "...+07:00", "status": 400, "code": "VALIDATION_ERROR", "message": "...", "path": "...", "fieldErrors": [{"field":"...","message":"..."}] }`. `fieldErrors` chỉ có khi lỗi theo field.
- Timestamps serialize ISO-8601 với offset `+07:00`; tiền VND là integer đồng; enum là String.
- Public endpoints không yêu cầu login. `/me`, cart và customer orders yêu cầu role `USER`; catalog write, image upload/delete, inventory/admin orders yêu cầu role `ADMIN`. Spring Security enforce quyền; FE route guard chỉ UX.
- Browser dùng Keycloak OIDC Authorization Code + PKCE. Backend validate bearer JWT issuer/signature/audience/expiry và map role. Không tạo local password/login table.

## Endpoint baseline

| Method | Path | Access | Mục đích |
|---|---|---|---|
| `GET` | `/products` | Public | Danh sách active product; `page`, `size`, `q`, `categoryId`, `color`, `size`, `sort` |
| `GET` | `/products/{productId}` | Public | Chi tiết product và variants active |
| `GET` | `/categories` | Public | Danh mục active |
| `GET` | `/images/{imageId}` | Public | Trả bytes ảnh cùng Content-Type nếu product/image active |
| `POST` | `/admin/categories` | ADMIN | Tạo category |
| `PATCH` | `/admin/categories/{categoryId}` | ADMIN | Sửa category |
| `DELETE` | `/admin/categories/{categoryId}` | ADMIN | Soft delete; conflict nếu còn product active |
| `POST` | `/admin/products` | ADMIN | Tạo product và variant metadata ban đầu |
| `PATCH` | `/admin/products/{productId}` | ADMIN | Sửa product |
| `DELETE` | `/admin/products/{productId}` | ADMIN | Soft delete; không xóa order/history |
| `POST` | `/admin/products/{productId}/images` | ADMIN | Upload ảnh multipart; allow JPEG/PNG/WebP, tối đa 5 MiB mỗi ảnh và 10 ảnh/product; trả `imageId` và URL |
| `DELETE` | `/admin/products/{productId}/images/{imageId}` | ADMIN | Gỡ ảnh và xóa file an toàn |
| `POST` | `/admin/products/{productId}/variants` | ADMIN | Tạo Color/Size reference, SKU và variant |
| `PATCH` | `/admin/variants/{variantId}` | ADMIN | Sửa SKU/giá/dimensions theo constraint |
| `DELETE` | `/admin/variants/{variantId}` | ADMIN | Soft delete; active SKU/tổ hợp có thể dùng lại |
| `GET` | `/me/profile` | USER | Đọc profile cá nhân |
| `PUT` | `/me/profile` | USER | Tạo/cập nhật name, phone, address; upsert local user theo Keycloak subject |
| `GET` | `/me/cart` | USER | Đọc cart và dòng hàng |
| `PUT` | `/me/cart/items/{variantId}` | USER | Thêm variant hoặc đặt quantity mới; nếu variant đã có thì cập nhật cùng một cart item, không tạo dòng trùng |
| `PATCH` | `/me/cart/items/{cartItemId}` | USER | Đổi quantity dương |
| `DELETE` | `/me/cart/items/{cartItemId}` | USER | Xóa một dòng cart |
| `POST` | `/me/orders` | USER | Checkout COD; lấy snapshot profile/variant/giá và tạo order atomic |
| `GET` | `/me/orders` | USER | Lịch sử đơn của user hiện tại |
| `GET` | `/me/orders/{orderId}` | USER | Chi tiết đơn; chỉ owner mới đọc được |
| `GET` | `/admin/orders` | ADMIN | Tìm/lọc/phân trang đơn |
| `GET` | `/admin/orders/{orderId}` | ADMIN | Chi tiết đơn |
| `PATCH` | `/admin/orders/{orderId}/status` | ADMIN | Chuyển status theo transition hợp lệ |
| `POST` | `/admin/variants/{variantId}/inventory-adjustments` | ADMIN | Điều chỉnh tồn; ghi inventory transaction cùng transaction |
| `GET` | `/admin/variants/{variantId}/inventory-transactions` | ADMIN | Xem lịch sử tồn kho, phân trang |

Admin tạo variant bằng Color/Size dạng text trong request; backend chuẩn hóa/tra cứu record dùng chung và liên kết record phù hợp. Không expose CRUD public cho Color/Size; customer nhận giá trị qua product/variant response. Nếu create/update Product chứa nested variant, response phải nêu ID từng variant để tiếp tục quản lý tồn/ảnh.

## Status, lỗi và xử lý biên

- Dùng `400` cho payload/validation sai, `401` khi thiếu/sai token, `403` thiếu role, `404` resource không tồn tại/không nhìn thấy, `409` vi phạm trạng thái/unique/stock conflict, `413` ảnh vượt giới hạn, `415` media type không hỗ trợ, `500` lỗi không dự kiến.
- Checkout trả `201 Created` cùng order snapshot. Không tin giá/tổng/owner do client gửi; server tính từ dữ liệu hiện hành và profile đã lưu.
- Cập nhật cart/đơn/tồn dùng atomic service transaction. Request retry cho checkout/stock sẽ được idempotency strategy thiết kế trong feature liên quan trước khi bật retry tự động.
- Soft delete endpoint không trả payload dữ liệu đã xóa; response `204 No Content` khi thành công.
- Danh sách sort chỉ cho phép allowlist field; không ghép trực tiếp raw query parameter vào SQL.
- Ảnh: metadata lưu storage key tương đối trong DB; bytes nằm ở configured filesystem root. GET trả `Cache-Control` phù hợp và `X-Content-Type-Options: nosniff`; không nhận storage key tùy ý từ client. Backend kiểm tra magic bytes thay vì tin Content-Type do client khai báo.

## Quy tắc thay đổi API

Thay đổi breaking cần version/API migration plan; ghi endpoint, role, request/response, error và knowledge checkpoint trong session log. DTO không serialize JPA Entity. Đây là baseline scope Round 0, không bắt buộc triển khai tất cả endpoint ngay Round 1.

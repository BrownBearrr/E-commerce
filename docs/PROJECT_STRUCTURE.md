# Project Structure — E-commerce

**Trạng thái:** Round 0 baseline hoàn tất. Một Git monorepo, một React frontend cho Customer/Admin và Spring Boot backend monolith.

## Repository layout

```text
e-commerce/
├── e_commerce_fe/            # Một React app; customer/admin dùng route/layout riêng
├── e_commerce_be/            # Spring Boot monolith, Maven
│   ├── src/main/java/...     # application code
│   ├── src/main/resources/
│   │   ├── db/migration/     # Flyway migrations
│   │   └── application*.yml
│   └── uploads/products/     # Local files; ignored by Git; not classpath resources
├── docs/                     # Bản docs dùng trong repository nếu được chọn đồng bộ
├── .gitignore
└── README.md
```

Shared knowledge canonical path hiện tại: `C:\Users\Public\Learn\E-comerce\docs`. Nếu repository có bản `docs/`, phải đồng bộ có chủ đích; không để hai bản khác nội dung mà không thông báo.

## Frontend organization

```text
e_commerce_fe/src/
├── app/                 # bootstrap, router, providers
├── customer/            # customer pages/layout/features
├── admin/               # admin pages/layout/features
├── shared/              # reusable UI, API client, validation, utilities
├── auth/                # Keycloak OIDC integration, route guards
└── styles/
```

Customer và Admin chia route tree, page, layout và feature code; dùng chung API client, auth integration, design system và build. Không tạo frontend project thứ hai. Frontend guard ẩn/chặn điều hướng theo role để UX; mọi API authorization phải kiểm tra ở backend.

## Backend package organization

```text
e_commerce_be/src/main/java/<base-package>/
├── config/              # security, Jackson, persistence, app configuration
├── common/              # error model, exception handling, shared primitives
├── identity/            # local app user mapping và profile
├── catalog/             # category, product, image metadata, color, size, variant
├── cart/                # cart và cart item
├── order/               # order, order item, checkout
└── inventory/           # current stock operations và inventory transaction
```

Trong mỗi feature package, tổ chức theo trách nhiệm (`api`, `application`, `domain`, `persistence`) khi cần; tránh tạo abstraction rỗng. Controller gọi application/service; repository chỉ truy cập domain mà package sở hữu. Quan hệ/JPA mapping cross-domain được tối giản; module khác gọi service interface thay vì truy cập repository trực tiếp. Đây là modular monolith foundation cho Round 8.

## Resource, secrets và uploads

- `src/main/resources` dành cho config, migrations và static resource đóng gói; không đặt upload động trong JAR.
- Local storage root mặc định `e_commerce_be/uploads/products/`; cấu hình override bằng environment/application config cho production persistent mount.
- `.gitignore` loại `e_commerce_be/uploads/products/**`, trừ file `.gitkeep` nếu cần giữ folder rỗng, cùng IDE/build output và local secrets.
- Không commit `.env`, credential, signing key, DB password hoặc Keycloak secret. Dùng environment variables/local ignored config.
- Flyway migration đặt tên tuần tự `V1__...sql`; file migration đã chạy không sửa, tạo migration mới.

## Git conventions

- Một repository dùng nhánh mặc định `main`; feature branch ngắn theo việc đang làm, merge qua review/checkpoint cá nhân phù hợp cách học.
- Commit nhỏ theo một thay đổi có thể giải thích; không commit generated build output, IDE state, upload ảnh hoặc secret.
- Round 0 chốt monorepo và tổ chức thư mục. Repository Git thật được khởi tạo/xác minh trong Round 1 khi workspace code được tạo; không khẳng định đã có remote hoặc lịch sử commit nếu chưa kiểm tra.

## Quy tắc khi khởi tạo code

1. Kiểm tra workspace/remote hiện có trước khi tạo skeleton để không ghi đè project.
2. Chọn Java/Spring Boot/MySQL versions tương thích tại thời điểm tạo project, pin dependency/plugin versions và ghi vào `TECH_STACK.md`/README.
3. Tạo Spring project với Maven, Flyway, validation, web, JPA, MySQL, security theo round; không bật Hibernate schema auto-update.
4. Tạo React app JavaScript và router; không thêm Redux/query/UI libraries trừ khi feature cần theo Master Plan.
5. Cấu hình local profile, timezone và upload root; secrets giữ ngoài Git.



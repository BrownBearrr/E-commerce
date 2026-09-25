# E-commerce Project Knowledge Base

Thư mục này lưu tri thức dùng chung cho dự án E-commerce: roadmap, kiến trúc, phạm vi công nghệ, cách học theo feature và tiến độ từng buổi. Master Plan là nguồn chuẩn; khi có quyết định mới, cập nhật tài liệu liên quan và ghi lại thay đổi.

## Cách dùng

1. Khi bắt đầu phiên mới, xem [PROJECT_STATUS.md](PROJECT_STATUS.md) trước để lấy trạng thái hiện tại và prompt bootstrap.
2. Trước feature, xem đúng tài liệu liên quan: [ROADMAP.md](ROADMAP.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DATABASE_DESIGN.md](DATABASE_DESIGN.md) hoặc [TECH_STACK.md](TECH_STACK.md).
3. Thực hiện theo [LEARNING_WORKFLOW.md](LEARNING_WORKFLOW.md): mở rộng từ kiến thức trực tiếp sang internals, khái niệm liên quan, lựa chọn thay thế và trade-off.
4. Sau mỗi quyết định/checkpoint được xác minh, cập nhật `PROJECT_STATUS.md`, tài liệu chuyên môn liên quan và [SESSION_LOG.md](SESSION_LOG.md). Chỉ đổi trạng thái [KNOWLEDGE_MAP.md](KNOWLEDGE_MAP.md) khi có bằng chứng giải thích, tự triển khai hoặc debug được.
5. ChatGPT cần mở trong Project E-commerce và có docs mới nhất ở Project sources. File trên ổ đĩa không tự xuất hiện trong mọi cuộc chat. Codex cần workspace/quyền đọc ghi tới docs chung.

## Tài liệu

- [PROJECT_STATUS.md](PROJECT_STATUS.md) — điểm vào phiên mới, trạng thái hiện tại, thứ tự ưu tiên nguồn và prompt mở phiên.
- [ROADMAP.md](ROADMAP.md) — 3 phase, 15 round và checkpoints.
- [LEARNING_WORKFLOW.md](LEARNING_WORKFLOW.md) — quy trình học sâu qua feature.
- [KNOWLEDGE_MAP.md](KNOWLEDGE_MAP.md) — trạng thái học và knowledge areas khởi tạo.
- [ARCHITECTURE.md](ARCHITECTURE.md) — kiến trúc theo từng giai đoạn.
- [DATABASE_DESIGN.md](DATABASE_DESIGN.md) — ERD và schema contract MySQL/Flyway sang Spring Boot/JPA.
- [API_DESIGN.md](API_DESIGN.md) — REST endpoints, role, pagination và error contract.
- [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md) — monorepo layout, frontend/backend package boundaries, uploads và Git rules.
- [TECH_STACK.md](TECH_STACK.md) — stack, phạm vi và công nghệ chưa thuộc scope.
- [SESSION_LOG.md](SESSION_LOG.md) — mẫu ghi chép từng session.

## Nguyên tắc quản lý

- Local-first: hoàn thiện business và chạy local trước khi Docker/Deploy/CI-CD.
- Monolith trước, chỉ tách microservices sau khi monolith hoàn thiện.
- Thanh toán chỉ COD; không tích hợp cổng thanh toán tiền thật.
- Không tự thêm technology ngoài scope. Nếu cần mở rộng, ghi rõ lý do và cập nhật Master Plan trước.
- Với feature có persistence, chốt database contract trước; Spring Boot entity/repository phải ánh xạ theo schema và Flyway migrations.
- Ghi rõ ngày, feature, quyết định và trạng thái; không viết lại lịch sử session cũ.

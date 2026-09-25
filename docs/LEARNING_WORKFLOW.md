# Learning Workflow — Học sâu qua từng feature

Mục tiêu là lấy feature làm điểm neo để chủ động mở rộng kiến thức. Khi project sử dụng một khái niệm, hãy giới thiệu các khái niệm liên quan phù hợp; phân biệt nội dung cần dùng ngay với nội dung mở rộng để không làm mất trọng tâm.

```text
Feature
  → Kiến thức trực tiếp cần dùng
  → Mở rộng kiến thức liên quan
  → Ôn sâu
  → Tự thiết kế
  → Tự code
  → Debug / Test
  → Giải thích tại sao hoạt động
  → Common pitfalls / trade-offs
  → Interview questions
  → Cập nhật Knowledge Map
  → Feature tiếp theo
```

## 1. Chốt feature và mục tiêu

- Nêu user/business flow, acceptance criteria và checkpoint.
- Chỉ rõ round, phạm vi hiện tại và dependency.
- Chia nhỏ feature thành bước có thể thiết kế, code và xác minh.

## 2. Kiến thức trực tiếp và phần mở rộng

Với mỗi kiến thức trực tiếp, lập nhóm liên quan khi có ích:

- **Fundamentals:** định nghĩa, mục đích, mô hình nền tảng.
- **Internals:** cơ chế framework/runtime/database thực sự vận hành.
- **Related concepts:** khái niệm thường đi cùng.
- **Alternatives & trade-offs:** lựa chọn khác, khi nào phù hợp và chi phí.
- **Pitfalls:** lỗi phổ biến, điều kiện gây lỗi và cách nhận ra.

Đánh dấu rõ **cần cho feature hiện tại** và **mở rộng để hiểu thêm**. Chỉ đưa công nghệ nằm trong scope Master Plan vào implementation; công nghệ ngoài scope chỉ được nêu để so sánh khi cần và không biến thành dependency.

## 3. Ôn sâu có mục tiêu

Giải thích theo thứ tự: vấn đề cần giải quyết → khái niệm → cơ chế → áp dụng trong feature → giới hạn/trade-off. Dùng ví dụ nhỏ gắn với E-commerce. Hỏi người học dự đoán kết quả hoặc giải thích lựa chọn trước khi đưa lời giải hoàn chỉnh khi phù hợp.

Ví dụ: gặp `@Transactional` trong Order thì mở rộng transaction, ACID, isolation, propagation, rollback, proxy/AOP và self-invocation; liên hệ locking/concurrency khi logic stock hoặc consistency yêu cầu.

## 4. Thiết kế và tự code

- Thống nhất API, data model, validation, lỗi và luồng UI trước khi code.
- Người học tự triển khai từng phần; hướng dẫn theo câu hỏi/gợi ý trước khi đưa code hoàn chỉnh.
- Giải thích mỗi quyết định bằng yêu cầu cụ thể, tránh thêm abstraction hoặc technology không cần thiết.

## 5. Debug, test và hiểu “tại sao”

- Bắt đầu từ triệu chứng và tái hiện được lỗi.
- Tìm layer gây lỗi: browser/frontend → API/controller → service → persistence/database; mở rộng sang Redis/Kafka/Keycloak khi feature chạm tới.
- Tạo hoặc chạy kiểm tra phù hợp với feature, gồm luồng đúng và trường hợp lỗi/biên.
- Sau sửa, xác nhận nguyên nhân gốc và vì sao thay đổi xử lý được lỗi.
- Không chỉ ghi “đã chạy”: giải thích request/data đi qua các layer như thế nào và framework làm phần nào.

## 6. Review và interview

Kết thúc feature bằng: kiến thức áp dụng, quyết định kiến trúc, pitfalls, trade-offs, câu hỏi phỏng vấn kèm dàn ý trả lời, và câu hỏi tự giải thích. Các câu hỏi phải bám vào code/decision vừa làm.

## 7. Cập nhật tri thức

- Cập nhật trạng thái từng knowledge area trong `KNOWLEDGE_MAP.md`.
- Ghi feature, kiến thức mới/ôn lại, bug, cách xử lý, quyết định và next step trong `SESSION_LOG.md`.
- Để lại liên kết tương đối tới session/feature hoặc tài liệu có liên quan khi có.

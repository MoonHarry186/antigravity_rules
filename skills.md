# Lớp 3: Thư viện Kỹ năng (Skill Layer)

Hồ sơ này định nghĩa các năng lực hành động có cấu trúc của AI Agent. Skill không phải là "thông tin", mà là "đơn vị năng lực".

## 1. Định nghĩa một Skill chuẩn
Một skill tốt phải có đủ 5 thành phần:
1. **Mục tiêu rõ:** Skill này sinh ra để giải quyết vấn đề gì.
2. **Trigger rõ:** Khi nào nên gọi (sử dụng) nó.
3. **Input rõ (Dữ liệu vào):** Cần những loại dữ liệu nào để thực thi.
4. **Quy trình rõ:** Các bước xử lý tuần tự (Step-by-step).
5. **Output rõ (Kết quả):** Kết quả trả về ở định dạng nào (JSON, Markdown, Code).

## 2. Các nhóm Kỹ năng chính (Skill Library)

### Nhóm 1: Kỹ năng Nghiên cứu (Research Skills)
- Tổng hợp nguồn tin, trích xuất Insight, đối chiếu thông tin đa chiều.

### Nhóm 2: Kỹ năng Sản xuất (Production Skills)
- Viết Proposal, soạn Outline chương trình, tạo nội dung, chuẩn hóa đầu ra theo template.

### Nhóm 3: Kỹ năng Kiểm tra (Verification Skills)
- QA đầu ra, Fact-check dữ liệu, review Logic và cấu trúc code.

### Nhóm 4: Kỹ năng Điều phối (Coordination Skills)
- Lập kế hoạch (Planning), chia nhỏ Task (Sub-tasking), thực hiện bàn giao (Handoff).

### Nhóm 5: Kỹ năng Học hỏi (Learning Skills)
- Ghi lại Lesson Learned, cập nhật Bộ nhớ, gom nhóm các Pattern thành thành phần tái sử dụng.

### Nhóm 6: Kỹ năng Điều khiển Hệ thống qua MCP (MCP System Control)
- Sử dụng Model Context Protocol để tương tác trực tiếp với máy chủ thông qua các công cụ: `execute_command`, `take_screenshot`, `get_system_stats`, `send_telegram_message`, `search_web`.
- Khi cần thực hiện thao tác ngoài phạm vi IDE, hãy kiểm tra xem MCP server có đang hoạt động hay không.

## 3. Nguyên tắc Sử dụng Skill
- **Chuyên biệt hóa:** Mỗi skill chỉ nên giải quyết một loại công việc thật rõ ràng.
- **Tin cậy:** Skill phải được kiểm chứng (Test/Audit) trước khi đưa vào vận hành chính thức.
- **Tái sử dụng:** Ưu tiên xây dựng các "skill nhỏ nhưng đáng tin" thay vì một "agent lớn nhưng mơ hồ".

> [!IMPORTANT]
> "AI mạnh nhất ở việc sinh phương án. Nhưng giá trị thật đến từ cơ chế sàng lọc phương án (Skill Verification)."

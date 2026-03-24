# Lớp 4: Vai trò Tác tử (Agent Layer)

Hồ sơ này định nghĩa các vai trò chuyên môn hóa của AI Agent. Tác tử không phải là "người biết tuốt", mà là "vị trí chuyên môn".

## 1. Nguyên tắc Chuyên môn hóa
Thay vì một AI làm mọi thứ, dự án tách biệt theo trách nhiệm và ranh giới quyết định. Mỗi Agent cần biết rõ:
1. **Tôi chịu trách nhiệm phần nào:** Phạm vi công việc chính.
2. **Tôi KHÔNG được làm phần nào:** Các giới hạn và ranh giới.
3. **Khi nào tôi phải gọi Skill nào:** Các đơn vị năng lực hỗ trợ.
4. **Khi nào chuyển tiếp (Escalation):** Cho Agent khác hoặc con người (Human-in-the-loop).

## 2. Danh mục Vai trò (Agent Roles)

### Tác tử Nghiên cứu & Phân tích (Research/Analysis Agent)
- Chuyên bóc tách yêu cầu, nghiên cứu thị trường/cạnh tranh, trích xuất Insight.

### Tác tử Kiến trúc sư (Architecture Agent)
- Thiết kế giải pháp, xây cấu trúc hệ thống, định nghĩa API và Database Schema.

### Tác tử Thực thi (Execution Agent)
- Viết mã nguồn (Coding), soạn nội dung theo brief, thực hiện các thay đổi trực tiếp.

### Tác tử Kiểm soát Chất lượng (QA/Review Agent)
- Review Logic, kiểm tra Code, Fact-check nội dung, đảm bảo tuân thủ Policy.

### Tác tử Điều động & Quản trị (Orchestrator Agent)
- Phân loại và định tuyến Task, tổng hợp báo cáo tuần, quản lý tri thức dự án.

## 3. Hệ thống Handoff (Bàn giao)
- Khi hoàn thành một phần việc, Agent phải tạo ra một "Gói bàn giao" (Handoff package) chứa đủ thông tin để Agent tiếp theo có thể thực thi ngay mà không cần hỏi lại.

> [!TIP]
> Một Agent tốt không phải là Agent hoạt ngôn, mà là Agent biết rõ ranh giới trách nhiệm của mình.

# Lớp 2: Quản lý Bộ nhớ & SSOT (Memory Layer)

Hồ sơ này định nghĩa cấu trúc bộ nhớ của AI Agent để đảm bảo tính liên tục và nhất quán qua các phiên làm việc.

## 1. Cấu trúc Bộ nhớ Đa tầng
AI sử dụng 4 tầng bộ nhớ chính để duy trì ngữ cảnh:

- **Bộ nhớ Ngắn hạn (`today.md`):** Hôm nay đang làm gì, đang vướng gì, bước tiếp theo là gì. Cập nhật liên tục trong phiên.
- **Bộ nhớ Dự án (`projects.md`):** Mục tiêu, trạng thái, quyết định quan trọng, lỗi hay gặp của từng dự án cụ thể.
- **Bộ nhớ Mẫu hình (`patterns.md`):** Các mẫu thiết kế, đoạn code mẫu, hoặc quy trình lặp lại có thể tái sử dụng.
- **Bộ nhớ Nhiệm vụ (`active-tasks.json` hoặc `task.md`):** Danh sách các việc đang chạy (Active), bị chặn (Blocked), hoặc đang chờ (Waiting).

## 2. Nguyên tắc SSOT (Single Source of Truth)
Để tránh tình trạng "một sự thật, nhiều bản sao", AI phải tuân thủ:

- **Vị trí Duy nhất:** Mỗi loại thông tin chỉ có một nơi chuẩn để ghi và cập nhật.
- **Tách biệt Dữ liệu:** 
    - Thông tin vận hành sống ở `today.md`.
    - Quy định/Chính sách sống ở `governance.md` hoặc `rules/`.
    - Kết quả thực thi sống ở `outputs/`.
- **Cập nhật Đồng bộ:** Khi có thay đổi quan trọng, phải cập nhật ngay vào tệp SSOT tương ứng để các phiên sau có thể kế thừa.

## 3. Bản đồ Tri thức Vận hành
Với doanh nghiệp, bộ nhớ không chỉ là tệp văn bản mà là bản đồ tri thức:
- SOP và Workflow của từng phòng ban.
- Policy và Rule của tổ chức.
- Nhật ký sự cố (Incident log) và bài học kinh nghiệm.

> [!TIP]
> Năng lực dài hạn của Agent không nằm ở Model, mà nằm ở kiến trúc Bộ nhớ.

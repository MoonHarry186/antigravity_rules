---
trigger: always_on
---

# Tiêu chuẩn & quy tắc bám sát thiết kế (Design Fidelity)

Tài liệu này quy định quy trình và tiêu chuẩn bắt buộc để đạt độ trung thực tối đa so với thiết kế đã cung cấp (Figma, mockup, HTML tham chiếu, hoặc code tham chiếu).

## 1. Trích xuất “tỷ lệ vàng” (Golden Ratio Extraction)

Trước khi code, bạn **PHẢI** trích từ tài liệu thiết kế đúng các giá trị “golden ratios”:

- **Màu**: Dùng đúng mã HEX đã cho. **Không** được làm tròn / ước lượng.
- **Khoảng cách (spacing)**: Ánh xạ spacing về bội số gần nhất của 0.25rem (4px), hoặc dùng đúng pixel nếu thiết kế ghi là quan trọng (ví dụ `w-[600px]`).
- **Glassmorphism**: Trích đúng độ mờ nền, bán kính `backdrop-blur` (px), và độ mờ viền.
- **Typography**: Kiểm tra `letter-spacing` (tracking) và `line-height` (leading) cụ thể.
- **Chuyển động & trạng thái (HTML/CSS tham chiếu)**: Trích từ tham chiếu và stylesheet các giá trị **duration**, **delay**, **timing-function** (`ease`, `cubic-bezier`, keyframes) của `transition` / `animation`, cùng **transform** / **filter** — không chỉ dùng đường cong mặc định ở mục 2.

## 2. Quy tắc triển khai

- **Không magic number**: Mọi token thiết kế lặp lại **PHẢI** nằm trong khối `@theme` của Tailwind hoặc biến CSS.
- **Đặt tên biến theo ngữ nghĩa**: Dùng tên phản ánh ý đồ thiết kế (ví dụ `--color-surface-container-low` thay vì `--color-dark-gray`).
- **Thứ tự lớp (layering)**: Giữ đúng phân cấp trục Z theo mô hình độ cao (elevation) trong thiết kế.
- **Quy tắc “không viền cứng”**: Tránh viền `1px solid` để phân vùng layout trừ khi thiết kế yêu cầu rõ. Ưu tiên chênh tông màu hoặc “ghost border” mềm (độ mờ 10–15%).
- **Đường cong transition**: Với mọi tương tác cao cấp, dùng `cubic-bezier(0.2, 0.8, 0.2, 1)` để có cảm giác chuyển động “đằm và nặng”.
- **Quy tắc pha ảnh**: Ảnh trên nền tối **PHẢI** dùng `mix-blend-overlay`, `opacity-60`, và lớp gradient `from-background` để hòa vào môi trường.

## 3. Quy trình kiểm tra pixel-perfect

Tuyên bố “đã xong” **không có giá trị** nếu chưa kiểm tra trực quan:

1. **Rà soát trên trình duyệt**: Dùng công cụ trình duyệt để inspect phần tử, đối chiếu spacing/màu.
2. **So sánh cạnh nhau**: Chụp màn hình bản triển khai và đặt cạnh thiết kế/mockup gốc.
3. **Checklist kiểm tra**:
   - [ ] Tracking chữ khớp (ví dụ `-0.04em`).
   - [ ] Độ mờ glass khớp (ví dụ fill 60%, viền 15%).
   - [ ] Kích thước component đúng (ví dụ orb 600px).
   - [ ] Spacing thống nhất với yêu cầu “chừa hơi” (breathing room).
   - [ ] **HTML tĩnh tham chiếu đã chỉ định (nếu có)**: Style tính toán (computed) trong DevTools khớp trên các node quan trọng (màu, typography, spacing, opacity, `filter` / backdrop).
   - [ ] **HTML tĩnh tham chiếu đã chỉ định (nếu có)**: Ảnh chụp cạnh nhau cùng **độ rộng viewport** với bản tham chiếu; không lệch có hệ thống.

## 4. HTML tĩnh tham chiếu → React

Khi dự án **chỉ định một file HTML tĩnh** làm chuẩn triển khai (ví dụ `index.html` ở root, `reference.html`, `mockup.html`, hoặc bất kỳ đường dẫn `.html` đã thống nhất), file đó **cộng** CSS nhúng hoặc liên kết là nguồn bám thiết kế **bắt buộc** (cùng mức ưu tiên với Figma/mockup). **Tên file không cố định** — luôn bám đúng file mà repo hoặc người dùng xác định là tham chiếu.

- **Một nguồn chuẩn**: Coi HTML tham chiếu **và** mọi CSS liên kết/nhúng là chuẩn. Các giá trị lặp lại **PHẢI** đưa vào `@theme` Tailwind hoặc biến CSS trước khi React rải số tùy tiện.
- **Cấu trúc khớp**: Khi tách component, giữ nesting DOM, khung flex/grid, và stacking `z-index`; ranh giới component khớp các vùng hợp lý trong tham chiếu để layout và độ cao không đổi.
- **Port style**: Ưu tiên dùng chung stylesheet hoặc bộ token như bản tham chiếu; nếu map sang Tailwind, mỗi class phải truy về một rule đã đo từ tham chiếu (DevTools → **Computed**).
- **Font & viewport**: Khớp cách load font (họ, weight, subset) và kiểm tra cùng **độ rộng viewport** dùng khi đánh giá tham chiếu (metric font ảnh hưởng xuống dòng và spacing).
- **Chuyển động & tương tác**: Hover, focus, active **PHẢI** khớp thời gian và thay đổi hình ảnh của tham chiếu. Nếu tham chiếu có easing/duration thì dùng đúng; nếu không nói gì, áp mục **Đường cong transition** ở mục 2 làm mặc định.
- **Cổng hoàn thành**: Không được tuyên bố HTML→React đã khớp nếu chưa **đối chiếu computed style** trên các phần tử chính **và** chưa có **ảnh chụp cạnh nhau** (trang tham chiếu vs route React) cùng kích thước.

## 5. Cấm (vi phạm nghiêm)

- **Không được làm “tương đối”**: “Nhìn gần giống” là thất bại. Cần thì dùng eyedropper hoặc inspector trên tài liệu tham chiếu.
- **Không bịa spacing**: Thiết kế ghi 2.75rem thì không được dùng `p-10` (2.5rem). Dùng `p-[2.75rem]`.
- **Không quên trạng thái**: Hover, Focus, Active **PHẢI** theo thời gian transition và đổi màu trong thiết kế.
- **Không dùng trắng/đen thuần**: Trừ khi thiết kế quy định, dùng token `on-surface` và `background` của thiết kế.
- **Không bỏ qua HTML tham chiếu**: Nếu đã có **file HTML tĩnh tham chiếu đã chỉ định** mà vẫn code React theo trí nhớ hoặc chỉ theo Figma lỏng, không đối chiếu lại HTML/CSS đó, thì coi là thất bại.


---
trigger: always_on
---

# Frontend Coding Standards & Rules

Bộ quy tắc này đảm bảo mọi code frontend được tạo ra đều tuân thủ các tiêu chuẩn đã thiết lập về cấu trúc thư mục, gọi API, xác thực, UI và TypeScript — phù hợp với stack **Vite + React + TypeScript**, **React Router**, **Axios**, **Tailwind CSS**.

## 1. Cấu trúc thư mục (Directory Structure)

Mọi code trong `frontend/src/` phải tuân theo cấu trúc rõ ràng:

- `pages/`: Màn hình tương ứng route (một file hoặc thư mục con nếu màn hình lớn).
- `components/`: Component tái sử dụng (UI thuần hoặc có logic nhẹ); **không** nhét toàn bộ nghiệp vụ trang vào đây.
- `contexts/`: React Context (ví dụ: auth, theme) — state toàn cục có giới hạn.
- `lib/`: Client HTTP (`api.ts`), helpers, hằng số — **không** chứa JSX.
- `hooks/`: Custom hooks (`useXxx`) dùng chung.
- `types/` hoặc `*.types.ts` cạnh module: kiểu dữ liệu dùng chung giữa các trang/API.
- `assets/`: Hình ảnh, font tĩnh (import qua Vite).

## 2. Xử lý lỗi (Error Handling)

- **API:** Không bắt lỗi Axios rải rác bằng `try-catch` lặp lại không cần thiết; ưu tiên tách hàm `parseApiError`, hook `useQuery`/wrapper hoặc xử lý tập trung sau interceptor (khi mở rộng).
- **401 / refresh:** Giữ luồng refresh token và redirect đăng nhập trong `lib/api.ts` (hoặc module tương đương); không nhân bản logic này ở mọi page.
- **UI:** Hiển thị thông báo lỗi thân thiện (toast / inline / empty state), không chỉ `alert()` trừ prototype nhanh.
- **React:** Với lỗi render nghiêm trọng, cân nhắc **Error Boundary** ở cấp layout hoặc route cha.

## 3. Validation

- Form đăng ký / đăng nhập / nhập liệu quan trọng **phải** validate trước khi gọi API (giảm round-trip, UX tốt hơn).
- **Ưu tiên** thư viện schema (ví dụ **Zod**) khi dự án đã thêm dependency; schema có thể đặt cạnh page hoặc trong `lib/schemas/`.
- Thông báo lỗi validation phải khớp trường (field-level), tránh chỉ báo “Lỗi” chung chung.

## 4. Chuẩn hóa tiêu thụ API (Standardized Response)

- Mọi response từ backend tuân theo `IApiResponse<T>` / `IPaginatedResponse<T>` — frontend **phải** đọc `success`, `data`, `message`, `meta` một cách nhất quán.
- **KHÔNG** giả định `response.data` trực tiếp là payload nghiệp vụ nếu backend bọc trong `{ success, data }`; luôn lấy đúng lớp `data` đã thỏa thuận với API.
- Phân trang: đọc `meta` (page, limit, total, …) thống nhất với backend.

## 5. HTTP Client & biến môi trường

- Sử dụng **Axios** instance tập trung (ví dụ `src/lib/api.ts`): `baseURL` từ `import.meta.env.VITE_API_URL`, mặc định căn chỉnh với prefix **`/api/v1/`** của backend.
- **`withCredentials: true`** khi backend bật cookie / CORS credentials — giữ đồng bộ với cấu hình CORS phía server.
- **KHÔNG** hard-code URL production trong source; mọi URL môi trường qua biến `VITE_*`.

## 6. TypeScript Typing

- **KHÔNG** sử dụng `any` trừ trường hợp bất khả kháng (khi đó ghi chú ngắn lý do).
- Định nghĩa type cho props component, state phức tạp, và payload/response API (có thể trùng với type sinh từ schema).
- Bật strict mode trong `tsconfig` của frontend; build (`tsc -b`) phải pass trước khi merge.

## 7. Kiểm thử (Testing)

- **Ưu tiên** **Vitest** + **React Testing Library** (phù hợp Vite).
- Viết test cho: custom hooks quan trọng, util trong `lib/`, và luồng auth / form quan trọng nếu có thời gian.
- Tránh test chi tiết implementation nội bộ; tập trung hành vi người dùng và output.

## 8. Danh sách, lọc & phân trang (UI)

- Màn **danh sách** phải phản ánh query params của backend: `page`, `limit`, `sort`, `filter` — đồng bộ với API list (xem chuẩn backend CRUD & filtering).
- Trạng thái loading / empty / error phải rõ ràng (skeleton, empty state, retry nếu cần).
- URL có thể phản ánh filter (query string) khi UX cần chia sẻ link.

## 9. Routing & bảo vệ route

- Định nghĩa route tập trung (ví dụ trong `App.tsx` hoặc `routes/`); tránh duplicate path string khắp codebase — có thể dùng hằng số `ROUTES`.
- Route cần đăng nhập **phải** bọc bằng component kiểu `ProtectedRoute` (hoặc loader/guard tương đương), đồng bộ với `AuthContext`.
- Dùng `Navigate` / redirect sau login có chủ đích (tránh vòng lặp redirect).

## 10. Phiên bản API & đồng bộ backend

- Frontend **phải** gọi đúng phiên bản API mà backend expose (ví dụ `/api/v1/...`).
- Khi backend nâng `v2`, thêm biến env hoặc nhánh client rõ ràng; không trộn `v1`/`v2` trong cùng module mà không tách boundary.

## 11. Trạng thái ứng dụng & health

- Trang chủ hoặc layout có thể hiển thị trạng thái “offline” / lỗi kết nối dựa trên lỗi network — **không** bắt buộc gọi `/health` trừ khi product yêu cầu dashboard vận hành.
- Nếu gọi health: dùng cùng `baseURL` và xử lý response theo contract backend.

## 12. CORS & cookie (phối hợp backend)

- Frontend phụ thuộc cấu hình CORS phía server (`LINK_COR`, credentials). Không đổi `withCredentials` hoặc header tùy tiện mà không kiểm tra backend.
- Không lưu secret trong `localStorage`; chỉ token/session theo thiết kế đã chốt (hiện tại: token JSON trong `localStorage` — nếu đổi sang httpOnly cookie, cập nhật rule và `api.ts` đồng bộ).

## 13. Authentication (JWT & Context)

- **Bearer Token:** Gắn `Authorization: Bearer <accessToken>` qua interceptor (như `api.ts`), đọc token từ nguồn lưu trữ thống nhất.
- **Refresh:** Xử lý 401 + refresh tập trung; sau refresh thành công retry request gốc; thất bại thì xóa session và chuyển `/login`.
- **Context:** `AuthProvider` cung cấp `user`, `loading`, `login`, `logout`; component con dùng hook `useAuth()`, không prop-drilling sâu.

## 14. Code Formatting (Prettier)

- **Mọi dự án PHẢI sử dụng Prettier** (cùng tinh thần với backend) để định dạng thống nhất.
- Cấu hình Prettier khuyến nghị (đồng bộ với backend khi có monorepo):
  ```json
  {
    "semi": true,
    "trailingComma": "all",
    "singleQuote": true,
    "printWidth": 120,
    "tabWidth": 4
  }
  ```
- Nên có script `npm run format` / `npm run lint` trong `frontend/package.json` và chạy trước khi commit.

## 15. Quản lý mã nguồn (.gitignore)

- Thư mục `frontend/` **phải** ignore (hoặc root ignore bao phủ):
  - `node_modules/`
  - `dist/` (output Vite)
  - `.env` / `.env.local` / `.env.*.local`
  - `*.log`
  - `.DS_Store` / `thumbs.db`
- **KHÔNG** commit file môi trường chứa secret; chỉ commit `.env.example` với key mẫu (không giá trị thật).

## 16. UI, styling & icon

- **Design compliance (bắt buộc):** UI phải bám sát design được cung cấp (**Figma/mockup/design system**) một cách nghiêm ngặt; không tự ý thay đổi layout, spacing, màu sắc, typography, border radius, icon, hoặc trạng thái component khi chưa có phê duyệt.
- Sử dụng **Tailwind CSS** theo convention dự án (utility-first); tránh inline style dài lặp lại — tách class hoặc component nhỏ.
- **Icon:** Dùng **lucide-react** (hoặc bộ icon đã chốt trong dự án) một cách nhất quán; không trộn nhiều thư viện icon không cần thiết.
- **Tailwind v4 Setup:**
  - Phải sử dụng `@tailwindcss/vite` plugin trong `vite.config.ts`.
  - Sử dụng `@import "tailwindcss";` tại dòng đầu tiên của `src/index.css`.
  - Mọi cấu hình design tokens (colors, fonts, ...) phải thực hiện trong block `@theme { ... }` bên trong file CSS.
  - Không sử dụng `tailwind.config.js` trừ trường hợp đặc biệt cần tương thích ngược.
- Giữ **spacing, typography, màu** đồng bộ (design tokens / theme Tailwind); không magic number lặp lại quá mức.

## 17. Logging & môi trường dev

- **KHÔNG** để `console.log` / `console.debug` trong code production merge; dùng log có điều kiện `import.meta.env.DEV` hoặc xóa trước commit.
- Lỗi thực tế từ API nên được ghi nhận (UX + có thể tích hợp error reporting sau); tránh chỉ `console.error` không hành động.

## 18. Hiệu năng & truy cập (khuyến nghị)

- **Code splitting:** `React.lazy` + `Suspense` cho route/màn hình nặng khi cần.
- **Accessibility:** Nút có `aria-label` khi chỉ có icon; form có `label` gắn `htmlFor`; tương phản màu đọc được.
- **Hình ảnh:** Ưu tiên định dạng phù hợp, `loading="lazy"` cho ảnh below-the-fold khi dùng `<img>`.

## 19. Don't Do (Những điều không được làm)

- **Không phá vỡ design:** Không tự ý chỉnh layout, spacing, màu, font, icon, state khác với design đã duyệt.
- **Không hard-code môi trường:** Không hard-code API URL, token, secret, hoặc config production trong source.
- **Không lặp lại logic API/Auth:** Không copy-paste xử lý token refresh, parse error ở nhiều page/component; phải dùng module dùng chung.
- **Không lạm dụng `any`:** Không dùng `any` để né typing trừ khi bất khả kháng và có ghi chú rõ lý do.
- **Không trộn nhiều UI system:** Không dùng song song `antd` + `mui` trong cùng sản phẩm nếu chưa có quyết định kiến trúc.
- **Không commit code debug:** Không để `console.log`, code dead, commented-out block, hoặc TODO không có ticket trước khi merge.
- **Không bỏ qua state UX:** Không bỏ loading/empty/error state cho màn hình gọi API.
- **Không update state sai cách:** Không mutate state trực tiếp trong React; phải cập nhật immutable.
- **Không bỏ qua bảo mật hiển thị:** Không render trực tiếp HTML chưa sanitize (đặc biệt với rich text/editor).
- **Không thêm dependency tùy tiện:** Không cài thư viện mới khi chưa có use-case rõ ràng hoặc khi đã có thư viện tương đương trong dự án.


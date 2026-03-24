---
trigger: always_on
---

# Backend Coding Standards & Rules

Bộ quy tắc này đảm bảo mọi code backend được tạo ra đều tuân thủ các tiêu chuẩn đã thiết lập về xử lý lỗi, validation và cấu trúc thư mục.

## 1. Cấu trúc thư mục (Directory Structure)
Mọi code phải tuân theo cấu trúc:
- `server/src/common/`: Chứa các thành phần dùng chung (utils, middlewares, interfaces, config).
- `server/src/modules/`: Chứa các logic nghiệp vụ theo module (ví dụ: `user`, `order`).
    - Mỗi module bao gồm: `.controller.ts`, `.service.ts`, `.route.ts`, `.validation.ts`, `.model.ts`.

## 2. Xử lý lỗi (Error Handling)
- **KHÔNG** sử dụng `try-catch` trực tiếp trong Controller. Sử dụng wrapper `catchAsync`.
- **LUÔN** ném lỗi bằng class `ApiError(statusCode, message)`.
- Lỗi sẽ được xử lý tập trung tại `errorHandler` middleware.

## 3. Validation
- Sử dụng **Joi** để validate dữ liệu đầu vào (body, query, params).
- Áp dụng `validate` middleware trước khi vào Controller.
- Schema validation phải được định nghĩa trong file `.validation.ts` của module tương ứng.

## 4. Chuẩn hóa Phản hồi (Standardized Response)
- Mọi API response phải tuân theo interface `IApiResponse<T>` hoặc `IPaginatedResponse<T>`.
- Cấu trúc response luôn bao gồm: `{ success: boolean, data: ..., message?: string, meta?: ... }`.

## 5. Kết nối Database
- Sử dụng **Mongoose** (MongoDB).
- Các Model phải được định nghĩa rõ ràng với TypeScript interfaces.
- Kết nối DB phải được thực hiện thành công trước khi server lắng nghe (listen).

## 6. TypeScript Typing
- **KHÔNG** sử dụng `any` trừ trường hợp bất khả kháng.
- Luôn định nghĩa Interface/Type cho Request body, Response data, và Database Models.
- Sử dụng strict mode trong TypeScript.

## 7. Unit Testing
- Viết test cho các Middleware và Logic quan trọng trong Service.
- Sử dụng **Jest** làm framework testing chính.

## 8. CRUD & Filtering (Quy tắc bổ sung)
- **Mọi model dữ liệu PHẢI triển khai đầy đủ CRUD cơ bản:** Create (Tạo), Read (List & Detail), Update (Cập nhật), Delete (Xóa).
- **Hàm getList (Lấy danh sách):**
    - Phải hỗ trợ phân trang (pagination) mặc định.
    - Phải hỗ trợ lọc (filtering) theo các trường dữ liệu quan trọng của module (ví dụ: `status`, `category`, `role`, `priority`).
    - Phải hỗ trợ sắp xếp (sorting) theo thời gian tạo (`createdAt`) mặc định.

## 9. Tài liệu API (Swagger/OpenAPI)
- **Mọi Endpoint PHẢI được viết tài liệu Swagger đầy đủ.**
- Tài liệu phải bao gồm:
    - `Summary`: Mô tả ngắn gọn chức năng.
    - `Tags`: Phân loại theo module (User, Post, ...).
    - `Parameters`: Mô tả các tham số query, params.
- Không được viết swagger vào trong router mà phải viết ra file riêng
## 10. Routing Versioning (Quản lý phiên bản)
- **Hệ thống Router PHẢI được phân cấp theo phiên bản (Versioning).**
- Cấu trúc thư mục: `src/routes/v1/index.ts`.
- Mọi module router phải được tập trung tại file `index.ts` của phiên bản tương ứng.
- Khi cần nâng cấp lên v2, chỉ cần tạo thư mục `src/routes/v2/` và đăng ký trong `app.ts`.
- Tiền tố API (Prefix): `/api/v1/`.

## 11. Health Check (Kiểm tra sức khỏe)
- **Endpoint PHẢI là `/api/v1/health`.**
- Phản hồi phải bao gồm:
    - `uptime`: Thời gian chạy của server.
    - `status`: Trạng thái tổng quát (`UP`/`DOWN`).
    - `database`: Trạng thái kết nối cơ sở dữ liệu.
    - `timestamp`: Thời điểm kiểm tra.

## 12. CORS (Cross-Origin Resource Sharing)
- **PHẢI sử dụng middleware `cors`.**
- Cấu hình phải dựa trên biến môi trường `LINK_COR`.
- Các phương thức được phép: `GET, POST, PUT, PATCH, DELETE`.
- Phải hỗ trợ `credentials: true`.

## 13. Authentication (Xác thực JWT)
- **Cơ chế xác thực:** Sử dụng Bearer Token (JWT).
- **Mã hóa mật khẩu:** Sử dụng `bcryptjs` với salt round là 10.
- **Middleware:** Phải có `auth.middleware.ts` để kiểm tra token và phân quyền.
- **Biến môi trường:** Phải cấu hình `JWT_SECRET` và `JWT_EXPIRES_IN`.

## 14. Code Formatting (Prettier)
- **Mọi dự án PHẢI sử dụng Prettier để tự động chuẩn hóa định dạng code.**
- Cấu hình Prettier bắt buộc:
    ```json
    {
        "semi": true,
        "trailingComma": "all",
        "singleQuote": true,
        "printWidth": 120,
        "tabWidth": 4
    }
    ```
- Nên tích hợp lệnh `npm run format` để kiểm tra và định dạng lại mã nguồn trước khi commit.

## 15. Quản lý mã nguồn (.gitignore)
- **Mọi dự án PHẢI có file `.gitignore` để tránh commit các file không cần thiết.**
- Các thư mục/file bắt buộc phải ignore:
    - `node_modules/`
    - `dist/` hoặc `build/`
    - `logs/`
    - `.env`
    - `*.log`
    - `.DS_Store` (cho macOS) hoặc `thumbs.db` (cho Windows)

## 16. Seed Admin (Khởi tạo tài khoản quản trị)
- **Yêu cầu:** Mọi dự án PHẢI có cơ chế tự động khởi tạo ít nhất một tài khoản quản trị tối cao (`superadmin`) nếu cơ sở dữ liệu chưa có.
- **Vai trò:** Role bắt buộc phải là `superadmin`.
- **Bảo mật:** Thông tin `email` và `password` cho tài khoản seed phải được cấu hình thông qua biến môi trường (`ADMIN_EMAIL`, `ADMIN_PASSWORD`).
- **Thực thi:** Quá trình seed nên được thực hiện ngay sau khi kết nối Database thành công và trước khi server lắng nghe yêu cầu.

## 17. Logging (Ghi nhật ký hệ thống)
- **Yêu cầu:** PHẢI sử dụng thư viện **winston** để ghi log hệ thống.
- **Phân loại:** Log phải được phân theo các cấp độ (levels): `error`, `warn`, `info`, `http`, `debug`.
- **Định dạng:** Log phải bao gồm timestamp và được định dạng rõ ràng (ví dụ: JSON cho production, colorized text cho development).
- **Lưu trữ:**
    - Môi trường **Development:** Log ra Console.
    - Môi trường **Production:** Log ra cả Console và File (tách biệt `combined.log` và `error.log`).
- **Tích hợp:** Sử dụng logger thay cho `console.log` trong toàn bộ mã nguồn.
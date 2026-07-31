# Luna Nail Booking

## Tổng quan

Luna Nail Booking là ứng dụng quản lý lịch hẹn cho tiệm nail tại Việt Nam. Dự án được xây dựng với:

- Next.js App Router
- TypeScript
- Tailwind CSS
- Supabase

Mục tiêu của dự án là tạo nền tảng booking trực tuyến cho khách hàng, đồng thời hỗ trợ admin theo dõi và quản lý lịch hẹn, nhân sự, thanh toán và trạng thái booking.

## Trạng thái hiện tại

Dự án đang ở giai đoạn Foundation (Phase 1):

- Hoàn thiện cấu trúc public / admin responsive
- Xây dựng design token và layout chung
- Thiết lập Supabase, validation `.env`, migration, RLS cơ bản
- Chưa có hệ thống booking engine thực tế và dashboard dữ liệu đầy đủ
- Chưa có authentication middleware và quy trình thanh toán hoàn chỉnh

## Yêu cầu môi trường

- Node.js 22+
- pnpm 11+
- Supabase CLI (để chạy database local)

## Cài đặt nhanh

```bash
pnpm install
cp .env.example .env.local
pnpm dev
```

Sau khi copy, cập nhật các biến Supabase trong `.env.local` gồm `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY` và các biến cần thiết khác.

## Thiết lập database

```bash
supabase start
supabase db reset
```

Lệnh `supabase db reset` sẽ chạy toàn bộ migration trong `supabase/migrations` và populate dữ liệu từ `supabase/seed.sql`.

> Lưu ý: Khi schema đã vào production, không chỉnh sửa migration cũ trực tiếp. Tạo migration mới để bảo toàn lịch sử database.

## Chạy kiểm tra chất lượng

```bash
pnpm typecheck
pnpm lint
pnpm test:run
pnpm build
pnpm format:check
```

## Biến môi trường

Xem `./.env.example` để biết tất cả biến cần thiết.

- Các biến `NEXT_PUBLIC_*` có thể được expose ra browser.
- `SUPABASE_SERVICE_ROLE_KEY`, connection string, webhook secret, cron secret chỉ sử dụng bên server.
- `getServerEnv()` sẽ báo các biến thiếu khi khởi tạo các feature tương ứng.

## Kiến trúc thư mục

- `src/app`: routes và layouts theo App Router của Next.js.
- `src/components`: thành phần giao diện chung, không truy cập trực tiếp database.
- `src/features`: domain logic, truy vấn, hành động, schema và services.
- `src/lib`: helper chung và tích hợp hạ tầng.
- `src/config`: cấu hình trung tâm, danh sách trạng thái, permission matrix.
- `supabase/migrations`: schema versioned.
- `supabase/seed.sql`: dữ liệu seed idempotent.
- `tests`: unit / integration / E2E.
- `docs`: tài liệu kỹ thuật chuyên sâu.

## Phân quyền và trạng thái booking

Hiện tại đã định nghĩa các role cơ bản:

- `CUSTOMER`
- `STAFF`
- `RECEPTIONIST`
- `MANAGER`
- `OWNER`

Permission kiểm tra qua helper `can()`. Cơ chế phân quyền theo chi nhánh và RLS sẽ được hoàn thiện ở các phase sau.

Booking status và luồng chuyển trạng thái đã khai báo trong cả TypeScript và PostgreSQL. Các bước tiếp theo sẽ bổ sung:

- State machine transition
- Quy trình thanh toán
- Notification flow
- Xử lý no-show, hủy, đổi lịch

## Giới hạn hiện tại

Trong phiên bản hiện tại, một số tính năng vẫn còn ở mức khung:

- Admin dashboard là skeleton responsive, chưa đầy đủ dữ liệu thực tế
- Public page chưa có auth middleware đầy đủ
- Chưa có triển khai booking transaction, reports, realtime update
- Chưa có validation và workflow booking/payment hoàn chỉnh

## Hướng dẫn khắc phục lỗi phổ biến

- Nếu thiếu cấu hình Supabase public: kiểm tra `SUPABASE_URL` và `SUPABASE_ANON_KEY` trong `.env.local`.
- Nếu build không tải được Google Fonts: kiểm tra kết nối mạng hoặc triển khai font self-hosted.
- Nếu Supabase reset lỗi: kiểm tra Docker, Supabase CLI và phiên bản tương thích.

## Tài liệu tham khảo

- [Kiến trúc hệ thống](docs/architecture.md)
- [Sơ đồ database](docs/database-schema.md)
- [Luồng booking](docs/booking-engine.md)
- [Phân quyền](docs/permissions.md)

---

> Lưu ý: Workspace hiện tại không chứa thư mục `.git`, nên tôi không thể tự động push thay đổi lên nhánh `dev` từ đây. Nếu bạn muốn, tôi có thể hướng dẫn bạn các lệnh cần chạy để tạo git repo và đẩy lên `dev`.

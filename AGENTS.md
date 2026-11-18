# Agent Instructions — sprinthub-admin-service

API cho **Platform Admin** của SprintHub (NestJS, prefix `/admin-api/v1`). Phục vụ
`sprinthub-admin-ui`.

Repo trung tâm là `../sprinthub-be`: mở session ở đó; thiết kế ở
`../sprinthub-be/docs/design/design.md` (mục "Admin tách repo", ADR-007, ADR-015).

Trạng thái: **chưa scaffold**. Admin là P1, làm sau lõi task.

## Quy tắc bắt buộc

- **Đọc Redis trực tiếp, ghi chỉ qua `FCALL`.** Dùng Redis user `sh-admin-svc`. Mọi
  thao tác ghi gọi Function của library `sprinthub` do `sprinthub-be` sở hữu
  (`sh_suspend_user`, `sh_grant_admin`, `sh_revoke_admin`, `sh_set_platform_setting`,
  `sh_transfer_owner`, job verify/rebuild…). Không `JSON.SET`/`SADD`/`DEL` trực tiếp.
- Cần Function mới hoặc đổi chữ ký: sửa ở `sprinthub-be` trước, tăng `LIB_VERSION`.
  Khởi động thì kiểm tra `LIB_VERSION`; không khớp thì readiness fail.
- Mọi route qua guard Platform Admin (`SISMEMBER platform-admins`); không phải admin
  thì `404`. Hành động nhạy cảm cần sudo. Mọi request ghi `stream:audit`.
- Không đọc hay trả nội dung task/comment; chỉ metadata.
- Không `KEYS`/`SCAN` trong request; danh sách dùng `users-by-created`,
  `workspaces-by-created`.
- Lỗi trả `code` + `params`; không dịch chuỗi.

## Git

Trunk-based, public, MIT. Danh tính commit: `kiendt <kiendt2312.work@gmail.com>`.
Không push hay tạo PR khi chưa được yêu cầu.

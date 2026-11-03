

# Phần 1. Phân tích DNS

- Input: `phong-dieu-phoi.greenlog.vn`
- Output: Địa chỉ IP của máy chủ (nếu DNS phân giải thành công).

## Sơ đồ DNS Lookup

Người dùng nhập tên miền
        ↓
Trình duyệt kiểm tra DNS Cache
        ↓
DNS Resolver
        ↓
Root DNS Server
        ↓
TLD DNS Server (.vn)
        ↓
Authoritative DNS Server
        ↓
Trả về địa chỉ IP
        ↓
Trình duyệt kết nối máy chủ

**Nguyên nhân lỗi:**
Nam nhập sai `grenlog.vn` thay vì `greenlog.vn`.

DNS không tìm được bản ghi phù hợp nên xuất hiện
`DNS_PROBE_FINISHED_NXDOMAIN`.

NXDOMAIN cho biết tên miền được truy vấn không tồn tại
theo phản hồi DNS, không chứng minh máy chủ bị sập.

# Phần 2. Triển khai

## Phân biệt lỗi

| Lỗi | Ý nghĩa |
|---|---|
| NXDOMAIN | DNS không phân giải được tên miền |
| 500 | Máy chủ gặp lỗi nội bộ |
| 404 | Máy chủ không tìm thấy tài nguyên |

## Quy trình kiểm tra

1. Kiểm tra tên miền đã nhập.
2. Dùng `nslookup` để kiểm tra DNS.
3. Nhấn `F12` → Network → tải lại trang.
4. Kiểm tra HTTP Status Code:
   - 200: Thành công.
   - 404: Không tìm thấy tài nguyên.
   - 500: Lỗi nội bộ máy chủ.
5. Nếu NXDOMAIN, thông thường chưa có phản hồi HTTP
   từ máy chủ web đích.

**Kết luận:** Lỗi hiện tại thuộc giai đoạn phân giải DNS,
do nhập sai tên miền, không phải lỗi HTTP 500.

# Phần 3. Kiểm thử

Nếu sửa đúng tên miền nhưng nhận `404 Not Found`:

- DNS đã phân giải và có máy chủ HTTP phản hồi.
- Máy chủ không tìm thấy đường dẫn hoặc tài nguyên yêu cầu.
- Nguyên nhân có thể do URL sai, trang bị xóa hoặc
  cấu hình định tuyến chưa đúng.

**Cách khắc phục:** Kiểm tra URL, đường dẫn và cấu hình
routing của website.



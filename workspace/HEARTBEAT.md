# HEARTBEAT.md

## Trigger

**Cron:** Mỗi ngày lúc **9:30 AM** (Asia/Ho_Chi_Minh), thứ Hai đến thứ Sáu.
**Manual:** Khi Hiếu nhắn `đặt cơm` → chạy ngay quy trình bên dưới.

---

## Config

```
group_name: G, Đặt cơm Opla, A.00.11
sender:     Cơm văn phòng
```

---

## Quy trình thực hiện

**QUAN TRỌNG:** Mọi lệnh shell đều phải gọi qua tool `exec`. Đây là cách DUY NHẤT để chạy lệnh.

---

### Bước 1 — Mở Zalo Web

Gọi tool exec:
```json
{ "tool": "exec", "command": "open -a \"Google Chrome\" https://chat.zalo.me/" }
```

Chờ 5 giây để trang load:
```json
{ "tool": "exec", "command": "sleep 5" }
```

Chụp screenshot:
```json
{ "tool": "exec", "command": "screencapture /tmp/zalo_check.png" }
```

Gọi Claude CLI kiểm tra login:
```json
{ "tool": "exec", "command": "claude -p \"Nhìn vào screenshot tại /tmp/zalo_check.png. Zalo web đã login chưa? Trả về đúng 1 từ: LOGGED_IN hoặc NOT_LOGGED_IN\"" }
```

- Nếu kết quả chứa `NOT_LOGGED_IN` → gửi Telegram: `"[CơmBot] Cần login Zalo tại https://chat.zalo.me"` → **DỪNG**
- Nếu kết quả chứa `LOGGED_IN` → tiếp tục bước 2

---

### Bước 2 — Vào group

1. Nhìn sidebar trái, tìm group tên `G, Đặt cơm Opla, A.00.11`
2. Nếu không thấy ngay → dùng thanh tìm kiếm, gõ tên group, nhấn Enter
3. Click vào group
4. Chờ load xong, chụp screenshot:

```json
{ "tool": "exec", "command": "screencapture /tmp/zalo_group.png" }
```

Gọi Claude CLI xác nhận đúng group:
```json
{ "tool": "exec", "command": "claude -p \"Nhìn screenshot tại /tmp/zalo_group.png. Header của group chat có tên 'G, Đặt cơm Opla, A.00.11' không? Trả về đúng 1 từ: CORRECT hoặc WRONG\"" }
```

- Nếu `WRONG` → gửi Telegram: `"[CơmBot] Không tìm thấy group"` → **DỪNG**
- Nếu `CORRECT` → tiếp tục bước 3

---

### Bước 3 — Tìm menu hôm nay

1. Trong group, cuộn lên xem tin nhắn gần nhất
2. Tìm tin nhắn (ảnh) từ người tên `Cơm văn phòng`
3. Chụp screenshot vùng chat:

```json
{ "tool": "exec", "command": "screencapture /tmp/zalo_chat.png" }
```

Gọi Claude CLI kiểm tra có menu hôm nay không:
```json
{ "tool": "exec", "command": "today=$(date +%d/%m/%Y) && claude -p \"Nhìn screenshot tại /tmp/zalo_chat.png. Có tin nhắn ảnh menu nào từ 'Cơm văn phòng' hôm nay ($today) không? Trả về đúng 1 từ: HAS_MENU hoặc NO_MENU\"" }
```

- Nếu `NO_MENU` → gửi Telegram: `"[CơmBot] Không có menu mới hôm nay"` → **DỪNG**
- Nếu `HAS_MENU` → chụp screenshot riêng vùng ảnh menu → lưu `/tmp/zalo_menu.png` → tiếp tục

---

### Bước 4 — Phân tích menu

Gọi Claude CLI phân tích ảnh menu:
```json
{ "tool": "exec", "command": "claude -p \"Phân tích ảnh menu tại /tmp/zalo_menu.png. Đây là menu cơm văn phòng. Liệt kê tất cả các món theo thứ tự từ trên xuống dưới. Sau đó trả về duy nhất tên món CUỐI CÙNG trong danh sách (không kèm giá, không giải thích).\"" }
```

- Lưu kết quả làm `last_meal`
- Nếu Claude CLI lỗi hoặc trả về rỗng → gửi Telegram: `"[CơmBot] Không parse được menu"` → **DỪNG**

---

### Bước 5 — Nhắn tin đặt cơm

1. Trong group đang mở, click vào input box phía dưới
2. Gõ chính xác: `+1 [last_meal]` (thay [last_meal] bằng tên món từ bước 4)
3. Nhấn Enter để gửi
4. Chờ 3 giây, chụp screenshot:

```json
{ "tool": "exec", "command": "sleep 3 && screencapture /tmp/zalo_sent.png" }
```

Gọi Claude CLI xác nhận tin đã gửi:
```json
{ "tool": "exec", "command": "claude -p \"Nhìn screenshot tại /tmp/zalo_sent.png. Trong chat, có tin nhắn '+1 [last_meal]' vừa được gửi thành công không? Trả về đúng 1 từ: SENT hoặc NOT_SENT\"" }
```

- Nếu `NOT_SENT` → gửi Telegram: `"[CơmBot] Tin nhắn chưa gửi được"` → **DỪNG**
- Nếu `SENT` → tiếp tục bước 6

---

### Bước 6 — Ghi log

Tạo thư mục memory nếu chưa có:
```json
{ "tool": "exec", "command": "mkdir -p memory" }
```

Ghi log:
```json
{ "tool": "exec", "command": "echo '- Ngày: YYYY-MM-DD\n- Món đặt: [last_meal]\n- Trạng thái: thành công' >> memory/YYYY-MM-DD.md" }
```

(Thay YYYY-MM-DD bằng ngày thực tế, [last_meal] bằng tên món)

Không cần thông báo Telegram nếu thành công.

---

## Xử lý lỗi chung

Nếu gặp lỗi không nằm trong quy trình:
1. Ghi log lỗi vào `memory/YYYY-MM-DD.md`
2. Gửi Telegram: `"[CơmBot] Lỗi: [mô tả ngắn]"`
3. **DỪNG** — không cố tiếp tục

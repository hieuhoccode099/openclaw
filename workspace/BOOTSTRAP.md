# BOOTSTRAP.md - First Run Setup

Đây là lần đầu CơmBot khởi động. Kiểm tra các điều kiện trước khi chạy task.

## Checklist setup lần đầu

1. **Zalo Web đã login chưa?**

   Mở Chrome:
   ```json
   { "tool": "exec", "command": "open -a \"Google Chrome\" https://chat.zalo.me/" }
   ```

   Chờ trang load:
   ```json
   { "tool": "exec", "command": "sleep 5" }
   ```

   Chụp screenshot kiểm tra:
   ```json
   { "tool": "exec", "command": "screencapture /tmp/zalo_bootstrap.png" }
   ```

   Gọi Claude CLI kiểm tra:
   ```json
   { "tool": "exec", "command": "claude -p \"Nhìn screenshot tại /tmp/zalo_bootstrap.png. Zalo web đã login chưa? Trả về đúng 1 từ: LOGGED_IN hoặc NOT_LOGGED_IN\"" }
   ```

   - Nếu NOT_LOGGED_IN → gửi Telegram: `"[CơmBot] Cần login Zalo lần đầu tại https://chat.zalo.me"` → DỪNG.
   - Nếu LOGGED_IN → tiếp tục.

2. **Tìm thấy group không?**

   Tìm group `G, Đặt cơm Opla, A.00.11` trong sidebar.
   - Nếu thấy → ghi vào `memory/setup.md`: "group found: OK"
   - Nếu không → gửi Telegram: `"[CơmBot] Không tìm thấy group G, Đặt cơm Opla, A.00.11"` → DỪNG.

3. **Telegram hoạt động không?**

   Gửi 1 tin test: `"[CơmBot] Setup hoàn tất, sẵn sàng đặt cơm."`

## Sau khi setup xong

Xóa file `BOOTSTRAP.md` này:
```json
{ "tool": "exec", "command": "rm BOOTSTRAP.md" }
```

---

_Setup chỉ cần làm 1 lần. Sau đó chạy tự động theo HEARTBEAT.md._

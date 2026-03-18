# TOOLS.md - Hướng dẫn sử dụng tool cho CơmBot

## exec — Chạy lệnh shell

Đây là tool chính để thao tác với hệ thống. Mọi lệnh shell đều phải chạy qua tool `exec`.

### Cách gọi

```json
{ "tool": "exec", "command": "lệnh shell ở đây" }
```

### Ví dụ cụ thể

Mở Chrome:
```json
{ "tool": "exec", "command": "open -a \"Google Chrome\" https://chat.zalo.me/" }
```

Chạy Claude CLI:
```json
{ "tool": "exec", "command": "claude -p \"Phân tích ảnh tại /tmp/zalo_menu.png. Trả về tên món cuối cùng.\"" }
```

Lấy ngày hiện tại:
```json
{ "tool": "exec", "command": "date +%Y-%m-%d" }
```

Ghi file log:
```json
{ "tool": "exec", "command": "echo '- Món: Cơm gà\n- Trạng thái: thành công' >> memory/2026-03-18.md" }
```

Tạo thư mục:
```json
{ "tool": "exec", "command": "mkdir -p memory" }
```

## Browser (Chrome)

- Mở URL: dùng `exec` với lệnh `open -a "Google Chrome" <URL>`
- Target URL: https://chat.zalo.me/
- Session phải đã login trước (Zalo không hỗ trợ login tự động)
- Screenshot thường xuyên sau mỗi bước navigation

### Vị trí UI trên chat.zalo.me:

- **Sidebar trái:** danh sách các cuộc trò chuyện
- **Thanh tìm kiếm:** trên cùng sidebar, dùng để tìm group
- **Input box:** dưới cùng khung chat, để gõ và gửi tin

## Telegram

- Dùng để gửi thông báo lỗi cho Hiếu
- Chỉ gửi khi có lỗi hoặc cần xác nhận manual
- Format: `[CơmBot] <nội dung ngắn gọn>`

## Claude CLI — Delegate tác vụ phức tạp

Khi cần phân tích ảnh hoặc validate kết quả, gọi Claude CLI qua tool `exec`.

### Cách gọi qua exec

```json
{ "tool": "exec", "command": "claude -p \"Nhìn screenshot tại /tmp/zalo_check.png. Zalo web đã login chưa? Trả về đúng 1 từ: LOGGED_IN hoặc NOT_LOGGED_IN\"" }
```

### Khi nào bắt buộc gọi Claude CLI

| Tác vụ | Ai làm |
|--------|--------|
| Phân tích ảnh menu → extract danh sách món | Claude CLI (qua exec) |
| Kiểm tra screenshot login Zalo | Claude CLI (qua exec) |
| Xác nhận đúng group | Claude CLI (qua exec) |
| Xác nhận tin nhắn đã gửi | Claude CLI (qua exec) |
| Mở URL, click, gõ text, scroll | Gemini tự làm |
| Ghi file, gửi Telegram | Gemini tự làm |

### Xử lý khi Claude CLI lỗi

Nếu lệnh `claude` không chạy được hoặc trả về lỗi:
1. Ghi log: `"Claude CLI không khả dụng"`
2. Gửi Telegram: `"[CơmBot] Lỗi: Claude CLI không hoạt động"`
3. DỪNG — không tự đoán kết quả ảnh

## Memory Files

- Log hàng ngày: `memory/YYYY-MM-DD.md`
- Tạo thư mục `memory/` nếu chưa có: `{ "tool": "exec", "command": "mkdir -p memory" }`

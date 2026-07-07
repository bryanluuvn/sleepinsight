# SleepInsight — hướng dẫn dùng & bước tiếp theo

## Chạy thử ngay
Đây là 1 PWA thuần frontend, chưa cần backend để dùng thử:

1. Copy 4 file (`index.html`, `manifest.json`, `icon.svg`, `sw.js`) vào cùng 1 thư mục.
2. Serve bằng local server bất kỳ, ví dụ:
   ```
   npx serve .
   ```
   (mở trực tiếp bằng `file://` sẽ không đăng ký được service worker/manifest, cần http server)
3. Mở trên điện thoại (cùng wifi, hoặc deploy lên Vercel/Netlify/GitHub Pages là nhanh nhất) → menu trình duyệt → **"Thêm vào màn hình chính"** để cài như app thật.

## Hiện trạng (Phase 1 — MVP)
- Lưu dữ liệu bằng `localStorage` ngay trên máy — **chưa đồng bộ cloud**, gỡ app/xoá cache sẽ mất data. Đây là đánh đổi để bạn dùng thử ngay không cần setup Apps Script.
- Flow 3 mốc/ngày: tối (2 bước, ăn uống + vận động/tinh thần) → sáng (1 bước) → cuối ngày (1 bước, output).
- App tự nhận biết đang ở mốc nào dựa theo giờ hiện tại + dữ liệu đã có, không cần bạn tự chọn menu.
- Vòng tròn ngày (day ring) ở trang chủ: dải màu tượng trưng đêm→bình minh→ngày→hoàng hôn, cung sáng = khung giờ ngủ đêm qua, chấm trắng = thời điểm hiện tại.
- Tab Lịch sử: 14 ngày gần nhất + nút xuất CSV để phân tích sâu (Sheets, Python).

## Bước tiếp theo (theo đúng roadmap đã bàn)
1. **Đồng bộ Google Sheets** (khi bạn sẵn sàng): thêm Apps Script `doPost`/`doGet`, đổi `setLog()`/`loadLogs()` trong `index.html` để gọi API thay vì chỉ đọc/ghi `localStorage`. Vẫn giữ localStorage làm cache offline (queue rồi sync khi có mạng) — đúng pattern bạn đã dùng ở TripSplit.
2. **Notification nhắc giờ** (bạn nói để sau — hợp lý, cần thêm quyền + logic phức tạp hơn, nên làm sau khi flow nhập đã mượt).
3. **Sleep score từ Apple Watch**: Apple HealthKit **không có Web API public** để web app đọc trực tiếp — 2 hướng khả thi:
   - Dùng app trung gian như **Health Auto Export** (iOS) để tự động đẩy dữ liệu HealthKit (sleep stages, HR) qua webhook vào Apps Script/Sheets của bạn — không cần code native.
   - Hoặc tự build 1 iOS Shortcut đơn giản chạy mỗi sáng, lấy sleep data rồi POST vào endpoint Apps Script.
   Khi có nguồn này, thêm field `appleSleepScore`, `deepSleepMin`, `remMin` vào schema — không phá vỡ gì hiện có, chỉ merge thêm vào record theo `date`.
4. Khi đủ ~4-6 tuần data: bắt đầu phân tích correlation (CSV xuất sẵn từ tab Lịch sử dùng được ngay).

## Ghi chú thiết kế
- Bảng màu & mô-típ "day ring" dùng chung tinh thần với bộ nhận diện Bryan In Data (navy/blue/purple), nhưng thêm tông "dawn" (cam/hồng nhạt) riêng cho ngữ cảnh buổi sáng — mã hoá đúng ý tưởng chu kỳ ngủ-thức thay vì màu trang trí thuần tuý.
- Toàn bộ input dùng tap (segmented control, thang 5, +/- stepper) thay vì gõ chữ, đúng yêu cầu "input nhanh nhất có thể".

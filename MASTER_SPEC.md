# INVARIANTS.md
# LUẬT BẤT BIẾN — FUTURES AI
# 200 BOT CON + BOT THƯ KÝ + BOT CHÍNH

> Đây là luật bất biến của dự án.
> Codex phải đọc file này trước khi tạo, sửa, xóa, refactor hoặc tối ưu code.
> Không được tự ý thay đổi các quy tắc bên dưới.
> Nếu phát hiện mâu thuẫn, phải DỪNG và báo rõ, không tự quyết định thay đổi nghiệp vụ.

---

## 1. KIẾN TRÚC BẮT BUỘC

Hệ thống có 3 tầng logic:

200 BOT CON
→ BOT THƯ KÝ
→ BOT CHÍNH
→ BINANCE FUTURES

### 200 BOT CON
- Chỉ backtest/paper trade/nghiên cứu.
- Không được đặt lệnh tiền thật.
- Luôn duy trì đội hình 200 BOT hoạt động.
- BOT yếu có thể được tối ưu, đào thải và thay bằng BOT mới sau khi vượt qua kiểm thử.

### BOT THƯ KÝ
- Là tầng trung gian duy nhất giữa 200 BOT CON và BOT CHÍNH.
- Thu thập dữ liệu, chuẩn hóa, so sánh, đối chiếu kho mẫu và xác nhận hướng.
- Không được đặt lệnh tiền thật.
- Không được gửi 200 tín hiệu riêng lẻ cho BOT CHÍNH.

### BOT CHÍNH
- Không được giao dịch theo cảm tính.
- Không được thấy LONG/SHORT từ BOT THƯ KÝ rồi vào ngay.
- Phải tìm điểm vào, kiểm tra Entry Zone Score và Risk Engine trước khi đặt lệnh.
- Là thành phần duy nhất được phép đặt lệnh Futures thật.

---

## 2. TIMEFRAME BẤT BIẾN

Tất cả 200 BOT CON, BOT THƯ KÝ và kho mẫu chính đều dùng:

- 5 phút
- 15 phút
- 1 giờ

Nhưng vai trò là:

- 5m = khung xác nhận chính.
- 15m = khung xác nhận chính.
- 1h = khung bối cảnh/tham khảo.

### CỔNG SIMILARITY CỨNG

- 5m Similarity >= 90%.
- 15m Similarity >= 90%.

Nếu một trong hai < 90%:
→ ĐỨNG XEM.

1h KHÔNG được thay thế 5m hoặc 15m để vượt cổng 90%.

---

## 3. TOP 30

Nguồn nghiên cứu là Top 30 Binance Futures theo thanh khoản/chất lượng dữ liệu.

Ưu tiên:
- Khối lượng Futures 24h.
- Open Interest.
- Thanh khoản.
- Spread.
- Chất lượng dữ liệu.

Danh sách Top 30 được cập nhật động.

Coin rời Top 30 không bị xóa khỏi kho mẫu.

---

## 4. KHO MẪU

### Kho lõi
- Tối đa 1.000.000 mẫu.
- 500.000 WIN.
- 500.000 LOSS.

### Kho bổ sung
- Được lưu thêm vì cấu trúc thị trường thay đổi theo thời gian.
- Tổng dung lượng kho mẫu tối đa: 15 GB trên SSD VPS.

### Lưu trữ
- Lưu trên SSD/database.
- Không nạp toàn bộ kho mẫu vào RAM.
- Chỉ truy vấn tập ứng viên cần thiết.

### Quy tắc cuốn chiếu
- Mẫu mới hợp lệ liên tục được đưa vào.
- Mẫu kém giá trị/lỗi/trùng dư thừa có thể bị thay thế khi kho đầy.
- Không được xóa LOSS hợp lệ chỉ vì đó là LOSS.
- Không xóa dữ liệu chỉ để làm đẹp Win Rate.

---

## 5. MẪU PHẢI LÀ DỮ LIỆU THỰC

Không được coi một dự đoán của bot là mẫu thắng/thua nếu chưa có kết quả tương lai được xác minh.

Tại thời điểm tín hiệu T:
- Chỉ dùng dữ liệu <= T để tạo tín hiệu.
- Dữ liệu > T chỉ được dùng sau đó để xác định kết quả.

Cấm look-ahead bias.

Mẫu phải có kết quả:
- WIN
- LOSS
- HÒA

Dữ liệu lỗi/thiếu/không xác minh được:
→ KHÔNG HỢP LỆ
→ không đưa vào kho WIN/LOSS chất lượng.

---

## 6. MỘT MẪU KHÔNG CHỈ LÀ GIÁ

Mẫu phải lưu trạng thái thị trường tại thời điểm tín hiệu, tối thiểu gồm:

- Coin.
- BOT ID.
- Timestamp.
- LONG/SHORT.
- 5m.
- 15m.
- 1h.
- MACD.
- RSI6.
- RSI12.
- EMA.
- Volume.
- OI.
- ATR.
- Price Action.
- Force Score.
- Market Regime.
- Entry.
- Exit.
- SL.
- TP.
- MFE.
- MAE.
- Duration.
- PnL.
- Result.

---

## 7. WIN/LOSS QUALITY GATE

### WIN LONG
Ưu tiên:
- 15m có vùng cấu trúc/hỗ trợ phù hợp.
- 5m tạo đáy đã xác nhận.
- Lực bán suy yếu.
- Volume phù hợp.
- MACD quay lên.
- RSI6 + RSI12 quay lên.
- Giá thực tế đi đúng hướng.

### WIN SHORT
Ngược lại:
- 15m có vùng cấu trúc/kháng cự phù hợp.
- 5m tạo đỉnh đã xác nhận.
- Lực mua suy yếu.
- Volume phù hợp.
- MACD quay xuống.
- RSI6 + RSI12 quay xuống.
- Giá thực tế đi đúng hướng.

### Ngưỡng xác minh
- MFE >= +0.5 ATR → đạt ngưỡng đi đúng hướng.
- MAE <= -0.5 ATR → đạt ngưỡng đi ngược để xác định LOSS, hoặc phá cấu trúc/điều kiện LOSS theo luật xác minh.

### Thời gian xác minh
- 5m: tối đa 12 nến = 60 phút.
- 15m: tối đa 8 nến = 120 phút.

Hết thời gian mà chưa đủ WIN/LOSS rõ ràng:
→ HÒA / KHÔNG ĐỦ BẰNG CHỨNG.

---

## 8. GỘP MẪU

Chỉ gộp khi mẫu giống 100%.

- Không gộp 90%.
- Không gộp 95%.
- Không gộp 99%.

Nếu trùng 100%, gộp thành một nhóm/bản ghi nhưng phải giữ:
- Số lần xuất hiện.
- WIN.
- LOSS.
- HÒA.
- Win Rate.
- PnL.
- Lần xuất hiện gần nhất.
- Các metadata cần thiết.

---

## 9. SIMILARITY

Similarity phải tính bằng CODE.
Không được để AI phán đoán cảm tính.

Similarity được xác định theo 3 lớp:

### Lớp 1 — SO GIÁ TRỊ
30%

### Lớp 2 — SO HƯỚNG
30%

### Lớp 3 — SO ĐỘ DỐC + CẤU TRÚC
40%

Mẫu giống nhau không chỉ vì số gần nhau.

Phải so:
- Giá trị.
- Trạng thái.
- Hướng.
- Độ dốc.
- Tốc độ thay đổi.
- Quan hệ giữa các đường.
- Cấu trúc diễn biến.

Nếu hướng ngược nhau, phải bị phạt mạnh hoặc loại.

---

## 10. ĐỊNH NGHĨA HƯỚNG MACD

### MACD TĂNG
DIF đang hướng lên hoặc:
- Âm → 0 → dương.

### MACD GIẢM
DIF đang hướng xuống hoặc:
- Dương → 0 → âm.

Không được chỉ dùng DIF > DEA để kết luận MACD tăng.

---

## 11. ĐỊNH NGHĨA HƯỚNG RSI

Dùng RSI6 và RSI12.

### RSI TĂNG
- RSI6 đang hướng lên.
- RSI12 đang hướng lên.

### RSI GIẢM
- RSI6 đang hướng xuống.
- RSI12 đang hướng xuống.

Nếu RSI6 và RSI12 ngược hướng:
→ RSI XUNG ĐỘT.
→ Không coi là xác nhận mạnh.

---

## 12. BOT THƯ KÝ — ĐIỀU KIỆN KẾT LUẬN

BOT THƯ KÝ phải:
- Đọc kho WIN.
- Đọc kho LOSS.
- Đọc kết quả 200 BOT.
- Đọc thị trường hiện tại.
- Phân tích 5m/15m/1h.
- So MACD + RSI và các chỉ số liên quan.
- Tính Similarity.
- Kiểm tra số mẫu.
- So WIN với LOSS.

Phải có >= 100 mẫu tương đồng hợp lệ mới được xét tiếp.

Nếu <100:
→ ĐỨNG XEM.

Nếu:
- 5m <90% hoặc
- 15m <90%

→ ĐỨNG XEM.

Nếu Similarity LOSS >= Similarity WIN:
→ ĐỨNG XEM.

Nếu LOSS chiếm ưu thế rõ rệt trong tập mẫu tương đồng:
→ ĐỨNG XEM.

BOT THƯ KÝ chỉ trả về:
- LONG
- SHORT
- ĐỨNG XEM

BOT THƯ KÝ không được đặt lệnh.

---

## 13. BOT CHÍNH — KHÔNG VÀO NGAY

Nếu BOT THƯ KÝ báo LONG/SHORT:
→ chưa được vào ngay.

BOT CHÍNH phải:
1. Tìm vùng Entry.
2. 15m xác định vùng cấu trúc.
3. 5m xác định Swing Low/Swing High.
4. Chờ nến xác nhận đóng.
5. Tính Entry Zone Score.
6. Kiểm tra Risk.
7. Mới đặt lệnh.

Cấm đuổi giá.

---

## 14. SWING LOW / SWING HIGH

### Swing Low 5m
Ứng viên khi:
- Low[n] < Low[n-1]
- Low[n] < Low[n-2]

Sau đó phải có nến xác nhận tăng và các tín hiệu phù hợp:
- Lực bán suy yếu.
- Force phù hợp.
- MACD cải thiện.
- RSI cải thiện.

### Swing High 5m
Ứng viên khi:
- High[n] > High[n-1]
- High[n] > High[n-2]

Sau đó phải có nến xác nhận giảm và:
- Lực mua suy yếu.
- Force phù hợp.
- MACD suy yếu.
- RSI suy yếu.

Không dùng nến đang hình thành để xác nhận.

LIVE tuyệt đối không được nhìn trước dữ liệu tương lai.

---

## 15. ENTRY ZONE SCORE

Công thức:

- 25% Cấu trúc giá.
- 20% Price Action.
- 20% Force Score.
- 15% MACD.
- 10% RSI.
- 10% Volume.

Ngưỡng:
- >=80 → được phép vào.
- 70–79 → tiếp tục chờ.
- <70 → không vào.

Similarity và Entry Score là hai cổng độc lập.

---

## 16. ENTRY ORDER

LIMIT là ưu tiên.

MARKET chỉ là ngoại lệ khi:
- Entry Score >=80.
- Điểm vào đã xác nhận mạnh.
- Giá đang rời vùng Entry quá nhanh.
- Risk Engine PASS.

Cấm đuổi giá.

Thời gian chờ Entry tối đa:
- 2 giờ kể từ lúc BOT THƯ KÝ xác nhận.

Hết 2 giờ chưa có Entry hợp lệ:
→ HỦY KỊCH BẢN.

---

## 17. FUTURES POSITION

- 5 USDT = MARGIN/lệnh.
- Leverage tối đa = x20.
- Risk Engine được dùng leverage thấp hơn.
- Không tăng leverage để gỡ lỗ.
- Tối đa 2 vị thế.
- Một coin không được LONG và SHORT đồng thời.

Nếu đang LONG mà có tín hiệu SHORT:
- Đánh giá vị thế LONG.
- Nếu cần đóng → đóng LONG.
- Xác nhận Binance đã đóng.
- Sau đó mới tìm Entry SHORT.

Không đảo hai chiều đồng thời.

---

## 18. FORCE SCORE

Công thức:
- MACD Momentum: 30%.
- RSI Momentum: 20%.
- Volume Force: 15%.
- OI Force: 15%.
- Price Action: 10%.
- Multi-Timeframe Confirmation: 10%.

Phân loại:
- >=70 = CÒN LỰC.
- 50–69 = SUY YẾU.
- <50 = MẤT LỰC.

Theo dõi:
- FORCE_NOW.
- FORCE_PREVIOUS.
- FORCE_CHANGE.

Nếu giảm khoảng 15 điểm trong vài chu kỳ:
→ MẤT LỰC NHANH.

Tính riêng LONG_FORCE và SHORT_FORCE.

Nếu hai phía gần nhau:
→ ĐỨNG XEM.

Force Score không được ghi đè SL bảo hiểm cứng.

---

## 19. QUẢN LÝ KHI PNL ÂM

Khi PnL bắt đầu âm:
→ BOT CHÍNH phải quét realtime.

Kiểm tra:
- Force.
- Force Change.
- MACD 5m.
- MACD 15m.
- RSI6/RSI12 5m.
- RSI6/RSI12 15m.
- Volume.
- OI.
- Price Action.

Nếu lực ngược vị thế đang mạnh và tăng, cấu trúc bị phá, MACD/RSI xác nhận:
→ CẮT NGAY.

Nếu chỉ giật/rút râu và chưa có xác nhận đảo chiều:
→ GIỮ.

Không cắt chỉ vì PnL vừa âm vài cent.

---

## 20. CHỐT LỜI / TRAILING

- PnL >= +0.10 USDT → bắt đầu trailing.
- PnL >= +0.50 USDT → chốt 50%.
- Sau đó theo dõi đỉnh PnL.
- Nếu PnL giảm 0.10 USDT từ đỉnh gần nhất → đóng phần còn lại.

---

## 21. SL ĐỘNG

SL động do BOT CHÍNH quản lý, dựa trên:
- Force.
- MACD.
- RSI.
- Volume.
- OI.
- Price Action.
- Cấu trúc thị trường.

Có thể cắt sớm hơn SL bảo hiểm.

---

## 22. SL BẢO HIỂM TRÊN BINANCE

Đây là lớp bảo vệ cuối cùng, đặt trực tiếp trên Binance ngay sau khi vị thế khớp.

Mức bảo hiểm:
- khoảng -3 USDT/lệnh.

Mục tiêu:
- bot crash;
- VPS chết;
- mất mạng;
- API lỗi;
- WebSocket mất kết nối.

SL bảo hiểm phải tồn tại độc lập với bot.

Không đợi BOT CHÍNH mất 3 USDT mới cắt.
BOT CHÍNH có thể cắt sớm.

Nếu không đặt được SL bảo hiểm:
→ vị thế không được coi là an toàn.
→ xử lý khẩn cấp + Telegram.

---

## 23. DAILY LOSS LIMIT

- Daily Loss Limit = -5 USDT/ngày.

Khi PnL ròng ngày <= -5 USDT:
→ dừng mở lệnh mới.

Vẫn quản lý và bảo vệ vị thế đang mở.

PnL ròng gồm:
- Trading PnL.
- Fees.
- Funding.
- Slippage.

Reset:
- 00:00 Asia/Ho_Chi_Minh.

---

## 24. CHUỖI THUA

- 3 lệnh thua liên tiếp → tạm dừng mở lệnh mới.

Không Martingale.

Không tăng leverage để gỡ.

---

## 25. TỰ CẢI TIẾN 200 BOT

Điểm BOT 0–100:

- Win Rate 20%.
- Profit Factor 20%.
- PnL 15%.
- Drawdown 15%.
- Số mẫu xác minh 10%.
- Hiệu suất gần đây 10%.
- Hiệu suất theo Market Regime 10%.

BOT yếu:
→ giảm trọng số
→ tối ưu tham số
→ backtest
→ walk-forward
→ out-of-sample
→ paper trade.

Nếu vẫn yếu:
→ đào thải.

BOT bị đào thải không xóa lịch sử.

Slot được dùng để tạo BOT mới.

Luôn duy trì 200 BOT hoạt động.

Mỗi phiên bản phải lưu:
- parent_bot_id
- version
- parameters
- reason_created
- backtest_result
- paper_result

---

## 26. RANKING BOT KHÔNG ĐƯỢC VƯỢT CỔNG CỨNG

BOT score cao không được cứu:
- Similarity <90%.
- <100 mẫu.
- LOSS áp đảo.
- Entry Score <80.
- Risk FAIL.

---

## 27. EXECUTION ENGINE

Luồng:

BOT THƯ KÝ
→ LONG/SHORT
→ BOT CHÍNH tìm Entry
→ Entry Score >=80
→ Risk PASS
→ LIMIT ưu tiên
→ xác nhận khớp
→ đặt SL bảo hiểm
→ quản lý vị thế.

Không retry lệnh mù.

---

## 28. CHỐNG DOUBLE ORDER

Nếu lệnh timeout:
- Không gửi lại ngay.
- Kiểm tra trạng thái thật trên Binance.
- Xác định FILLED/PARTIAL/NEW/CANCELED/REJECTED.
- Sau đó mới quyết định.

Phải có:
- signal_id
- trade_id
- order_id
- position_id

---

## 29. PARTIAL FILL

Phải hỗ trợ:
- NEW
- PARTIALLY FILLED
- FILLED
- CANCELED
- REJECTED

Khối lượng quản lý phải dựa trên khối lượng thực tế đã khớp.

---

## 30. BINANCE ↔ DATABASE

Binance là nguồn sự thật cuối cùng.

Nếu:
Database != Binance

thì:
- ưu tiên trạng thái Binance;
- đồng bộ Database;
- dừng mở lệnh nếu còn bất thường.

---

## 31. RESTART / RECONNECT

Sau:
- VPS restart.
- Bot restart.
- WebSocket reconnect.
- API reconnect.

phải:
1. Đọc Binance.
2. Đọc Database.
3. Đối chiếu.
4. Xác nhận vị thế.
5. Xác nhận Open Orders.
6. Xác nhận SL bảo hiểm.
7. Khôi phục trạng thái.

Trong lúc đồng bộ:
→ KHÔNG MỞ LỆNH MỚI.

Chỉ trạng thái:
SẴN SÀNG GIAO DỊCH
mới được mở lệnh.

---

## 32. QUẢN LÝ 2 VỊ THẾ

Mỗi vị thế quản lý độc lập.

Mỗi vị thế có:
- Entry.
- SL.
- SL bảo hiểm.
- TP.
- Trailing.
- Force.
- PnL.
- Similarity.
- Entry Score.

Nếu cả hai cùng có vấn đề:
→ xử lý độc lập.

---

## 33. QUÉT VỊ THẾ REALTIME

Không dùng polling cố định nếu WebSocket cung cấp realtime.

Khi dữ liệu mới:
- giá;
- PnL;
- Force;
- MACD;
- RSI;
- Volume;
- OI

→ cập nhật quản lý ngay.

---

## 34. DATABASE / VPS

Mục tiêu:
- RAM 2 GB.
- SSD 65 GB.

Kho mẫu:
- tối đa 15 GB.

Không preload toàn bộ mẫu vào RAM.

SQLite phiên bản đầu.

---

## 35. TELEGRAM

Toàn bộ nội dung Telegram bằng tiếng Việt.

Chỉ giữ thuật ngữ chuẩn:
- LONG
- SHORT
- MACD
- RSI
- EMA
- ATR
- OI
- mã coin.

Không spam 200 BOT.

Phải có:
- Bảng ghim.
- Nhật ký.
- Xác nhận hướng.
- Điểm vào.
- Mở lệnh.
- Đóng lệnh.
- Chốt lời.
- Cắt lỗ.
- Cảnh báo.
- Báo cáo ngày.

---

## 36. TELEGRAM — PHÂN BIỆT HƯỚNG VÀ LỆNH

Nếu thư ký báo:

LONG

Telegram phải ghi:
> CHƯA VÀO LỆNH — BOT CHÍNH ĐANG TÌM ĐIỂM VÀO.

Khi Entry đạt:
> ĐIỂM VÀO ĐƯỢC XÁC NHẬN.

Khi thực sự khớp:
> MỞ LỆNH FUTURES.

---

## 37. BÁO CÁO HÀNG NGÀY

Thời gian:
- 21:00 giờ Việt Nam.
- Asia/Ho_Chi_Minh.
- UTC+7.

Phạm vi:
- 00:00:00 → 20:59:59.

Báo cáo phải có:
- Tổng giao dịch.
- LONG/SHORT.
- WIN/LOSS.
- Win Rate.
- Gross Profit.
- Gross Loss.
- Fees.
- Funding.
- Slippage.
- Net PnL.
- Top BOT.
- Top Coin.
- Similarity.
- Mẫu WIN/LOSS.
- Daily Risk.
- Drawdown.
- Chuỗi thua.
- Vị thế mở.
- Chi tiết từng giao dịch.
- Tình trạng hệ thống.

---

## 38. API KEY

Không hard-code.

Dùng .env:

BINANCE_API_KEY=
BINANCE_API_SECRET=

TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
TELEGRAM_ADMIN_CHAT_ID=

TIMEZONE=Asia/Ho_Chi_Minh

DAILY_REPORT_HOUR=21
DAILY_REPORT_MINUTE=0

---

## 39. CHẾ ĐỘ CHẠY

Phải có:
- BACKTEST
- PAPER
- LIVE

Mặc định:
PAPER

Không tự động bật LIVE.

---

## 40. TEST BẮT BUỘC

Phải test:
- Unit Test.
- Integration Test.
- Backtest.
- Walk-forward.
- Out-of-sample.
- Paper Trading.
- Risk.
- Execution.
- Restart.
- Reconnect.
- Database.
- Telegram.
- Timezone.

### Test Similarity
- 89.9% → FAIL.
- 90.0% → PASS.

Cả 5m và 15m đều phải đạt.

### Test Entry
- Similarity 95% + Entry Score 79 → KHÔNG VÀO.
- Similarity 95% + Entry Score 80 → được xét Risk.

---

## 41. KHÔNG TỰ Ý THAY ĐỔI

Codex tuyệt đối không được tự ý:
- giảm số BOT;
- bỏ BOT THƯ KÝ;
- bỏ BOT CHÍNH;
- bỏ kho WIN;
- bỏ kho LOSS;
- giảm ngưỡng 90%;
- đổi 5m/15m;
- để 1h thay thế 5m/15m;
- cho BOT CON giao dịch tiền thật;
- cho BOT THƯ KÝ đặt lệnh;
- cho BOT CHÍNH vào ngay khi thư ký báo LONG/SHORT;
- bỏ Entry Score;
- bỏ tìm đáy/đỉnh;
- bỏ Force Score;
- bỏ SL bảo hiểm;
- bỏ Daily Loss Limit;
- bỏ Telegram;
- đổi 21:00 Việt Nam;
- đưa toàn bộ kho mẫu vào RAM;
- xóa LOSS hợp lệ;
- dùng dữ liệu tương lai;
- martingale;
- DCA Futures vô hạn.

---

## 42. QUY TẮC KHI SỬA CODE

Trước khi sửa:
1. Đọc SPEC.md.
2. Đọc INVARIANTS.md.
3. Xác định phạm vi.

Sau khi sửa:
1. Chạy test.
2. Kiểm tra git diff.
3. Kiểm tra invariant.
4. Báo rõ thay đổi.
5. Không tuyên bố xong khi chưa test.

Nếu có mâu thuẫn:
→ DỪNG.
→ Nêu mâu thuẫn.
→ Không tự quyết định thay đổi nghiệp vụ.

---

## 43. NGUYÊN TẮC CUỐI

200 BOT CON:
> Tìm và thử nghiệm các kịch bản thật trên thị trường.

KHO MẪU:
> Lưu những gì thực sự đã xảy ra, cả WIN và LOSS.

BOT THƯ KÝ:
> So sánh hiện tại với lịch sử và xác nhận hướng.

BOT CHÍNH:
> Tìm điểm vào tốt nhất, quản lý rủi ro và thực thi.

BOT CHÍNH không được biến:
LONG/SHORT
thành
VÀO LỆNH NGAY.

Mọi lệnh thật phải qua:

BOT THƯ KÝ
→ Similarity
→ 100 mẫu
→ WIN/LOSS comparison
→ Entry
→ Entry Score
→ Risk
→ Execution
→ SL bảo hiểm.


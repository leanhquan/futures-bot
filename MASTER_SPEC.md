# SPEC.md
# FUTURES AI MASTER SPEC
# 200 BOT CON + BOT THƯ KÝ + BOT CHÍNH + KHO MẪU + BINANCE FUTURES

## 1. Mục tiêu

Xây dựng hệ thống giao dịch Binance Futures gồm:

TOP 30 COIN FUTURES
→ 200 BOT CON
→ BOT THƯ KÝ
→ BOT CHÍNH
→ BINANCE FUTURES

Vai trò:
- 200 BOT CON: phòng nghiên cứu, backtest, paper trade, thử kịch bản, tạo mẫu.
- BOT THƯ KÝ: thu thập, chuẩn hóa, so sánh, đối chiếu kho mẫu và xác nhận hướng.
- BOT CHÍNH: tìm điểm vào, quản lý rủi ro, thực thi lệnh Futures thật.

BOT CHÍNH tuyệt đối không giao dịch theo cảm tính.

## 2. Kiến trúc project

futures-bot/
├── bot_main/
├── agents_200/
├── database/
├── telegram/
├── SPEC.md
├── INVARIANTS.md
├── README.md
├── requirements.txt
├── .env
└── .env.example

Giữ project gọn, không tự tạo hàng chục thư mục nếu không cần.

## 3. 200 BOT CON

- Phải có 200 BOT CON hoạt động.
- Không được tự ý giảm số lượng.
- BOT CON không được đặt lệnh tiền thật.
- Chỉ backtest, paper trade, nghiên cứu và tạo dữ liệu.
- Có thể dùng một engine quản lý 200 agent/strategy thay vì 200 process.

Các BOT có thể có chiến lược khác nhau nhưng phải dùng cùng chuẩn timeframe.

## 4. Timeframe

Tất cả BOT CON, BOT THƯ KÝ và kho mẫu chính sử dụng:
- 5 phút
- 15 phút
- 1 giờ

Vai trò:
- 5m = khung xác nhận chính.
- 15m = khung xác nhận chính.
- 1h = khung bối cảnh/tham khảo.

Cổng Similarity bắt buộc:
- 5m Similarity >= 90%.
- 15m Similarity >= 90%.

Nếu một trong hai dưới 90%:
→ ĐỨNG XEM.

1h không được thay thế 5m hoặc 15m để vượt cổng 90%.

## 5. Top 30 coin

Nguồn nghiên cứu là Top 30 Binance Futures theo thanh khoản/chất lượng dữ liệu.

Ưu tiên:
- Khối lượng Futures 24h.
- Open Interest.
- Thanh khoản.
- Spread.
- Chất lượng dữ liệu.

Danh sách cập nhật động.
Coin rời Top 30 không bị xóa khỏi kho mẫu.

## 6. Chỉ báo

### MACD
Lưu:
- DIF
- DEA
- Histogram
- Khoảng cách DIF-DEA
- Hướng DIF
- Độ dốc DIF
- Độ dốc DEA
- Hướng Histogram
- Giao cắt

### RSI
Sử dụng:
- RSI6
- RSI12

Lưu:
- Giá trị
- Hướng
- Độ dốc
- Trạng thái

### Chỉ báo bổ sung
- EMA9/20/50/100/200
- Volume
- Volume trung bình
- Volume Ratio
- ATR/ATR%
- Bollinger Bands
- Open Interest/OI Change
- Funding Rate
- Long/Short Ratio
- Liquidation nếu có
- Price Action
- Market Regime

## 7. Định nghĩa MACD TĂNG/GIẢM

MACD TĂNG:
- DIF đang hướng lên; hoặc
- DIF chuyển âm → 0 → dương.

MACD GIẢM:
- DIF đang hướng xuống; hoặc
- DIF chuyển dương → 0 → âm.

Không được chỉ dùng DIF > DEA để kết luận MACD tăng.

## 8. Định nghĩa RSI TĂNG/GIẢM

RSI TĂNG:
- RSI6 hướng lên
- RSI12 hướng lên

RSI GIẢM:
- RSI6 hướng xuống
- RSI12 hướng xuống

Nếu RSI6 và RSI12 ngược hướng:
→ RSI XUNG ĐỘT.

## 9. Kho mẫu

### Kho lõi
- 500.000 WIN
- 500.000 LOSS
- Tổng 1.000.000 mẫu lõi.

### Kho bổ sung
Được phép lưu thêm mẫu lịch sử để theo dõi cấu trúc thị trường thay đổi.

### Giới hạn dung lượng
Tổng kho mẫu tối đa 15 GB trên SSD VPS.

Không nạp toàn bộ kho mẫu vào RAM.

## 10. Mẫu phải có kết quả thật

Một tín hiệu không tự động trở thành mẫu thắng/thua.

Tại thời điểm T:
- chỉ dùng dữ liệu <= T để tạo tín hiệu.
- dữ liệu > T chỉ dùng sau đó để xác minh kết quả.

Cấm look-ahead bias.

Phân loại:
- WIN
- LOSS
- HÒA
- KHÔNG HỢP LỆ

Mẫu không hợp lệ do lỗi dữ liệu, thiếu dữ liệu, không xác minh được kết quả hoặc lỗi mô phỏng không được đưa vào kho WIN/LOSS chất lượng.

## 11. Nội dung mẫu

Một mẫu phải lưu trạng thái thị trường tại thời điểm tín hiệu, tối thiểu:

- Coin
- BOT ID
- Timestamp
- LONG/SHORT
- 5m
- 15m
- 1h
- MACD
- RSI6
- RSI12
- EMA
- Volume
- OI
- ATR
- Price Action
- Force Score
- Market Regime
- Entry
- Exit
- SL
- TP
- MFE
- MAE
- Duration
- PnL
- Result

Một mẫu không chỉ là "giá tăng" hoặc "giá giảm".

## 12. WIN/LOSS chất lượng

### WIN LONG
Ưu tiên:
15m vùng hỗ trợ/cấu trúc phù hợp
→ 5m hồi về vùng
→ tạo đáy
→ lực bán suy yếu
→ Volume phù hợp
→ MACD quay lên
→ RSI6 + RSI12 quay lên
→ nến xác nhận tăng
→ giá thực tế đi đúng hướng.

### WIN SHORT
Ngược lại:
15m vùng kháng cự/cấu trúc phù hợp
→ 5m hồi lên vùng
→ tạo đỉnh
→ lực mua suy yếu
→ Volume phù hợp
→ MACD quay xuống
→ RSI6 + RSI12 quay xuống
→ nến xác nhận giảm
→ giá thực tế đi đúng hướng.

Volume + MACD + RSI là 3 chỉ báo trực quan cốt lõi.

## 13. MFE/MAE

Lưu:
- MFE = mức đi thuận hướng tốt nhất.
- MAE = mức đi ngược sâu nhất.

Chuẩn hóa theo ATR:
- MFE_R = MFE / ATR
- MAE_R = MAE / ATR

Ngưỡng xác minh:
- MFE >= +0.5 ATR → đạt mức đi đúng hướng tối thiểu.
- MAE <= -0.5 ATR → đạt mức đi ngược để xác định LOSS, hoặc phá cấu trúc/điều kiện LOSS theo luật xác minh.

## 14. Thời gian xác minh

- 5m: tối đa 12 nến = 60 phút.
- 15m: tối đa 8 nến = 120 phút.

Nếu hết thời gian mà chưa WIN/LOSS rõ:
→ HÒA / KHÔNG ĐỦ BẰNG CHỨNG.

## 15. Gộp mẫu

Chỉ gộp khi hai mẫu giống 100%.

Không gộp 90%, 95%, 99%.

Khi gộp phải giữ:
- số lần xuất hiện
- WIN
- LOSS
- HÒA
- Win Rate
- PnL
- lần xuất hiện gần nhất
- metadata liên quan.

## 16. Kho cuốn chiếu

Kho cập nhật liên tục.

Khi đầy:
- gộp mẫu trùng 100%;
- loại dữ liệu lỗi/không hợp lệ;
- loại bản sao dư thừa;
- loại mẫu cũ/ít giá trị theo chính sách chất lượng;
- giữ mẫu WIN/LOSS hợp lệ và cấu trúc hiếm.

Không được xóa LOSS hợp lệ chỉ vì đó là LOSS.

## 17. BOT THƯ KÝ

BOT THƯ KÝ là tầng trung gian duy nhất giữa 200 BOT CON và BOT CHÍNH.

Nhiệm vụ:
- thu thập dữ liệu kho WIN;
- thu thập dữ liệu kho LOSS;
- thu thập kết quả 200 BOT;
- chuẩn hóa;
- so sánh;
- đối chiếu kho mẫu;
- phân tích thị trường hiện tại;
- tính Similarity;
- kiểm tra số mẫu;
- so WIN với LOSS;
- kết luận LONG/SHORT/ĐỨNG XEM.

BOT THƯ KÝ không đặt lệnh thật.

## 18. Similarity

Similarity phải tính bằng code.

Định nghĩa mẫu giống:
- giống giá trị;
- giống trạng thái;
- giống hướng;
- giống độ dốc;
- giống tốc độ thay đổi;
- giống quan hệ giữa các đường;
- giống cấu trúc diễn biến.

### 3 lớp Similarity
- Lớp 1: SO GIÁ TRỊ = 30%
- Lớp 2: SO HƯỚNG = 30%
- Lớp 3: SO ĐỘ DỐC + CẤU TRÚC = 40%

Đặc biệt áp dụng cho:
- MACD
- RSI
- EMA
- Volume
- OI
- ATR
- Price Action

Nếu hướng ngược nhau phải bị phạt mạnh hoặc loại.

## 19. Cổng Similarity

Bắt buộc:
- 5m >= 90%
- 15m >= 90%

Cả hai phải đạt.

1h chỉ là bối cảnh.

## 20. Số mẫu tối thiểu

BOT THƯ KÝ phải có ít nhất:
- 100 mẫu tương đồng hợp lệ.

Nếu <100:
→ ĐỨNG XEM.

## 21. So sánh WIN/LOSS

Tính riêng:
- Similarity_WIN
- Similarity_LOSS

Ưu thế:
WIN_ADVANTAGE = Similarity_WIN - Similarity_LOSS

Nếu Similarity_LOSS >= Similarity_WIN:
→ ĐỨNG XEM.

Nếu LOSS chiếm ưu thế rõ rệt về số lượng/tỷ lệ trong tập mẫu tương đồng:
→ ĐỨNG XEM.

Không cho 200 BOT cứu một trường hợp mà kho mẫu đang cảnh báo.

## 22. Kết luận BOT THƯ KÝ

Chỉ có:
- LONG
- SHORT
- ĐỨNG XEM

Phải gửi kèm:
- Similarity 5m
- Similarity 15m
- 1h context
- số mẫu
- mẫu WIN
- mẫu LOSS
- Historical Win Rate
- MACD
- RSI
- lý do
- timestamp

## 23. BOT CHÍNH

BOT CHÍNH không được vào ngay khi nhận LONG/SHORT.

Phải:
1. Nhận hướng từ BOT THƯ KÝ.
2. Tìm vùng Entry.
3. 15m xác định vùng cấu trúc.
4. 5m xác định Swing Low/Swing High.
5. Chờ nến xác nhận đóng.
6. Tính Entry Zone Score.
7. Kiểm tra Risk Engine.
8. Mới đặt lệnh.

## 24. Đáy/Đỉnh

### 15m
Dùng để xác định:
- vùng cấu trúc
- hỗ trợ
- kháng cự
- retest

### 5m
Dùng để xác định:
- Swing Low
- Swing High
- điểm vào

Không bắt đáy/đỉnh mù quáng.

## 25. Swing Low / Swing High

### Swing Low 5m
Ứng viên khi:
Low[n] < Low[n-1]
và
Low[n] < Low[n-2]

Sau đó phải có:
- nến xác nhận tăng;
- lực bán suy yếu;
- Force phù hợp;
- MACD cải thiện;
- RSI cải thiện.

### Swing High 5m
Ứng viên khi:
High[n] > High[n-1]
và
High[n] > High[n-2]

Sau đó phải có:
- nến xác nhận giảm;
- lực mua suy yếu;
- Force phù hợp;
- MACD suy yếu;
- RSI suy yếu.

Không dùng nến đang hình thành để xác nhận.

## 26. Entry Zone Score

Công thức:
- 25% Cấu trúc giá
- 20% Price Action
- 20% Force Score
- 15% MACD
- 10% RSI
- 10% Volume

Ngưỡng:
- >=80 → điểm vào tốt.
- 70–79 → chờ thêm.
- <70 → không vào.

Similarity và Entry Score là hai cổng độc lập.

## 27. Entry Order

LIMIT là ưu tiên.

MARKET chỉ là ngoại lệ khi:
- Entry Score >=80;
- điểm vào xác nhận mạnh;
- giá rời vùng Entry quá nhanh;
- Risk Engine PASS.

Cấm đuổi giá.

Thời gian chờ Entry tối đa:
- 2 giờ kể từ lúc BOT THƯ KÝ xác nhận.

Hết 2 giờ chưa có Entry hợp lệ:
→ hủy kịch bản.

## 28. Futures

- 5 USDT = MARGIN/lệnh.
- Leverage tối đa = x20.
- Risk Engine được dùng leverage thấp hơn.
- Không tăng leverage để gỡ lỗ.

Tối đa:
- 2 vị thế.

Một coin không được LONG và SHORT đồng thời.

## 29. Force Score

Công thức:
- MACD Momentum 30%
- RSI Momentum 20%
- Volume Force 15%
- OI Force 15%
- Price Action 10%
- Multi-Timeframe Confirmation 10%

Phân loại:
- >=70 = CÒN LỰC.
- 50–69 = SUY YẾU.
- <50 = MẤT LỰC.

Theo dõi:
- FORCE_NOW
- FORCE_PREVIOUS
- FORCE_CHANGE

Giảm khoảng 15 điểm trong vài chu kỳ:
→ MẤT LỰC NHANH.

Tính riêng:
- LONG_FORCE
- SHORT_FORCE

Nếu hai bên gần nhau:
→ ĐỨNG XEM.

## 30. Quản lý khi PnL âm

Khi PnL bắt đầu âm:
→ BOT CHÍNH quét realtime.

Kiểm tra:
- Force
- Force Change
- MACD 5m/15m
- RSI6/RSI12 5m/15m
- Volume
- OI
- Price Action

Nếu lực ngược vị thế mạnh và tăng, cấu trúc bị phá, MACD/RSI xác nhận:
→ CẮT NGAY.

Nếu chỉ giật/rút râu và chưa xác nhận đảo chiều:
→ GIỮ.

## 31. Chốt lời / trailing

- +0.10 USDT → bắt đầu trailing.
- +0.50 USDT → chốt 50%.
- Sau đó nếu PnL giảm 0.10 USDT từ đỉnh gần nhất → đóng phần còn lại.

## 32. SL động và SL bảo hiểm

SL động:
- do BOT CHÍNH quản lý theo Force/MACD/RSI/Volume/OI/Price Action.
- có thể cắt sớm.

SL bảo hiểm:
- đặt trực tiếp trên Binance ngay sau khi vị thế khớp.
- khoảng -3 USDT/lệnh.
- độc lập với bot, dùng để bảo vệ khi VPS/mạng/API/WebSocket gặp sự cố.

Không đợi tới -3 mới cắt nếu thị trường đã xác nhận đi ngược.

Nếu không đặt được SL bảo hiểm:
→ vị thế không được coi là an toàn.
→ xử lý khẩn cấp + Telegram.

## 33. Daily Loss Limit

- -5 USDT/ngày.

Khi PnL ròng ngày <= -5:
→ dừng mở lệnh mới.

Vẫn quản lý vị thế đang mở.

PnL ròng gồm:
- Trading PnL
- Fees
- Funding
- Slippage

Reset:
00:00 Asia/Ho_Chi_Minh.

## 34. Chuỗi thua

- 3 lệnh thua liên tiếp → tạm dừng mở lệnh mới.
- Không martingale.
- Không tăng leverage để gỡ.

## 35. Tự cải tiến 200 BOT

Điểm BOT 0–100:
- Win Rate 20%
- Profit Factor 20%
- PnL 15%
- Drawdown 15%
- Số mẫu xác minh 10%
- Hiệu suất gần đây 10%
- Hiệu suất theo Market Regime 10%

BOT yếu:
→ giảm trọng số
→ tối ưu thông số
→ backtest
→ walk-forward
→ out-of-sample
→ paper trade

Nếu vẫn yếu:
→ đào thải.

Lịch sử BOT không bị xóa.

Slot được dùng để tạo BOT mới.

Luôn duy trì 200 BOT hoạt động.

## 36. Ranking BOT

Ranking không được vượt điều kiện cứng:
- Similarity 5m >=90%
- Similarity 15m >=90%
- >=100 mẫu tương đồng
- WIN có ưu thế
- Entry Score >=80
- Risk PASS

## 37. Execution Engine

BOT THƯ KÝ
→ LONG/SHORT
→ BOT CHÍNH tìm Entry
→ Entry Score >=80
→ Risk PASS
→ LIMIT ưu tiên
→ xác nhận khớp
→ đặt SL bảo hiểm
→ quản lý vị thế.

Không retry mù.

## 38. Chống double order / partial fill

Nếu timeout:
- kiểm tra trạng thái Binance trước khi retry.

Phải xử lý:
- NEW
- PARTIALLY FILLED
- FILLED
- CANCELED
- REJECTED

Khối lượng quản lý dựa trên khối lượng thực tế đã khớp.

## 39. Binance ↔ Database

Binance là nguồn sự thật cuối cùng.

Nếu Database khác Binance:
- ưu tiên Binance;
- đồng bộ Database;
- dừng mở lệnh mới nếu còn bất thường.

## 40. Restart / Reconnect

Sau:
- VPS restart
- Bot restart
- WebSocket reconnect
- API reconnect

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

Chỉ trạng thái SẴN SÀNG GIAO DỊCH mới được mở lệnh.

## 41. Quản lý 2 vị thế

Mỗi vị thế quản lý độc lập:
- Entry
- SL
- SL bảo hiểm
- TP
- Trailing
- Force
- PnL
- Similarity
- Entry Score

Nếu cả hai có vấn đề:
→ xử lý độc lập.

## 42. Quét vị thế realtime

Nếu có WebSocket:
- không dùng polling cố định làm cơ chế chính.

Mỗi dữ liệu mới về:
- giá
- PnL
- Force
- MACD
- RSI
- Volume
- OI

→ cập nhật quản lý ngay.

## 43. Database / VPS

- RAM 2 GB.
- SSD 65 GB.
- Kho mẫu tối đa 15 GB.
- Không preload toàn bộ kho mẫu vào RAM.
- SQLite phiên bản đầu.

## 44. Telegram

Toàn bộ nội dung tiếng Việt.

Có:
- Bảng ghim
- Nhật ký
- Xác nhận hướng
- Điểm vào
- Mở lệnh
- Đóng lệnh
- Chốt lời
- Cắt lỗ
- Cảnh báo
- Báo cáo ngày

Không spam 200 BOT.

## 45. Bảng ghim

Một message ghim duy nhất, cập nhật bằng EDIT.

Hiển thị tối thiểu:
- Trạng thái
- Giờ Việt Nam
- Số dư
- PnL ngày
- 200 BOT
- BOT tốt nhất
- Tín hiệu hiện tại
- Similarity 5m/15m
- Mẫu WIN/LOSS
- Vị thế
- Force
- Risk
- Kết quả hôm nay

## 46. Telegram phân biệt tín hiệu và lệnh

Khi thư ký báo:
LONG/SHORT

Phải ghi:
CHƯA VÀO LỆNH — BOT CHÍNH ĐANG TÌM ĐIỂM VÀO.

Khi Entry đạt:
ĐIỂM VÀO ĐƯỢC XÁC NHẬN.

Khi Binance khớp:
MỞ LỆNH FUTURES.

## 47. Báo cáo hàng ngày

- 21:00 giờ Việt Nam.
- Asia/Ho_Chi_Minh.
- Phạm vi 00:00:00 → 20:59:59.

Phải có:
- Tổng giao dịch
- LONG/SHORT
- WIN/LOSS
- Win Rate
- Gross Profit
- Gross Loss
- Fees
- Funding
- Slippage
- Net PnL
- Top BOT
- Top Coin
- Similarity
- Mẫu WIN/LOSS
- Daily Risk
- Drawdown
- Chuỗi thua
- Vị thế mở
- Chi tiết giao dịch
- Tình trạng hệ thống

## 48. .env

BINANCE_API_KEY=
BINANCE_API_SECRET=

TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
TELEGRAM_ADMIN_CHAT_ID=

TIMEZONE=Asia/Ho_Chi_Minh

DAILY_REPORT_HOUR=21
DAILY_REPORT_MINUTE=0

Không hard-code secret.
Không commit .env.

## 49. Chế độ chạy

Phải có:
- BACKTEST
- PAPER
- LIVE

Mặc định:
PAPER

Không tự bật LIVE.

## 50. Test bắt buộc

Phải có:
- Unit Test
- Integration Test
- Backtest
- Walk-forward
- Out-of-sample
- Paper Trading
- Risk Test
- Execution Test
- Restart Test
- Reconnect Test
- Database Test
- Telegram Test
- Timezone Test

### Test Similarity
- 89.9% → FAIL
- 90.0% → PASS
- cả 5m và 15m đều phải đạt.

### Test Entry
Similarity 95% + Entry Score 79:
→ KHÔNG VÀO.

Similarity 95% + Entry Score 80:
→ được xét Risk.

## 51. Quy tắc bảo toàn SPEC

Nếu code hiện tại khác SPEC:
→ SPEC ưu tiên cao hơn code.

Nếu sửa bug:
- được sửa bug kỹ thuật;
- không được biến bug fix thành thay đổi nghiệp vụ.

Nếu muốn refactor:
- không được refactor lớn ngoài phạm vi nếu không cần.

Nếu có mâu thuẫn hoặc yêu cầu chưa rõ:
→ DỪNG.
→ Nêu mâu thuẫn.
→ Không tự quyết định thay đổi nghiệp vụ.

## 52. Luồng cuối cùng

TOP 30 BINANCE FUTURES
→ 5m/15m/1h
→ 200 BOT CON
→ thử kịch bản
→ paper trade
→ bộ xác minh
→ WIN/LOSS/HÒA/LOẠI
→ kho 500k WIN + 500k LOSS
→ kho bổ sung tối đa 15GB
→ BOT THƯ KÝ
→ thị trường hiện tại
→ 5m/15m/1h
→ MACD/RSI/Volume
→ Similarity 3 lớp
→ 5m >=90%
→ 15m >=90%
→ >=100 mẫu
→ so WIN với LOSS
→ LONG/SHORT/ĐỨNG XEM
→ BOT CHÍNH
→ 15m tìm vùng
→ 5m tìm Swing Low/High
→ Entry Score >=80
→ Risk
→ LIMIT ưu tiên
→ khớp
→ SL bảo hiểm trên Binance
→ quản lý realtime
→ chốt lời / trailing / cắt sớm
→ kết quả
→ cập nhật kho mẫu

# KẾT LUẬN NGHIỆP VỤ

200 BOT CON:
Tìm và thử nghiệm các kịch bản thực tế.

KHO MẪU:
Lưu cả những trường hợp thắng và thua đã được xác minh.

BOT THƯ KÝ:
So sánh thị trường hiện tại với lịch sử và xác nhận hướng.

BOT CHÍNH:
Tìm điểm vào tốt nhất, quản lý rủi ro và thực thi.

BOT THƯ KÝ báo LONG/SHORT không đồng nghĩa với vào lệnh ngay.

Mọi lệnh thật phải qua:
BOT THƯ KÝ
→ Similarity
→ >=100 mẫu
→ WIN/LOSS comparison
→ Entry
→ Entry Score
→ Risk
→ Execution
→ SL bảo hiểm.

---

# PHẦN B — LUẬT BẤT BIẾN CHO CODEX

Các quy tắc dưới đây là luật nghiệp vụ cứng. Codex phải đọc phần này trước mọi thay đổi và tuyệt đối không tự ý bỏ, đổi, giảm hoặc đơn giản hóa.

## B1. Kiến trúc
1. Phải có 200 BOT CON.
2. BOT CON chỉ backtest/paper trade, không giao dịch tiền thật.
3. BOT THƯ KÝ là tầng trung gian duy nhất giữa 200 BOT CON và BOT CHÍNH.
4. BOT THƯ KÝ không đặt lệnh thật.
5. BOT CHÍNH là thành phần duy nhất được phép đặt lệnh Futures thật.

## B2. Timeframe
6. Chuẩn dữ liệu chung là 5m + 15m + 1h.
7. 5m và 15m là hai khung xác nhận chính.
8. 1h chỉ là bối cảnh và không được thay thế 5m/15m.
9. 5m Similarity phải >= 90%.
10. 15m Similarity phải >= 90%.
11. Một trong hai khung dưới 90% thì ĐỨNG XEM.

## B3. Kho mẫu
12. Kho lõi gồm 500.000 WIN + 500.000 LOSS.
13. Được lưu thêm mẫu lịch sử nhưng tổng kho mẫu tối đa 15 GB SSD.
14. Không nạp toàn bộ kho mẫu vào RAM.
15. Phải có ít nhất 100 mẫu tương đồng hợp lệ trước khi xét lệnh.
16. Phải lưu cả WIN và LOSS.
17. Không xóa LOSS hợp lệ chỉ để làm đẹp thống kê.
18. Chỉ gộp mẫu khi giống 100%.
19. Dữ liệu lỗi/thiếu/không xác minh được không được vào kho WIN/LOSS chất lượng.
20. Cấm look-ahead bias.
21. Dữ liệu sau thời điểm T chỉ dùng để xác minh kết quả, không dùng tạo tín hiệu tại T.

## B4. Similarity và hướng chỉ báo
22. Similarity phải tính bằng code, không cảm tính.
23. Similarity gồm 30% so giá trị + 30% so hướng + 40% so độ dốc/cấu trúc.
24. Mẫu giống phải giống giá trị, trạng thái, hướng, độ dốc, tốc độ thay đổi, quan hệ giữa các đường và cấu trúc diễn biến.
25. Hướng ngược phải bị phạt mạnh hoặc loại.
26. MACD tăng = DIF hướng lên hoặc đi từ âm -> 0 -> dương.
27. MACD giảm = DIF hướng xuống hoặc đi từ dương -> 0 -> âm.
28. RSI dùng RSI6 + RSI12.
29. RSI tăng = RSI6 và RSI12 cùng hướng lên.
30. RSI giảm = RSI6 và RSI12 cùng hướng xuống.
31. RSI6/RSI12 ngược hướng = XUNG ĐỘT.
32. Similarity_LOSS >= Similarity_WIN = ĐỨNG XEM.
33. LOSS chiếm ưu thế rõ rệt trong tập mẫu tương đồng = ĐỨNG XEM.

## B5. Entry
34. BOT THƯ KÝ báo LONG/SHORT không có nghĩa là vào ngay.
35. BOT CHÍNH bắt buộc tìm điểm vào.
36. 15m xác định vùng cấu trúc; 5m xác định Swing Low/Swing High và điểm vào.
37. Không bắt đáy/đỉnh mù quáng.
38. Không dùng nến đang hình thành để xác nhận Swing.
39. Swing Low: Low[n] < Low[n-1] và Low[n] < Low[n-2], sau đó cần nến xác nhận tăng và lực bán suy yếu.
40. Swing High: High[n] > High[n-1] và High[n] > High[n-2], sau đó cần nến xác nhận giảm và lực mua suy yếu.
41. Entry Zone Score = 25% cấu trúc + 20% Price Action + 20% Force + 15% MACD + 10% RSI + 10% Volume.
42. Entry Score >= 80 mới được phép vào.
43. 70–79 = chờ; <70 = không vào.
44. LIMIT là phương thức ưu tiên.
45. MARKET chỉ là ngoại lệ khi Entry đã xác nhận mạnh, giá rời vùng nhanh và Risk PASS.
46. Cấm đuổi giá.
47. Chờ Entry tối đa 2 giờ; hết thời gian thì hủy kịch bản.

## B6. Futures và Risk
48. 5 USDT = margin/lệnh.
49. Leverage tối đa x20; có thể dùng thấp hơn.
50. Tối đa 2 vị thế.
51. Một coin không được LONG và SHORT đồng thời.
52. +0,10 USDT = bắt đầu trailing.
53. +0,50 USDT = chốt 50%.
54. Giảm 0,10 USDT từ đỉnh PnL = đóng phần còn lại.
55. Force Score = MACD 30% + RSI 20% + Volume 15% + OI 15% + Price Action 10% + MTF 10%.
56. Force >=70 = CÒN LỰC; 50–69 = SUY YẾU; <50 = MẤT LỰC.
57. Force giảm khoảng 15 điểm trong vài chu kỳ = MẤT LỰC NHANH.
58. Khi PnL bắt đầu âm phải quét realtime; lực ngược mạnh = cắt sớm; chỉ giật/rút râu = không cắt nếu chưa xác nhận đảo chiều.
59. SL bảo hiểm trực tiếp trên Binance khoảng -3 USDT/lệnh, đặt ngay sau khi khớp.
60. SL bảo hiểm là tuyến cuối cho lỗi VPS/mạng/API/WebSocket; không cần chờ tới -3 nếu bot phát hiện mất lực.
61. Daily Loss Limit = -5 USDT/ngày.
62. Đạt Daily Loss Limit = dừng mở lệnh mới nhưng vẫn quản lý vị thế đang mở.
63. 3 lệnh thua liên tiếp = tạm dừng mở lệnh mới.
64. Cấm Martingale và DCA Futures vô hạn.

## B7. Tự cải tiến 200 BOT
65. Có thể tối ưu, đào thải và thay thế BOT nhưng đội hình hoạt động luôn là 200 BOT.
66. BOT yếu: giảm trọng số -> tối ưu -> backtest -> walk-forward -> out-of-sample -> paper trade.
67. BOT bị đào thải không xóa lịch sử.
68. BOT mới phải vượt kiểm thử trước khi vào đội hình.
69. Ranking BOT không được vượt các cổng cứng Similarity, 100 mẫu, WIN/LOSS, Entry Score và Risk.

## B8. Execution và Recovery
70. Không retry lệnh mù.
71. Timeout phải kiểm tra trạng thái thật trên Binance trước khi retry.
72. Phải xử lý NEW / PARTIALLY FILLED / FILLED / CANCELED / REJECTED.
73. Binance là nguồn sự thật cuối cùng.
74. Sau restart/reconnect phải đối chiếu Binance ↔ Database ↔ trạng thái bot.
75. Khi đang đồng bộ không được mở lệnh mới.
76. Phải xác nhận SL bảo hiểm tồn tại sau reconnect.
77. Mỗi vị thế được quản lý độc lập.

## B9. Telegram và vận hành
78. Telegram toàn bộ bằng tiếng Việt.
79. Có bảng ghim, nhật ký, tín hiệu, entry, mở/đóng lệnh, chốt lời, cắt lỗ, cảnh báo.
80. Bảng ghim chỉ là một message được cập nhật.
81. Báo cáo hàng ngày lúc 21:00 Asia/Ho_Chi_Minh.
82. Báo cáo ngày 00:00:00–20:59:59 giờ Việt Nam.
83. Không spam 200 BOT.

## B10. Bảo mật và quy tắc Codex
84. API key/Telegram token chỉ trong .env, không hard-code.
85. Chế độ mặc định là PAPER, không tự bật LIVE.
86. Không tự ý bỏ, đổi, giảm hoặc đơn giản hóa bất kỳ luật bất biến nào.
87. Nếu có mâu thuẫn hoặc yêu cầu chưa rõ: DỪNG, nêu mâu thuẫn, không tự quyết định thay đổi nghiệp vụ.
88. Sau thay đổi phải chạy test và kiểm tra git diff.
89. Không được tuyên bố hoàn thành nếu chưa có bằng chứng test.

---

# PHẦN C — CẤU TRÚC PROJECT TỐI THIỂU

```text
futures-bot/
├── MASTER_SPEC.md
├── README.md
├── requirements.txt
├── .env
├── .env.example
├── bot_main/
├── agents_200/
├── database/
└── telegram/
```

`MASTER_SPEC.md` là tài liệu nguồn duy nhất của đặc tả và luật bất biến.

---

# PHẦN D — CHỈ THỊ CHO CODEX

Trước khi viết hoặc sửa code:

1. Đọc toàn bộ `MASTER_SPEC.md`.
2. Xác định phạm vi nhiệm vụ.
3. Không tự ý thay đổi nghiệp vụ.
4. Viết test trước hoặc cùng với phần code liên quan.
5. Chạy test sau thay đổi.
6. Kiểm tra `git diff`.
7. Báo rõ phần đã làm, phần đã test, PASS/FAIL và phần còn thiếu.

Nếu một yêu cầu mới xung đột với luật trong tài liệu:
→ DỪNG và báo mâu thuẫn.
→ Không tự ý sửa luật để làm cho code chạy được.

# 05 — Behavioral Analytics

> Mục tiêu: trả lời câu hỏi **"người dùng đang làm gì và rơi rụng ở đâu?"** trước khi quyết
> định gửi tin nhắn gì.

## 5.1 Bộ công cụ phân tích

| Công cụ | Trả lời câu hỏi | Dạng hiển thị |
|---|---|---|
| **Boards** | Các chỉ số quan trọng hôm nay thế nào? | Bảng card tự dựng |
| **Event analysis** | Event này xảy ra bao nhiêu lần, bởi bao nhiêu người? | Số liệu + biểu đồ |
| **Trends** | Chỉ số này tăng hay giảm theo thời gian? | Biểu đồ đường |
| **Funnels** | Người dùng rơi rụng ở bước nào? | Phễu |
| **Cohorts** | Người dùng có quay lại không? | Ma trận retention |
| **Pivots** | Cắt dữ liệu theo nhiều chiều cùng lúc | Bảng chéo |
| **Flows** | Người dùng thực tế đi đường nào? | Sơ đồ luồng |
| **Real Impact** | Campaign có thật sự tạo khác biệt không? | So sánh với control group |
| **Anomaly Detection** | Có gì bất thường không? | Cảnh báo |

---

## 5.2 Funnels — công cụ dùng nhiều nhất

### Funnel đo gì

Chuỗi event **theo thứ tự**, cho thấy bao nhiêu người đi tới từng bước và rơi rụng ở đâu.

```
App Launched          ████████████████████  10.000  (100%)
                                 ↓ -58%
Product Viewed        ████████              4.200   (42%)
                                 ↓ -64%
Added to Cart         ███                   1.500   (15%)   ← điểm rơi lớn nhất
                                 ↓ -40%
Checkout Started      ██                      900   (9%)
                                 ↓ -33%
Charged               █                       600   (6%)
```

Đọc funnel này: điểm nghẽn nằm ở **Product Viewed → Added to Cart** (mất 64%).
Đó là chỗ đáng ưu tiên xử lý, không phải bước cuối.

### Cách tạo funnel

```
1. Analytics  →  Funnels
2. Chọn các event theo đúng thứ tự mong muốn  (tối đa 8 bước)
   — có thể kéo thả để sắp xếp lại
3. (tùy chọn) Thêm event property filter cho từng bước
   → chỉ đếm user thực hiện event đó với property đó
4. Đặt conversion window  (mặc định 5 ngày)
   ⚠ Nên để NGẮN HƠN khoảng thời gian (date range) đã chọn, để giảm sai lệch dữ liệu
5. Bấm  View Funnel
```

### Sau khi funnel hiện ra, có thể

| Thao tác | Dùng để |
|---|---|
| **Compare against another segment** | So sánh user iOS vs Android, mới vs cũ |
| **Break down by property** | Tách theo thành phố, nguồn cài đặt, gói dịch vụ |
| **Hold a property constant** | Chỉ tính user giữ nguyên một property xuyên suốt |
| **Enforce strict ordering** | Bắt buộc đúng thứ tự, không cho event khác xen vào |

### Hiểu đúng "conversion window"

Khoảng thời gian cho phép user hoàn thành **toàn bộ** chuỗi bước.

```
Conversion window = 1 ngày    →  "mua trong ngày" — khắt khe, tỉ lệ thấp
Conversion window = 30 ngày   →  "có mua là được" — rộng rãi, tỉ lệ cao
```

Không có con số đúng tuyệt đối — chọn theo chu kỳ mua tự nhiên của sản phẩm.
Đồ ăn: vài giờ. Vé máy bay: vài tuần. Bất động sản: vài tháng.

---

## 5.3 Cohorts — đo giữ chân

Cohort nhóm người dùng theo **thời điểm** họ làm một hành động, rồi theo dõi họ có quay lại
làm hành động khác hay không.

```
Cohort: user App Installed tuần nào → % quay lại App Launched các tuần sau

Tuần cài đặt  Size    W0     W1     W2     W3     W4
─────────────────────────────────────────────────────
Tuần 1        2.400   100%   32%    18%    12%     9%
Tuần 2        2.900   100%   35%    21%    14%    11%
Tuần 3        3.100   100%   41%    26%    19%     —
Tuần 4        2.700   100%   38%    24%     —      —
```

**Cách đọc:**
- Cột dọc: so sánh các cohort với nhau → chất lượng user có cải thiện không?
  (Tuần 3 có W1 = 41%, tốt hơn tuần 1 là 32% → thay đổi gần đây có tác dụng)
- Hàng ngang: đường cong giữ chân → rơi mạnh nhất luôn ở W0→W1

**Nguyên tắc:** nếu đường cong không **phẳng lại** (flatten) sau vài kỳ, sản phẩm chưa có
product-market fit — gửi push nhiều cũng không cứu được.

---

## 5.4 Trends

Theo dõi một event theo thời gian. Dùng để:
- Phát hiện mùa vụ (cuối tuần, lễ tết)
- Phát hiện sự cố (event tụt về 0 → có thể SDK lỗi, không phải người dùng bỏ đi)
- Đo tác động của một thay đổi (release mới, campaign lớn)

> 💡 Mẹo gỡ lỗi: event tụt đột ngột về 0 gần như **luôn** là lỗi tracking, không phải
> lỗi hành vi người dùng. Kiểm tra bản release gần nhất trước khi hoảng.

---

## 5.5 Flows

Khác funnel ở chỗ: funnel là đường đi **bạn giả định**, flows là đường đi **người dùng thật sự đi**.

```
Funnel  →  "tôi nghĩ họ đi A → B → C"
Flows   →  "thực tế 40% đi A → D → thoát"
```

Dùng Flows khi funnel cho kết quả khó hiểu — thường sẽ phát hiện ra người dùng đang đi một
đường hoàn toàn khác.

---

## 5.6 Pivots

Bảng chéo — cắt một chỉ số theo nhiều chiều cùng lúc.

```
Số lượt Charged, theo Thành phố × Nền tảng

              iOS     Android   Web     Tổng
Hà Nội        1.240     980      310    2.530
TP.HCM        1.890   1.420      450    3.760
Đà Nẵng         320     410       90      820
Tổng          3.450   2.810      850    7.110
```

Phát hiện kiểu: *"Đà Nẵng là nơi duy nhất Android vượt iOS"* → có thể do phân khúc giá.

---

## 5.7 Boards

Dashboard tự dựng, gồm các card hiển thị chỉ số quan trọng. Dùng để không phải vào từng
mục mỗi sáng.

**Board nên có cho một app thương mại điện tử:**

| Card | Chỉ số |
|---|---|
| 1 | DAU / MAU |
| 2 | Lượt cài đặt mới hôm nay |
| 3 | Funnel mua hàng (tỉ lệ chuyển đổi tổng) |
| 4 | Doanh thu (`Charged` → `Amount`) |
| 5 | Retention D1 / D7 / D30 |
| 6 | Tỉ lệ click push của campaign đang chạy |

---

## 5.8 Real Impact & Control Group

Đây là khái niệm dễ bị bỏ qua nhưng quan trọng nhất về mặt đo lường.

**Vấn đề:** campaign push có 8% người mua hàng. Nhưng trong số đó, bao nhiêu người **dù sao
cũng mua**?

**Giải pháp:** control group — nhóm người dùng bị **loại khỏi mọi campaign marketing**,
nhận trải nghiệm bình thường.

```
Nhóm nhận campaign (90%)   →  8,0% mua hàng
Control group     (10%)    →  6,5% mua hàng
                              ─────────────
Tác động thật của campaign =  1,5 điểm %
```

Không có control group, bạn sẽ tưởng campaign hiệu quả gấp 5 lần thực tế.

> Có thể tạo **nhiều** nhóm treatment và control.
> Ở cấp profile, có thể **Exclude from control groups** tối đa 20 profile (để test).

---

## 5.9 Anomaly Detection

Tự phát hiện bất thường trong số liệu. Ví dụ: `Charged` giảm 40% so với cùng kỳ → cảnh báo.
Hữu ích để biết sự cố **trước khi** sếp hỏi.

---

## 5.10 Thứ tự phân tích nên theo

```
1. Trends     →  có gì bất thường không?
2. Funnels    →  rơi rụng ở bước nào?
3. Pivots     →  rơi rụng ở nhóm nào?
4. Flows      →  họ đi đâu thay vì đi tiếp?
5. Segment    →  khoanh vùng nhóm có vấn đề
6. Campaign   →  tác động vào nhóm đó
7. Real Impact→  đo lại xem có hiệu quả không
```

Đừng bắt đầu từ bước 6.

## 5.11 Checklist

- [ ] Tôi biết conversion window ảnh hưởng thế nào tới số liệu funnel
- [ ] Tôi biết đọc ma trận cohort theo cả hàng và cột
- [ ] Tôi hiểu vì sao cần control group
- [ ] Tôi biết phân biệt Funnels (giả định) và Flows (thực tế)
- [ ] Tôi đã dựng một Board cho công việc hằng ngày

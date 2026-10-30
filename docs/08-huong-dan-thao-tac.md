# 08 — Hướng dẫn thao tác (Labs)

> Sáu bài thực hành đi từ dễ tới khó. Làm theo thứ tự trên **project test/sandbox**, đừng
> làm trên production.
>
> ⚠️ Tên menu và vị trí nút có thể khác đôi chút tùy phiên bản dashboard. Nếu không thấy mục
> nào, dùng ô tìm kiếm trên dashboard hoặc đối chiếu [docs.clevertap.com](https://docs.clevertap.com).

---

## Lab 0 — Chuẩn bị

**Mục tiêu:** có môi trường sạch để thực hành.

1. Đăng nhập dashboard CleverTap, chọn đúng **Project** (ưu tiên project dev/staging).
2. Vào **Find People**, tìm profile của chính bạn (theo email hoặc số điện thoại dùng trong app).
3. Mở profile đó → bấm **Mark as test profile**
   → profile được gắn `ct_is_test_user = Yes` và lọt vào segment hệ thống **Test Users**.
4. (tùy chọn) Bấm **Exclude from control groups** để chắc chắn bạn luôn nhận được mọi campaign
   khi test. *(Giới hạn 20 profile cho toàn tài khoản — dùng tiết kiệm.)*

✅ **Kết quả:** bạn có một profile test để gửi thử mà không ảnh hưởng số liệu thật.

---

## Lab 1 — Đọc một User Profile

**Mục tiêu:** hiểu dữ liệu thật trông như thế nào.

1. **Find People** → mở profile test của bạn.
2. Quan sát 3 khu vực:

| Khu vực | Cần nhìn gì |
|---|---|
| **Identifiers** | Có CleverTap ID chưa? Có Email/Phone/Identity chưa? Profile là Anonymous hay Identified? |
| **User Properties** | Có `Name`, `Email`, `Phone`, `Gender`, `DOB` không? Các cờ `MSG-push`, `MSG-email` đang bật hay tắt? |
| **Events** | Timeline hành vi. Tìm `App Launched`, `Session Concluded`. Bấm vào một event để xem **event properties** |

3. Thêm `?showDebugEvents=true` vào cuối URL trang profile → tải lại.
   Giờ bạn thấy thêm `Identity Set`, `Reachable By`, `Push Unregistered`…

**Câu hỏi tự trả lời:**
- Profile này có nhận được push không? (Nhìn `MSG-push` và `Reachable By`)
- Có event nào bị **làm mờ (grayed out)** không? → đó là event trước khi đăng nhập,
  **không** được tính vào analytics và segmentation.

---

## Lab 2 — Tạo Past Behavior Segment

**Mục tiêu:** tạo segment "người bỏ giỏ hàng".

```
1. Segments  →  Segments
2. Bấm  + Segment
3. Mục "Past behavior segments"  →  chọn  Actions
```

4. Trên trang query, dựng điều kiện:

```
Người dùng ĐÃ thực hiện    : Added to Cart
Trong                       : 7 ngày qua
VÀ KHÔNG thực hiện          : Charged
Trong                       : 7 ngày qua
```

> Nếu dashboard của bạn có mục riêng cho kiểu này, chọn **Inaction** thay vì **Actions**.

5. (tùy chọn) Thêm điều kiện thuộc tính: `City = Hanoi`
6. Bấm **Save segment** → đặt tên theo quy ước: `[PBS] Cart Abandoners 7d`

✅ **Kết quả:** một segment tái sử dụng được cho campaign và phân tích.

**Việc cần làm thêm:**
- Ghi lại **size** của segment. Nếu bằng 0 → event `Added to Cart` chưa được track, hoặc
  tên event bị viết khác.
- Nhớ: thống kê PBS tính lại **hằng ngày**, nhưng khi dùng trong campaign thì danh sách user
  đủ điều kiện được tính **real-time**.

---

## Lab 3 — Tạo Live User Segment

**Mục tiêu:** segment cập nhật tức thì, dùng để trigger.

```
1. Segments  →  Segments
2. Bấm  + Segment
3. Mục "Live user segments"  →  chọn  Inaction within time
4. Chọn các thuộc tính cho segment:
       Đã thực hiện   : Added to Cart
       Nhưng không    : Charged
       Trong vòng     : 60 phút
5. (tùy chọn) Tick "Filter on past user behavior and common properties"
   → thêm bộ lọc hành vi quá khứ hoặc user property
6. Save segment  →  đặt tên: [LIVE] Cart Abandon 60m
```

**So sánh Lab 2 và Lab 3:**

| | Lab 2 (PBS) | Lab 3 (Live) |
|---|---|---|
| Cập nhật | Hằng ngày | Real-time |
| Dùng cho | Phân tích, batch campaign | Trigger campaign tức thì |
| Lọc dashboard Today | ❌ Không | ✅ Có |
| Include/Exclude trong campaign | ✅ Có | ❌ Không |

---

## Lab 4 — Dựng Funnel và tìm điểm nghẽn

**Mục tiêu:** tìm ra bước rơi rụng nhiều nhất.

```
1. Analytics  →  Funnels
2. Chọn event theo đúng thứ tự (tối đa 8 bước):
       App Launched
       Product Viewed
       Added to Cart
       Charged
   (có thể kéo thả để sắp xếp lại)
3. (tùy chọn) Thêm event property filter cho một bước
       ví dụ: Product Viewed  where  Category = Điện tử
4. Conversion window: đặt 2 ngày
   ⚠ Phải NGẮN HƠN date range đang chọn
5. Bấm  View Funnel
```

**Phân tích tiếp:**

| Thao tác | Mục đích |
|---|---|
| **Break down by property** → `CT Source` hoặc `City` | Nhóm nào rơi nhiều nhất? |
| **Compare against another segment** | So iOS vs Android, user mới vs cũ |
| **Enforce strict ordering** | Bắt buộc đúng thứ tự, không cho event khác xen vào |

**Việc cần làm:** đổi conversion window từ 2 ngày → 7 ngày → 30 ngày và ghi lại tỉ lệ chuyển
đổi mỗi lần. Hiểu rõ con số "conversion rate" vô nghĩa nếu không kèm conversion window.

---

## Lab 5 — Tạo Campaign đầu tiên (gửi cho chính mình)

**Mục tiêu:** chạy trọn vẹn vòng đời một campaign mà không làm phiền ai.

```
1. Bấm  + Campaigns
2. Chọn kênh:  Push
3. WHO  — chọn segment:  Test Users
          (CHỈ Test Users — đây là bài tập)
4. WHAT — soạn nội dung:
          Tiêu đề: Chào {{Name | default: "bạn"}}!
          Nội dung: Đây là campaign test đầu tiên của tôi.
          Deep link: để trống hoặc trỏ tới màn hình chính
5. WHEN — delivery type:  One Time  →  gửi ngay
6. Loại campaign:  Single Message
7. Labels: thêm nhãn  test
8. Xem lại toàn bộ  →  Publish
```

> ⚠️ **Nhớ: campaign đã publish chỉ có thể STOP, không thể PAUSE.**

**Sau khi gửi:**
1. Kiểm tra điện thoại có nhận được push không.
2. Vào **Campaigns** → mở campaign vừa gửi → xem báo cáo: Sent / Delivered / Viewed / Clicked.
3. Quay lại **Find People** → mở profile test → thấy các event `Notification Sent`,
   `Notification Viewed`, `Notification Clicked` trên timeline.

**Nếu không nhận được push, kiểm tra theo thứ tự:**

| Kiểm tra | Cách xem |
|---|---|
| `MSG-push` có bật không | Trên trang profile |
| Profile có `Reachable By` push không | Bật `?showDebugEvents=true` |
| Có `Push Unregistered` không | Debug events |
| Cấu hình FCM/APNS đúng chưa | Settings → Channels → Push |
| Thiết bị có cho phép thông báo không | Cài đặt hệ điều hành |

---

## Lab 6 — Dựng Journey bỏ giỏ hàng

**Mục tiêu:** kịch bản tự động hóa hoàn chỉnh.

### Bước 1 — Vẽ ra giấy trước

```
Entry: Added to Cart
  → chờ 1 giờ
  → đã Charged? ── Rồi → Thoát
                  └ Chưa → Push nhắc giỏ hàng
  → chờ 24 giờ
  → đã Charged? ── Rồi → Thoát
                  └ Chưa → Email kèm sản phẩm trong giỏ
  → chờ 48 giờ
  → đã Charged? ── Rồi → Thoát
                  └ Chưa → Email mã giảm 10%
Goal: Charged
```

> Vẽ trước vì **thay đổi cấu trúc journey đang live bắt buộc phải tạo bản draft mới** —
> chỉ nội dung tin nhắn (phần WHAT) mới sửa trực tiếp được.

### Bước 2 — Dựng trên canvas

```
1. Journeys  →  + Journey
2. Entry criteria:  user thực hiện  Added to Cart
3. Thêm node  Wait  →  1 giờ
4. Thêm node  Conditional Split  →  điều kiện: đã thực hiện Charged?
       Nhánh CÓ    →  node Exit
       Nhánh KHÔNG →  đi tiếp
5. Thêm node  Message (Push)  →  soạn nội dung nhắc giỏ hàng
6. Lặp lại bước 3–5 cho mốc 24 giờ (Email) và 48 giờ (Email + mã giảm)
7. Đặt  Goal:  Charged
8. Giới hạn đối tượng:  chỉ  Test Users   ← quan trọng khi tập
9. Lưu nháp  →  kiểm thử  →  Publish
```

### Bước 3 — Kiểm tra

- Dùng profile test, thực hiện `Added to Cart` trong app → xem có vào journey không.
- Theo dõi **Journey Performance**: entry count, drop-off từng node, goal conversion.

---

## Bài tập mở rộng

| # | Bài | Kỹ năng rèn |
|---|---|---|
| 1 | Dựng một **Board** gồm 6 card: DAU, cài đặt mới, funnel mua hàng, doanh thu, retention D7, CTR campaign | Boards |
| 2 | Chạy **Cohort** cho `App Installed` → `App Launched` theo tuần, đọc ma trận | Cohorts |
| 3 | Tạo **A/B test** 2 tiêu đề push, chỉ đổi **một** biến, chạy đủ 7 ngày | A/B testing |
| 4 | Bật **control group** 10% và so sánh Real Impact | Đo lường |
| 5 | Dùng **Pivots** cắt `Charged` theo Thành phố × Nền tảng | Pivots |
| 6 | Dùng **Flows** xem người dùng thật sự đi đâu sau `Product Viewed` | Flows |
| 7 | Chạy **RFM Analysis**, chọn một nhóm (At Risk) và thiết kế journey win-back cho nhóm đó | RFM + Journeys |
| 8 | Thiết lập **frequency cap** cho toàn project | Vận hành |

---

## Sổ tay gỡ lỗi nhanh

| Triệu chứng | Nguyên nhân thường gặp |
|---|---|
| Segment size = 0 | Event chưa được track, hoặc tên event viết khác (phân biệt hoa thường) |
| Event không hiện trên profile | SDK chưa tích hợp đúng, hoặc event gửi trước `onUserLogin` nên nằm ở profile anonymous |
| Event bị làm mờ (grayed out) | Event trước khi đăng nhập — bị loại khỏi analytics/segmentation |
| Số export khác size segment | Blacklisted users vẫn được đếm trong size segment |
| Không lọc được dashboard Today bằng segment | Today chỉ nhận **live user segment** |
| Không dùng được include/exclude | Chỉ **PBS** hỗ trợ; không khả dụng cho bulletins và A/B testing |
| Không pause được campaign | Đúng vậy — publish rồi chỉ **stop** được |
| Không sửa được entry criteria của journey | Thay đổi cấu trúc phải tạo **draft version mới** |
| Push không tới | Xem bảng kiểm tra ở Lab 5 |
| Số liệu funnel kỳ lạ | Conversion window quá ngắn, hoặc chưa bật strict ordering |
| Event đột ngột về 0 trong Trends | Gần như luôn là lỗi tracking sau một bản release, không phải người dùng bỏ đi |

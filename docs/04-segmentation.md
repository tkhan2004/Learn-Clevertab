# 04 — Segmentation

> Segment là **cầu nối** giữa phân tích và hành động. Toàn bộ dashboard CleverTap có thể lọc
> theo segment bạn tạo ra.

## 4.1 Segment là gì

Nhóm người dùng khớp tiêu chí dựa trên:
- Hành động **đã làm** hoặc **không làm**
- Thuộc tính profile
- Trong một **khoảng thời gian** xác định

Ví dụ: *"Người dùng đã `Added to Cart` nhưng chưa `Charged` trong 7 ngày qua, ở Hà Nội"*

## 4.2 Ba loại segment

```
                    SEGMENTS
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
┌───────────────┐ ┌─────────────┐ ┌──────────────┐
│ PAST BEHAVIOR │ │ LIVE  USER  │ │ CUSTOM  LIST │
│     (PBS)     │ │             │ │              │
├───────────────┤ ├─────────────┤ ├──────────────┤
│ Hành vi quá   │ │ Thêm user   │ │ Danh sách    │
│ khứ           │ │ REAL-TIME   │ │ upload từ    │
│ Thống kê tính │ │ ngay khi    │ │ nguồn ngoài  │
│ lại HẰNG NGÀY │ │ khớp đkiện  │ │ (CRM, CSV)   │
└───────────────┘ └─────────────┘ └──────────────┘
      │                  │                │
      ▼                  ▼                ▼
 Phân tích +        Trigger campaign   Chiến dịch
 batch campaign     tức thì            đặc thù
```

### 1️⃣ Past Behavior Segment (PBS)

Nhóm theo hành động trong quá khứ. Có 3 kiểu điều kiện:

| Kiểu | Ý nghĩa | Ví dụ |
|---|---|---|
| **Actions** | Đã thực hiện một event | Users đã `Added to Cart` trong 30 ngày qua |
| **Inaction** | Đã làm A nhưng **không** làm B trong khoảng thời gian | Đã `Added to Cart` nhưng chưa `Charged` trong 7 ngày |
| **Actions with user properties** | Kết hợp hành vi + thuộc tính profile | Đã `Charged` và `City = Hanoi` |

### 2️⃣ Live User Segment

Người dùng được thêm vào **ngay lập tức** khi hành vi khớp. Các tùy chọn:

| Tùy chọn | Dùng khi |
|---|---|
| **Single action** | Ngay khi user thực hiện một event |
| **Inaction within time** | User làm A nhưng không làm B trong X phút/giờ |
| **On a date/time** | Tại một mốc thời gian cụ thể |
| **Page visit** | Truy cập một trang web cụ thể |
| **Referrer entry** | Đến từ một nguồn giới thiệu |
| **Page count** | Xem đủ N trang |

> Đây là loại dùng cho các kịch bản **thời gian thực**: bỏ giỏ hàng, chào mừng người dùng mới,
> nhắc hoàn tất đăng ký.

### 3️⃣ Custom List Segment

Danh sách người dùng từ nguồn bất kỳ — công cụ bên thứ ba, database nội bộ, file CSV.
Dùng khi tiêu chí không thể diễn đạt bằng hành vi trong CleverTap (ví dụ: danh sách khách VIP
do sale chọn tay).

### Segment hệ thống có sẵn

Mọi tài khoản đều có:
- **All Users**
- **Test Users** — các profile được đánh dấu `ct_is_test_user = Yes`

---

## 4.3 Cách tạo segment

### Past Behavior Segment

```
1. Segments  →  Segments
2. Bấm  + Segment
3. Mục "Past behavior segments"  →  chọn  Actions
4. Định nghĩa điều kiện trên trang query
5. Save segment  →  đặt tên
```

### Live User Segment (ví dụ "Inaction within time")

```
1. Segments  →  Segments
2. Bấm  + Segment
3. Mục "Live user segments"  →  chọn  Inaction within time
4. Chọn các thuộc tính cho segment
   (tùy chọn) tick "Filter on past user behavior and common properties"
   để thêm bộ lọc hành vi quá khứ / thuộc tính người dùng
5. Save segment  →  đặt tên
```

---

## 4.4 ⚠️ Giới hạn & bẫy cần nhớ

Phần này là thứ dễ làm bạn "ngớ người" khi dùng thật:

| Vấn đề | Chi tiết |
|---|---|
| **Lọc dashboard** | View thời gian thực (ví dụ dashboard **Today**) **chỉ** lọc được bằng **live user segment**. Phân tích hành vi quá khứ **chỉ** lọc được bằng **past behavior segment**. Không trộn được. |
| **Include / Exclude** | Chỉ **PBS** mới dùng được để include/exclude. Không khả dụng cho **bulletins** và **A/B testing**. |
| **Dùng cả include lẫn exclude** | Hai điều kiện nối với nhau bằng **AND** |
| **Thời gian xử lý** | Thống kê PBS tính **mỗi ngày**. Nhưng khi dùng trong campaign, danh sách user đủ điều kiện được tính **real-time**. |
| **Số liệu export lệch** | Số user trong CSV export có thể **khác** số hiển thị của segment, vì **blacklisted users vẫn được đếm** trong kích thước segment |
| **Lọc theo trung bình của property** | Event thiếu property đó bị tính là **0**, trừ khi bạn lọc riêng "event có property tồn tại" |

### Phân trang và xóa segment

| Số segment trong tài khoản | Xem mỗi trang | Xóa cùng lúc tối đa |
|---|---|---|
| ≥ 1.000 | 500 | 500 |
| < 1.000 | 50 | 50 |

---

## 4.5 RFM Analysis

Phân nhóm người dùng theo 3 chiều:

| Chiều | Câu hỏi |
|---|---|
| **R** — Recency | Lần cuối họ hoạt động là bao giờ? |
| **F** — Frequency | Họ hoạt động thường xuyên không? |
| **M** — Monetary | Họ chi bao nhiêu tiền? |

Kết quả chia người dùng thành các nhóm như Champions, Loyal, At Risk, Hibernating, Lost…
Mỗi nhóm cần chiến lược khác nhau:

| Nhóm | Đặc điểm | Chiến lược |
|---|---|---|
| **Champions** | Mới mua, mua nhiều, chi nhiều | Giữ chân, loyalty, referral |
| **Loyal** | Mua đều đặn | Upsell, cross-sell |
| **At Risk** | Từng tốt nhưng lâu không quay lại | Win-back, ưu đãi mạnh |
| **Hibernating / Lost** | Lâu rồi không hoạt động | Reactivation, hoặc chấp nhận buông |

---

## 4.6 Mẹo thực chiến

| Mẹo | Lý do |
|---|---|
| Đặt tên segment theo quy ước | Ví dụ `[PBS] Cart Abandoners 7d`, `[LIVE] New Signup`. Sau 6 tháng sẽ có hàng trăm segment |
| Luôn kiểm tra size segment trước khi gửi | Segment 2 triệu người ≠ segment bạn định nhắm |
| Tạo control group từ đầu | Không có nhóm đối chứng thì không biết campaign có thật sự hiệu quả hay không |
| Loại Test Users khỏi campaign thật | Tránh gửi nhầm cho nội bộ |
| Segment nhỏ + thông điệp đúng > segment to + thông điệp chung chung | Tỉ lệ chuyển đổi và tỉ lệ unsubscribe khác nhau rất xa |

## 4.7 Checklist

- [ ] Tôi phân biệt được PBS và Live segment, và biết khi nào dùng cái nào
- [ ] Tôi nhớ dashboard Today chỉ lọc được bằng live segment
- [ ] Tôi biết include/exclude chỉ hoạt động với PBS
- [ ] Tôi hiểu vì sao số export có thể lệch với size segment
- [ ] Tôi đã đặt quy ước đặt tên segment cho team

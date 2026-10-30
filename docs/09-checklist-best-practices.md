# 09 — Checklist & Best Practices

## 9.1 Checklist trước khi go-live một project CleverTap

### Dữ liệu

- [ ] Schema event đã được tài liệu hóa và cả dev lẫn marketing cùng đồng thuận
- [ ] Quy ước đặt tên event/property thống nhất (một kiểu viết hoa, động từ quá khứ)
- [ ] Kiểu dữ liệu property nhất quán (`Price` luôn là số, không phải chuỗi)
- [ ] `Charged` được dùng cho mọi giao dịch, có `Amount` và `Items`
- [ ] `onUserLogin` được gọi **sớm nhất có thể** sau khi biết danh tính người dùng
- [ ] User properties cơ bản được gửi: `Name`, `Email`, `Phone`, `Identity`
- [ ] Các cờ `MSG-email` / `MSG-push` / `MSG-sms` / `MSG-whatsapp` phản ánh đúng ý muốn người dùng
- [ ] **Không** gửi dữ liệu nhạy cảm (CMND/CCCD, số thẻ, mật khẩu) lên CleverTap
- [ ] Project dev/staging tách riêng khỏi production

### Vận hành

- [ ] **Frequency cap** đã bật cho từng kênh
- [ ] **Control group** đã thiết lập (gợi ý 5–10%)
- [ ] Profile nội bộ đã đánh dấu **Test profile**
- [ ] Phân quyền (**Roles**) đúng — không phải ai cũng được publish campaign
- [ ] **Approval workflow** bật nếu team đông người
- [ ] Quy ước đặt tên segment và label campaign đã thống nhất

### Kênh

- [ ] FCM / APNS đã cấu hình và test trên thiết bị thật
- [ ] Email: domain đã xác thực (SPF/DKIM), có link unsubscribe
- [ ] Deep link mở đúng màn hình trên cả iOS và Android
- [ ] In-App không che mất chức năng quan trọng của app

---

## 9.2 Mười sai lầm phổ biến

| # | Sai lầm | Hậu quả | Cách tránh |
|---|---|---|---|
| 1 | Tạo event cho từng nút bấm | Hàng trăm event rác, không phân tích được | Ít event, nhiều property |
| 2 | Gọi `onUserLogin` muộn | Mất dữ liệu hành vi đầu phễu | Gọi ngay khi biết danh tính |
| 3 | Không có control group | Không biết campaign có hiệu quả thật hay không | Bật control group 5–10% ngay từ đầu |
| 4 | Không bật frequency cap | User nhận 7 push/ngày → tắt thông báo | Bật cap ngay ngày đầu |
| 5 | Tối ưu CTR một mình | Tiêu đề giật gân, tỉ lệ hủy nhận tin tăng | Đo conversion và retention |
| 6 | Gửi mã giảm giá ngay bước đầu của journey bỏ giỏ | Dạy user bỏ giỏ hàng có chủ đích | Để giảm giá ở bước cuối |
| 7 | A/B test đổi nhiều biến cùng lúc | Không biết biến nào tạo khác biệt | Mỗi lần một biến |
| 8 | Dựng journey trực tiếp trên canvas | Sửa cấu trúc live rất phiền | Vẽ ra giấy trước |
| 9 | Không đánh dấu test profile | Số liệu thật bị bẩn | Mark as test profile |
| 10 | Dùng phễu để "chứng minh" giả thuyết có sẵn | Bỏ sót vấn đề thật | Dùng Flows xem đường đi thực tế |

---

## 9.3 Nguyên tắc về nội dung tin nhắn

| Nguyên tắc | Chi tiết |
|---|---|
| **Một tin nhắn, một việc cần làm** | Hai CTA trong một push = không ai bấm cái nào |
| **Luôn có fallback cho personalization tag** | `{{Name \| default: "bạn"}}` |
| **Nội dung phải khớp với deep link** | Hứa giảm giá thì mở ra phải thấy giảm giá |
| **Tôn trọng múi giờ** | Dùng `Timezone` của user hoặc bật Best Time / IntelliTime |
| **Transactional tách khỏi marketing** | OTP và xác nhận đơn hàng không bị frequency cap chặn |
| **Có đường thoát** | Email phải có unsubscribe; push phải có cách tắt theo chủ đề |

---

## 9.4 Nguyên tắc đo lường

```
Chỉ số bề mặt          →  Chỉ số thật
───────────────────────────────────────
Sent                   →  Delivered
Open rate              →  Click rate
Click rate             →  Conversion rate
Conversion rate        →  Conversion rate TRỪ ĐI control group
Conversion ngắn hạn    →  Retention dài hạn
```

Càng đi xuống dưới càng khó đo nhưng càng gần sự thật.

**Câu hỏi kiểm chứng:** *"Nếu tôi tắt campaign này, điều gì thay đổi?"*
Nếu không trả lời được bằng số, campaign đó chưa được đo đúng.

---

## 9.5 Quy ước đặt tên gợi ý

### Event
```
Title Case, động từ quá khứ
✅  Product Viewed, Added to Cart, Charged, Video Completed
❌  productView, click_button, ViewProduct
```

### Segment
```
[LOẠI] Mô tả + khung thời gian
✅  [PBS] Cart Abandoners 7d
    [LIVE] New Signup
    [LIST] VIP Customers Q4
```

### Campaign
```
[Kênh] Mục đích - Đối tượng - Thời gian
✅  [Push] Winback - Inactive 30d - 2026-11
    [Email] Onboarding D3 - New Users
```

### Label
```
Phân loại theo mục đích
✅  promo, transactional, onboarding, winback, test, q4-2026
```

---

## 9.6 Quyền riêng tư và tuân thủ

| Việc | Lưu ý |
|---|---|
| **Dữ liệu nhạy cảm** | Không gửi CMND/CCCD, số thẻ, mật khẩu, dữ liệu sức khỏe lên CleverTap |
| **Vị trí** | Latitude/longitude chỉ được ghi nếu người dùng đồng ý |
| **GDPR** | CleverTap hỗ trợ xóa dữ liệu theo yêu cầu người dùng |
| **ATT (iOS)** | Tôn trọng App Tracking Transparency |
| **Opt-out** | Phải tôn trọng cờ `MSG-*` — người dùng tắt là tắt thật |
| **Audit log** | Có sẵn trong Security & Data Governance — dùng để truy vết ai làm gì |

---

## 9.7 Bảng tra nhanh các giới hạn

| Giới hạn | Giá trị |
|---|---|
| Số bước tối đa trong funnel | 8 |
| Conversion window mặc định của funnel | 5 ngày |
| Giá trị tối đa mỗi multi-value event property | 100 |
| Độ dài tối đa mỗi giá trị trong array | 512 ký tự |
| Profile có thể "Exclude from control groups" | 20 |
| Segment hiển thị/xóa mỗi lần (tài khoản ≥1.000 segment) | 500 |
| Segment hiển thị/xóa mỗi lần (tài khoản <1.000 segment) | 50 |
| Campaign đã publish | Stop được, **không** pause được |
| Journey đang live | Sửa WHAT được; sửa cấu trúc phải tạo draft mới |
| Include/Exclude segment | Chỉ PBS; không cho bulletins và A/B testing |

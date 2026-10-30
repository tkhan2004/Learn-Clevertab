# 06 — Campaigns

> Campaign = **một lần gửi** tin nhắn tới một nhóm người dùng qua một kênh.
> Nhiều bước, nhiều kênh, có rẽ nhánh → đó là **Journey** (xem [07](07-journeys.md)).

## 6.1 Khung tư duy 4 câu hỏi

Mọi campaign đều trả lời đúng 4 câu:

```
┌──────────┬────────────────────────────────────────────┐
│  GOAL    │  Muốn đạt được gì?  (đo bằng chỉ số nào?)  │
├──────────┼────────────────────────────────────────────┤
│  WHO     │  Gửi cho ai?        (segment nào?)         │
├──────────┼────────────────────────────────────────────┤
│  WHAT    │  Gửi nội dung gì?   (kênh + nội dung)      │
├──────────┼────────────────────────────────────────────┤
│  WHEN    │  Gửi lúc nào?       (delivery type)        │
└──────────┴────────────────────────────────────────────┘
```

Nếu không trả lời được **GOAL** bằng một con số cụ thể, đừng gửi campaign.

---

## 6.2 Các kênh gửi (Channels)

| Nhóm | Kênh |
|---|---|
| **Mobile** | Push, In-App Messages, App Inbox, Native Display |
| **Web** | Web Push, Web Pop-up, Web Exit Intent, Web Native Display, Web Inbox |
| **Nhắn tin** | Email, SMS, WhatsApp, RCS |
| **Remarketing** | Facebook Audiences, Google Ads, TikTok |
| **Kỹ thuật** | Webhooks, Amazon EventBridge, Segment, mParticle |

### Chọn kênh thế nào

| Kênh | Mức can thiệp | Phù hợp với |
|---|---|---|
| **In-App** | Thấp — chỉ hiện khi user đang mở app | Giới thiệu tính năng, onboarding, upsell đúng lúc |
| **App Inbox** | Rất thấp — user tự vào xem | Tin tức, ưu đãi không gấp |
| **Push** | Trung bình — ngắt quãng user | Nhắc việc, tin quan trọng, giao dịch |
| **Email** | Trung bình | Nội dung dài, hóa đơn, bản tin |
| **SMS / WhatsApp** | Cao — tốn phí, riêng tư | OTP, giao dịch, nhắc quan trọng |

**Nguyên tắc:** dùng kênh ít can thiệp nhất mà vẫn đạt mục tiêu. Lạm dụng push là cách
nhanh nhất để bị tắt thông báo hoặc gỡ app.

---

## 6.3 Ba loại campaign

| Loại | Ý nghĩa |
|---|---|
| **Single Message** | Gửi một nội dung duy nhất cho toàn bộ đối tượng |
| **A/B Test** | Chia đối tượng thành nhiều nhóm, mỗi nhóm nhận biến thể khác nhau |
| **Message on user property** | Nội dung thay đổi theo thuộc tính người dùng |

---

## 6.4 Delivery types — "gửi lúc nào"

| Delivery type | Khi nào gửi | Ví dụ |
|---|---|---|
| **One Time** | Gửi ngay một lần | Thông báo bảo trì hệ thống |
| **Action** | Ngay khi user làm một event | User `Added to Cart` → gửi gợi ý sản phẩm kèm |
| **Inaction** | Khi user **không** làm event B sau khi làm A | `Added to Cart` nhưng không `Charged` sau 2 giờ |
| **On a date/time** | Vào mốc thời gian cụ thể | Sinh nhật, kỷ niệm đăng ký |
| **Recurring** | Lặp lại theo lịch | Bản tin hằng tuần |
| **Multiple Date** | Nhiều mốc ngày định sẵn | Chuỗi sự kiện khuyến mãi |
| **API** | Trigger từ hệ thống bên ngoài qua API | Đơn hàng đã giao |

> **Action** và **Inaction** là hai loại tạo ra phần lớn giá trị. Đây là chỗ "tự động hóa"
> thật sự bắt đầu.

---

## 6.5 Luồng tạo campaign

```
1. Bấm  + Campaigns
2. Chọn  kênh  (Push / Email / In-App / SMS …)
3. WHO   — chọn đối tượng nhận (segment, include/exclude)
4. WHAT  — soạn nội dung tin nhắn (có thể dùng personalization tag)
5. WHEN  — chọn delivery type + thời điểm kích hoạt
6. Chọn loại campaign: Single Message / A/B Test / Message on user property
7. (tùy chọn) Gắn Labels để nhóm campaign khi làm báo cáo
8. Publish
```

### ⚠️ Lưu ý quan trọng

> **Campaign đã publish thì có thể DỪNG (stop) nhưng KHÔNG THỂ TẠM DỪNG (pause).**

Dừng rồi là hết — muốn chạy lại phải tạo campaign mới. Vì vậy:
- Luôn gửi thử cho **Test Users** trước
- Kiểm tra lại size segment
- Kiểm tra deep link / URL
- Kiểm tra personalization tag có fallback chưa

### Labels

Gắn nhãn để nhóm campaign khi báo cáo và phân tích. Có thể gắn lúc tạo hoặc sau đó qua
**Edit Campaign**.

Gợi ý quy ước label: `promo`, `transactional`, `onboarding`, `winback`, `q4-2026`.

---

## 6.6 Cá nhân hóa nội dung

### Personalization tags

Chèn thuộc tính người dùng vào nội dung:

```
Chào {{Name}}, giỏ hàng của bạn vẫn còn {{Cart Items}} sản phẩm đang chờ!
```

**Luôn đặt giá trị dự phòng (fallback).** Nếu không, user thiếu `Name` sẽ nhận được
*"Chào , giỏ hàng..."* — trông rất thiếu chuyên nghiệp.

```
Chào {{Name | default: "bạn"}}
```

### Liquid Tags

Thêm logic vào nội dung:

```liquid
{% if Plan Type == "Premium" %}
  Cảm ơn bạn đã đồng hành cùng gói Premium!
{% else %}
  Nâng cấp Premium ngay hôm nay để nhận ưu đãi.
{% endif %}
```

### Linked Content

Lấy nội dung động qua API từ web server / tool bên thứ ba / hệ thống nội bộ ngay tại thời
điểm gửi. Dùng cho: giá realtime, tồn kho, điểm tích lũy.

### Catalogs & Recommendations

Đưa sản phẩm gợi ý vào tin nhắn dựa trên hành vi người dùng.

---

## 6.7 Tính năng nâng cao

### Campaign Limits (Frequency Cap)

Giới hạn số tin nhắn một người có thể nhận mỗi ngày. **Bật ngay từ đầu.**

Không có frequency cap, user có thể nhận 7 push/ngày từ 7 campaign khác nhau của cùng bạn.
Kết quả: tắt thông báo vĩnh viễn.

Gợi ý khởi điểm:
| Kênh | Giới hạn/ngày |
|---|---|
| Push | 2 |
| Email | 1 |
| SMS | 1 (chỉ transactional) |

> Tin nhắn **transactional** (OTP, xác nhận đơn hàng) thường được miễn trừ khỏi cap.

### Control Groups

Nhóm bị loại khỏi mọi campaign marketing → dùng làm đối chứng để đo hiệu quả thật.
Xem chi tiết ở [05 — Real Impact](05-behavioral-analytics.md#58-real-impact--control-group).

### A/B Testing

So sánh biến thể. Nguyên tắc:

| Quy tắc | Lý do |
|---|---|
| Mỗi lần chỉ đổi **một** biến | Đổi cả tiêu đề lẫn ảnh lẫn giờ gửi → không biết cái nào tạo khác biệt |
| Cỡ mẫu phải đủ lớn | 100 người mỗi nhánh không kết luận được gì |
| Chọn trước chỉ số thắng thua | Click rate hay conversion rate? Quyết định trước khi chạy |
| Chạy đủ thời gian | Dừng sớm khi thấy nhánh A dẫn trước = tự lừa mình |

> ⚠️ Nhắc lại từ [04](04-segmentation.md): **include/exclude segment không khả dụng cho A/B testing.**

### Best Time / IntelliTime

Để AI chọn thời điểm gửi tối ưu cho **từng** người dùng thay vì gửi đồng loạt một giờ.

### Approval Workflow

Quy trình duyệt campaign trước khi publish — nên bật khi team đông người để tránh gửi nhầm.

---

## 6.8 Campaign Reports

Các chỉ số cần theo dõi:

| Chỉ số | Ý nghĩa | Cảnh báo khi |
|---|---|---|
| **Sent** | Số tin đã gửi | Lệch nhiều so với size segment → kiểm tra reachability |
| **Delivered** | Số tin đến được thiết bị | Tỉ lệ thấp → token hết hạn, user gỡ app |
| **Viewed / Impressions** | Số lần được nhìn thấy | |
| **Clicked / CTR** | Tỉ lệ bấm vào | Thấp → nội dung hoặc đối tượng sai |
| **Conversion** | Số người thực hiện hành động mục tiêu | Chỉ số quan trọng nhất |
| **Unsubscribe / Opt-out** | Số người hủy nhận tin | Tăng → bạn đang gửi quá nhiều hoặc sai đối tượng |

**Đừng tối ưu CTR một mình.** Tiêu đề giật gân sẽ tăng CTR nhưng tăng cả tỉ lệ hủy nhận tin.
Chỉ số cuối cùng phải là **conversion** và **retention**.

---

## 6.9 Checklist trước khi Publish

- [ ] Đã xác định GOAL bằng một con số cụ thể
- [ ] Đã kiểm tra size segment
- [ ] Đã loại Test Users khỏi đối tượng thật
- [ ] Đã gửi thử tới Test Users và xem trên thiết bị thật
- [ ] Personalization tag đều có fallback
- [ ] Deep link / URL mở đúng màn hình
- [ ] Đã bật frequency cap
- [ ] Đã bật control group (nếu là campaign marketing)
- [ ] Đã gắn label
- [ ] **Nhớ: publish rồi chỉ dừng được, không pause được**

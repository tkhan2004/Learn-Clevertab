# 07 — Journeys

> Journey = **trình dựng campaign trực quan** để tạo trải nghiệm nhắn tin đa kênh tự động.
> Nếu Campaign là một phát bắn, Journey là cả một kịch bản.

## 7.1 Campaign vs Journey

| | Campaign | Journey |
|---|---|---|
| Số bước | 1 | Nhiều |
| Số kênh | 1 | Nhiều (omnichannel) |
| Rẽ nhánh theo hành vi | Không | Có |
| Chờ giữa các bước | Không | Có |
| Giao diện | Form | Canvas kéo thả |
| Dùng khi | Thông báo đơn lẻ | Onboarding, winback, bỏ giỏ hàng, chuỗi nuôi dưỡng |

**Quy tắc chọn:** nếu kịch bản có chữ *"nếu… thì… còn không thì…"* hoặc *"chờ X ngày rồi…"*
→ dùng Journey.

---

## 7.2 Cấu trúc một Journey

```
          ┌───────────────────┐
          │  ENTRY CRITERIA   │   Ai được vào journey này?
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │   Gửi Push        │   Node tin nhắn
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │   Chờ 24 giờ      │   Node chờ
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │ Đã mua hàng chưa? │   Conditional Split
          └────┬─────────┬────┘
          CÓ   │         │   KHÔNG
         ┌─────▼───┐ ┌───▼──────────┐
         │ Thoát   │ │ Gửi Email    │
         │ (thành  │ │ kèm mã giảm  │
         │  công)  │ └───┬──────────┘
         └─────────┘     │
                   ┌─────▼─────────┐
                   │  Chờ 48 giờ   │
                   └─────┬─────────┘
                         │
                   ┌─────▼─────────┐
                   │   Gửi SMS     │
                   └─────┬─────────┘
                         │
                   ┌─────▼─────────┐
                   │     Thoát     │
                   └───────────────┘

          ┌───────────────────┐
          │       GOAL        │   Đo thành công bằng gì? (ví dụ: Charged)
          └───────────────────┘
```

### Các thành phần

| Thành phần | Vai trò |
|---|---|
| **Entry criteria** | Điều kiện quyết định **user nào đủ tiêu chuẩn** vào journey |
| **Canvas** | Mặt phẳng để sắp xếp các node, định nghĩa luồng giao tiếp |
| **Nodes** | Các khối: gửi tin nhắn, chờ, rẽ nhánh điều kiện, thao tác khác |
| **Conditional Split** | Rẽ nhánh theo điều kiện hành vi/thuộc tính |
| **IntelliNode** | Node dùng AI để quyết định (ví dụ chọn kênh/thời điểm tối ưu) |
| **Goals** | Mục tiêu journey hướng tới — cơ sở đo hiệu quả |
| **States** | Trạng thái của journey (draft, live…) |

> Journey có thể bắt đầu ở **bất kỳ giai đoạn nào** của vòng đời người dùng: onboarding,
> re-engagement, hoặc giao dịch.

---

## 7.3 Các bước dựng một Journey

```
1. Định nghĩa ENTRY CRITERIA  — ai đủ điều kiện vào journey
2. Sắp xếp NODES trên canvas  — định hình luồng giao tiếp
3. Đặt GOALS                  — journey này hướng tới kết quả gì
4. Thêm MESSAGES trên các kênh — Email, Push, SMS…
5. Kiểm thử và publish
```

---

## 7.4 ⚠️ Sửa Journey đang chạy

Đây là điều rất dễ mắc lỗi:

| Loại thay đổi | Có sửa trực tiếp trên journey đang live không? |
|---|---|
| Phần **WHAT** — nội dung tin nhắn, cấu hình provider | ✅ Sửa trực tiếp được |
| **Thay đổi cấu trúc** — entry criteria, đường đi giữa các node, goals | ❌ Phải tạo **bản draft mới** |

**Hệ quả thực tế:** sửa typo trong nội dung thì dễ. Nhưng muốn thêm một nhánh rẽ → tạo
phiên bản mới, user đang đi dở journey cũ vẫn chạy theo phiên bản cũ.

👉 **Vì vậy: thiết kế cấu trúc journey thật kỹ trên giấy trước khi dựng.**

---

## 7.5 Ba kịch bản Journey kinh điển

### A. Onboarding (người dùng mới)

Mục tiêu: đưa user tới khoảnh khắc "à-há" càng nhanh càng tốt.

```
Entry: App Installed
  │
  ├─ Ngay lập tức  →  In-App: chào mừng + hướng dẫn 3 bước
  │
  ├─ Chờ 1 ngày
  │   └─ Đã hoàn tất profile chưa?
  │       ├─ Rồi    → đi tiếp
  │       └─ Chưa   → Push: "Hoàn tất hồ sơ để nhận ưu đãi"
  │
  ├─ Chờ 3 ngày
  │   └─ Đã dùng tính năng chính chưa?
  │       ├─ Rồi    → Thoát (thành công)
  │       └─ Chưa   → Email: hướng dẫn chi tiết
  │
  └─ Chờ 7 ngày
      └─ Vẫn chưa dùng → Push: ưu đãi dùng thử
      
Goal: user thực hiện event "tính năng chính"
```

### B. Cart Abandonment (bỏ giỏ hàng)

Mục tiêu: kéo người dùng quay lại hoàn tất đơn.

```
Entry: Added to Cart  (live trigger)
  │
  ├─ Chờ 1 giờ
  │   └─ Đã Charged chưa?  ── Rồi → Thoát (thành công)
  │                         └─ Chưa ↓
  ├─ Push: "Giỏ hàng của bạn đang chờ"
  │
  ├─ Chờ 24 giờ
  │   └─ Đã Charged chưa?  ── Rồi → Thoát
  │                         └─ Chưa ↓
  ├─ Email: hiển thị sản phẩm trong giỏ + đánh giá sản phẩm
  │
  ├─ Chờ 48 giờ
  │   └─ Đã Charged chưa?  ── Rồi → Thoát
  │                         └─ Chưa ↓
  └─ Email/SMS: mã giảm giá 10%  ← chỉ giảm giá ở bước CUỐI
  
Goal: Charged
```

> 💡 **Vì sao giảm giá để cuối:** nếu gửi mã giảm ngay ở bước 1, bạn đang dạy người dùng
> rằng cứ bỏ giỏ hàng là sẽ có khuyến mãi. Họ sẽ bỏ giỏ hàng có chủ đích.

### C. Win-back (kéo lại người dùng rời bỏ)

```
Entry: không App Launched trong 30 ngày
  │
  ├─ Push: "Có gì mới ở [app]" (nêu tính năng mới)
  │
  ├─ Chờ 3 ngày
  │   └─ Đã quay lại chưa? ── Rồi → Thoát
  │                          └─ Chưa ↓
  ├─ Email: tổng hợp nội dung họ bỏ lỡ
  │
  ├─ Chờ 7 ngày
  │   └─ Đã quay lại chưa? ── Rồi → Thoát
  │                          └─ Chưa ↓
  └─ Ưu đãi mạnh / khảo sát "vì sao bạn rời đi?"
  
Goal: App Launched
```

---

## 7.6 Nguyên tắc thiết kế Journey

| Nguyên tắc | Giải thích |
|---|---|
| **Luôn có lối thoát (exit)** | User đã mua rồi thì đừng tiếp tục nhắc họ mua. Đặt điều kiện thoát sau mỗi bước |
| **Tôn trọng khoảng nghỉ** | Đừng xếp 3 tin trong 1 giờ. Chờ ít nhất vài giờ giữa các bước |
| **Leo thang kênh dần** | In-App/Push (rẻ, nhẹ) → Email → SMS (đắt, can thiệp mạnh). Đừng bắt đầu bằng SMS |
| **Một journey một mục tiêu** | Journey vừa onboarding vừa upsell vừa khảo sát = không đo được gì |
| **Đặt Goal trước khi dựng** | Không có goal thì không có cách nào biết journey có hiệu quả |
| **Thiết kế trên giấy trước** | Vì sửa cấu trúc journey đang live rất phiền |
| **Dùng frequency cap toàn cục** | Journey không biết các campaign khác đang gửi gì cho cùng user |

---

## 7.7 Journey Performance

Sau khi journey chạy, theo dõi:

| Chỉ số | Câu hỏi |
|---|---|
| **Entry count** | Bao nhiêu người vào journey? |
| **Drop-off từng node** | Họ rơi ở bước nào? |
| **Goal conversion** | Bao nhiêu % đạt mục tiêu? |
| **Thời gian tới goal** | Mất bao lâu để chuyển đổi? |
| **So với control group** | Tác động thật là bao nhiêu? |

Nếu có một node mà ai cũng rơi tại đó → node đó sai (nội dung sai, thời điểm sai, hoặc
điều kiện rẽ nhánh sai).

## 7.8 Checklist

- [ ] Tôi phân biệt được khi nào dùng Campaign và khi nào dùng Journey
- [ ] Tôi biết sửa cấu trúc journey đang live phải tạo draft mới
- [ ] Tôi đã vẽ journey trên giấy trước khi dựng trên canvas
- [ ] Mỗi journey của tôi đều có điều kiện thoát và một goal duy nhất
- [ ] Tôi leo thang kênh từ nhẹ tới nặng, không bắt đầu bằng SMS

# 01 — Tổng quan CleverTap

## 1.1 CleverTap là gì?

CleverTap là nền tảng **customer engagement & retention** (gắn kết và giữ chân người dùng)
dành cho app và website. Nó gộp ba thứ mà bình thường phải dùng ba công cụ riêng:

| Nhóm chức năng | CleverTap làm gì | Công cụ tương đương |
|---|---|---|
| **Analytics** | Theo dõi hành vi người dùng, funnel, cohort, retention | Mixpanel, Amplitude, Firebase Analytics |
| **Segmentation** | Chia người dùng thành nhóm theo hành vi/thuộc tính | CDP, Segment |
| **Engagement** | Gửi push, email, SMS, in-app, WhatsApp… theo segment | Braze, OneSignal, MoEngage |

Điểm khác biệt chính so với công cụ analytics thuần: **dữ liệu và hành động nằm chung một chỗ**.
Bạn phân tích ra một nhóm người dùng có vấn đề → bấm gửi campaign ngay cho nhóm đó, không cần
export CSV sang hệ thống khác.

## 1.2 Bài toán CleverTap giải quyết

Vấn đề kinh điển của app: **cài đặt nhiều nhưng giữ chân kém**.

```
100 lượt cài đặt
 └─ 40 mở app lần 2        (60% rơi ngay ngày đầu)
     └─ 15 còn dùng sau 7 ngày
         └─ 5  còn dùng sau 30 ngày
             └─ 1  trả tiền
```

CleverTap trả lời ba câu hỏi:

1. **Ai đang rơi rụng?** → Analytics (Funnels, Cohorts, Retention)
2. **Vì sao họ rơi rụng?** → Segmentation + event properties
3. **Làm gì để kéo họ lại?** → Campaigns / Journeys

## 1.3 Mô hình dữ liệu cốt lõi

Đây là phần quan trọng nhất phải nắm. Mọi thứ trong CleverTap xoay quanh 5 khái niệm:

```
┌─────────────────────────────────────────────────────────────┐
│                      USER PROFILE                           │
│  (mỗi người dùng = 1 profile, tạo tự động khi mở app/web)   │
│                                                             │
│  ┌─────────────┐  ┌──────────────────┐  ┌───────────────┐  │
│  │ IDENTIFIERS │  │  USER PROPERTIES │  │    EVENTS     │  │
│  │ CleverTap ID│  │  Name, Email,    │  │ App Launched  │  │
│  │ Email       │  │  Phone, Gender,  │  │ Product Viewed│  │
│  │ Phone       │  │  DOB, Timezone,  │  │ Charged       │  │
│  │ Identity    │  │  + custom props  │  │ + custom      │  │
│  └─────────────┘  └──────────────────┘  └───────────────┘  │
└─────────────────────────────────────────────────────────────┘
          │
          │  lọc theo điều kiện
          ▼
     ┌─────────┐
     │ SEGMENT │  "Users đã Add to Cart nhưng chưa Charged trong 7 ngày"
     └────┬────┘
          │
    ┌─────┴──────┐
    ▼            ▼
┌─────────┐  ┌──────────┐
│CAMPAIGN │  │ JOURNEY  │   gửi tin nhắn tới segment đó
└────┬────┘  └────┬─────┘
     └──────┬─────┘
            ▼
       ┌─────────┐
       │ REPORTS │  đo xem có hiệu quả không
       └─────────┘
```

### Giải thích từng tầng

**1. User Profile** — Khi SDK được tích hợp, mỗi người mở app/website sẽ được tạo một profile.
Profile gồm 3 thành phần: *identifiers* (định danh), *properties* (thuộc tính), *events* (hành vi).

**2. Events** — Hành động người dùng thực hiện. Có 2 loại:
- *System events*: CleverTap tự ghi (App Installed, App Launched, Session Concluded,
  Notification Sent/Viewed/Clicked…)
- *Custom events*: do team định nghĩa và gửi qua SDK/API (Product Viewed, Added to Cart…)

**3. User Properties** — Thuộc tính mô tả người dùng (Name, Email, Phone, Gender, DOB,
Timezone, các cờ MSG-email / MSG-push / MSG-sms / MSG-whatsapp) và các thuộc tính tự định nghĩa.

**4. Segments** — Nhóm người dùng thỏa điều kiện về hành vi (đã/chưa làm gì) hoặc thuộc tính,
trong một khoảng thời gian. **Toàn bộ dashboard có thể lọc theo segment.**

**5. Campaigns / Journeys** — Nơi "làm gì đó" với segment. Campaign là một lần gửi (một kênh),
Journey là chuỗi nhiều bước đa kênh trên một canvas trực quan.

## 1.4 Bản đồ dashboard

Các khu vực chính bạn sẽ dùng (theo cấu trúc docs chính thức):

| Khu vực | Dùng để làm gì |
|---|---|
| **Getting Started** | Dashboard setup, events, user profiles, schema, access management |
| **Segmentation** | Find People, Segment Builder, RFM Analysis |
| **Behavioral Analytics** | Boards, Event analysis, Cohorts, Funnels, Trends, Pivots, Flows, Real Impact, Anomaly Detection |
| **Engagement Channels** | Push, In-App, Email, Web Push, Web Pop-up, App Inbox, WhatsApp, SMS, RCS, Webhooks, Google Ads… |
| **Campaigns** | Tạo/duyệt/báo cáo campaign, control groups, A/B test, frequency cap |
| **Journeys** | Canvas, nodes, states, IntelliNode, Conditional Split |
| **Content Manager** | Files, templates, content blocks |
| **Hyper Personalization** | Personalization tags, Liquid tags, Linked Content, Catalogs, Recommendations |
| **CleverAI** | Agent Hub, Scribe, IntelliChannel, IntelliTime, IntelliAB, Predictions |
| **Data Management** | Import/export (API, data warehouse, CSV, SFTP) |
| **Promotions** | Promo campaigns, loyalty tiers, rewards |
| **Product Experiences** | Product A/B test, Remote Config |

## 1.5 Khái niệm "Project"

Một **Project** = một workspace dùng chung cho cả team, ứng với một app/website.
Mỗi project có bộ credentials riêng:

- **Account ID** (còn gọi là CleverTap ID / Project ID) — dùng khi tích hợp SDK
- **Passcode / Token** — dùng cho SDK
- **Account Passcode** — dùng cho Server-side API

> Môi trường dev/staging/production thường tách thành các project riêng để dữ liệu test
> không làm bẩn số liệu thật.

## 1.6 Điều cần nhớ sau bài này

- CleverTap = Analytics + Segmentation + Engagement trong một nền tảng.
- Mọi phân tích và mọi campaign đều bắt đầu từ **Events** và **User Properties**.
  Dữ liệu đầu vào sai thì mọi thứ phía sau sai theo.
- **Segment** là cầu nối giữa "phân tích" và "hành động".
- Học theo luồng dữ liệu, đừng học theo menu.

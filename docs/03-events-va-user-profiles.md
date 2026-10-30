# 03 — Events & User Profiles

> Đây là tầng dữ liệu đầu vào. **Sai ở đây thì mọi segment, analytics và campaign phía sau đều sai.**

## 3.1 User Profile

Sau khi tích hợp SDK, CleverTap tạo một profile cho **mỗi** người mở app hoặc truy cập website.

Một profile gồm 3 phần:

```
USER PROFILE
├── IDENTIFIERS    ← định danh: ai là ai
├── PROPERTIES     ← mô tả: người này như thế nào
└── EVENTS         ← hành vi: người này đã làm gì
```

### Identifiers

| Identifier | Ghi chú |
|---|---|
| **CleverTap ID** | ID duy nhất CleverTap tự sinh, luôn có |
| **Identity** | ID riêng của hệ thống bạn (user_id trong DB) |
| **Email** | |
| **Phone** | Phải có mã quốc gia, dạng `+84912345678` |
| **Facebook ID / custom ID** | Tùy nhu cầu |

### Hai trạng thái profile

| Trạng thái | Định nghĩa |
|---|---|
| **Anonymous** | Chưa có thông tin định danh duy nhất |
| **Identified** | Đã có email hoặc user ID, liên hệ được qua email/push |

Trạng thái tự động cập nhật khi có thêm thông tin.

### ⚠️ Cạm bẫy: sự kiện trước khi đăng nhập

Đây là điểm rất hay gây hiểu nhầm số liệu:

```
Người dùng mở app (chưa login)
 └── Anonymous profile  ──┐
      • App Launched      │  các event này KHÔNG được chuyển
      • Product Viewed    │  sang profile đã định danh
                          │
Người dùng bấm Đăng nhập  │
 └── onUserLogin()        │
      │                   │
      ▼                   ▼
 Identified profile    event cũ bị làm mờ (grayed out),
      • Added to Cart   bị LOẠI khỏi analytics và segmentation
      • Charged
```

**Hệ quả thực tế:** nếu app yêu cầu login muộn, một phần hành vi đầu phễu sẽ không được tính.
Nên gọi `onUserLogin` càng sớm càng tốt sau khi biết danh tính người dùng.

### User Properties mặc định (system)

CleverTap hỗ trợ sẵn các thuộc tính sau — chỉ cần SDK gửi lên:

| Nhóm | Thuộc tính |
|---|---|
| Danh tính | `Name`, `Email`, `Identity`, `Phone` |
| Nhân khẩu học | `Gender`, `DOB`, `Timezone` |
| Đăng ký nhận tin | `MSG-email`, `MSG-push`, `MSG-sms`, `MSG-whatsapp` |

> Nếu có `DOB`, CleverTap **tự tính tuổi** → segment theo độ tuổi (ví dụ 25–30) mà không cần
> gửi thêm field.

Ngoài ra profile còn có sẵn dữ liệu thiết bị và vị trí.

### Custom User Properties

Thuộc tính riêng của nghiệp vụ, ví dụ:

| Thuộc tính | Giá trị ví dụ | Dùng để |
|---|---|---|
| `Plan Type` | Free / Premium / Enterprise | Segment theo gói |
| `City` | Hanoi / HCMC | Cá nhân hóa nội dung |
| `Loyalty Tier` | Silver / Gold | Ưu đãi theo hạng |
| `Last Order Value` | 450000 | Trigger upsell |

**5 cách cập nhật user property:**

1. SDK — `profilePush`
2. User Profile API (server-side)
3. Upload CSV
4. SFTP import
5. **Gián tiếp qua event** — khi track event, profile cũng được cập nhật giá trị mới nhất

---

## 3.2 Events

Event = hành động người dùng thực hiện.

### System Events (tự động)

Có ngay sau khi tích hợp SDK, không cần code thêm:

| Event | Khi nào ghi |
|---|---|
| `App Installed` | Lần đầu cài app |
| `App Launched` | Mỗi lần mở app |
| `Session Concluded` | Kết thúc phiên |
| `Notification Sent` | Hệ thống gửi push |
| `Notification Viewed` | Người dùng thấy push |
| `Notification Clicked` | Người dùng bấm push |

> `Notification Sent` chỉ xuất hiện trên **Event dashboard**, không hiện trên User Profile.

### Custom Events

Do team tự định nghĩa và gửi qua SDK hoặc API. Ví dụ: `Product Viewed`, `Added to Cart`,
`Search Performed`, `Video Completed`.

### Event `Charged` — trường hợp đặc biệt

Dùng cho **mọi giao dịch**. Cấu trúc riêng:

```jsonc
{
  "Charged": {
    "Amount": 549000,              // tổng tiền giao dịch
    "Payment Mode": "Credit Card",
    "Charged ID": "ORD-10231",
    "Items": [                     // mảng sản phẩm — field dành riêng
      { "Product Name": "Áo thun",  "Category": "Thời trang", "Price": 249000 },
      { "Product Name": "Quần jean","Category": "Thời trang", "Price": 300000 }
    ]
  }
}
```

Dùng `Charged` thay vì tự đặt tên `Purchase` vì:
- CleverTap **đánh dấu user là khách hàng** (customer)
- Mở khóa các báo cáo doanh thu, LTV, RFM
- `Items` là field dành riêng, không áp dụng quy tắc multi-value array thông thường

### Event Properties

Thuộc tính mô tả chi tiết một event.

| Kiểu dữ liệu hỗ trợ |
|---|
| String, Integer, Float, Boolean, Mixed |

**Multi-value array** (một key chứa nhiều giá trị):

| Giới hạn | Giá trị |
|---|---|
| Số giá trị tối đa mỗi key | 100 |
| Độ dài tối đa mỗi giá trị | 512 ký tự |
| Vượt giới hạn | Các giá trị thừa bị **bỏ** |
| Mảng rỗng | Bị **xóa** |

### Metadata tự động

Mỗi event luôn kèm thông tin user, timestamp và nguồn. Các thuộc tính hệ thống có tiền tố `CT`:

- `CT App Version`
- `CT Source`
- Latitude/Longitude — **chỉ** ghi nếu người dùng đồng ý chia sẻ vị trí

---

## 3.3 Thiết kế schema event — phần quan trọng nhất

Đây là việc làm **một lần** nhưng ảnh hưởng mãi mãi. Nguyên tắc:

### ✅ Nên

```
Ít event, nhiều property
```

| Thay vì | Hãy dùng |
|---|---|
| `Product Viewed Shoes`<br>`Product Viewed Shirts`<br>`Product Viewed Bags` | `Product Viewed` với property `Category = Shoes / Shirts / Bags` |
| `Button Clicked Home`<br>`Button Clicked Cart` | `Button Clicked` với property `Screen` + `Button Name` |

**Lý do:** property lọc được, so sánh được, breakdown được trong funnel. Tên event thì không.

### ✅ Quy ước đặt tên

| Quy tắc | Ví dụ |
|---|---|
| Thống nhất một kiểu viết | `Product Viewed` (Title Case) — đừng trộn `productViewed`, `product_viewed` |
| Động từ ở thì quá khứ | `Added to Cart`, không phải `Add to Cart` |
| Tên property cũng thống nhất | `Product Name` ở mọi event, không phải lúc `ProductName` lúc `product_name` |
| Kiểu dữ liệu nhất quán | `Price` luôn là số, đừng lúc `249000` lúc `"249.000đ"` |

### ❌ Không nên

| Lỗi | Hậu quả |
|---|---|
| Đặt event theo từng nút bấm | Hàng trăm event rác, không phân tích được |
| Gửi dữ liệu nhạy cảm (CMND, số thẻ, mật khẩu) | Vi phạm quyền riêng tư |
| Gửi event trong vòng lặp / scroll | Tốn quota, nhiễu số liệu |
| Không tách môi trường test và production | Số liệu thật bị bẩn |

### Tài liệu hóa schema

Luôn giữ một bảng schema chung cho cả team (dev + marketing):

| Event | Khi nào gửi | Properties | Kiểu | Bắt buộc |
|---|---|---|---|---|
| `Product Viewed` | User mở trang chi tiết sản phẩm | `Product ID` | String | ✓ |
| | | `Product Name` | String | ✓ |
| | | `Category` | String | ✓ |
| | | `Price` | Float | ✓ |
| | | `In Stock` | Boolean | |
| `Added to Cart` | User bấm "Thêm vào giỏ" | `Product ID`, `Quantity`, `Price` | | ✓ |
| `Charged` | Thanh toán thành công | `Amount`, `Items`, `Payment Mode` | | ✓ |

---

## 3.4 Thao tác trên dashboard

### Tìm một người dùng cụ thể

1. Vào **Find People**
2. Tìm theo email / phone / identity
3. Mở profile → xem được timeline event, properties, lịch sử nhận campaign

### Trên trang profile có thể

| Hành động | Tác dụng |
|---|---|
| **Mark as test profile** | Gắn property `ct_is_test_user = Yes`, dùng để test tin nhắn và segment |
| **Exclude from control groups** | Đảm bảo user này **luôn** nhận campaign, không bao giờ bị rơi vào control group / nhóm A/B. Tối đa **20 profile** |

### Xem debug events

Thêm `?showDebugEvents=true` vào cuối URL trang profile hoặc trang Find People.

Debug events gồm: `Identity Set`, `Identity Reset`, `Reachable By`, `Push Unregistered`.
→ Rất hữu ích khi gỡ lỗi "tại sao user này không nhận được push".

---

## 3.5 Checklist tự kiểm tra

- [ ] Tôi phân biệt được event property và user property
- [ ] Tôi biết vì sao nên dùng `Charged` thay vì event tự đặt tên
- [ ] Tôi hiểu event trước khi `onUserLogin` không được gộp vào profile đã định danh
- [ ] Tôi biết nguyên tắc "ít event, nhiều property"
- [ ] Tôi biết cách đánh dấu test profile để không làm bẩn số liệu

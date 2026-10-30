# CleverTap — Tài liệu học tập

Ghi chép cá nhân sau khóa **Getting Started with CleverTap** (CleverTap University).
Repo này là nơi mình hệ thống lại kiến thức nền tảng + hướng dẫn thao tác trên dashboard
để tra cứu nhanh khi làm việc thật.

> **Lưu ý:** nội dung được tổng hợp từ [docs.clevertap.com](https://docs.clevertap.com) và ghi chú
> khóa học. Giao diện dashboard CleverTap thay đổi khá thường xuyên — các bước thao tác nên
> được đối chiếu lại với dashboard của chính project bạn đang dùng.

---

## Mục lục

| # | Tài liệu | Nội dung |
|---|----------|----------|
| 01 | [Tổng quan CleverTap](docs/01-tong-quan-clevertap.md) | CleverTap là gì, giải quyết bài toán gì, mô hình dữ liệu cốt lõi |
| 02 | [Thuật ngữ (Glossary)](docs/02-thuat-ngu-glossary.md) | Từ điển Anh–Việt các khái niệm hay gặp |
| 03 | [Events & User Profiles](docs/03-events-va-user-profiles.md) | Dữ liệu vào hệ thống: sự kiện, thuộc tính, định danh người dùng |
| 04 | [Segmentation](docs/04-segmentation.md) | 3 loại segment, cách tạo, giới hạn cần nhớ |
| 05 | [Behavioral Analytics](docs/05-behavioral-analytics.md) | Boards, Funnels, Cohorts, Trends, Pivots, Flows, RFM |
| 06 | [Campaigns](docs/06-campaigns.md) | Kênh gửi, loại campaign, delivery type, A/B test, control group |
| 07 | [Journeys](docs/07-journeys.md) | Canvas đa kênh, entry criteria, node, goal |
| 08 | [Hướng dẫn thao tác (Labs)](docs/08-huong-dan-thao-tac.md) | 6 bài thực hành có các bước bấm cụ thể |
| 09 | [Checklist & Best practices](docs/09-checklist-best-practices.md) | Những lỗi hay mắc và cách tránh |
| 10 | [Tài nguyên tham khảo](docs/10-tai-nguyen-tham-khao.md) | Link docs, API, SDK, học tiếp gì |

---

## Lộ trình học gợi ý

```
            ┌──────────────────────────────┐
  Tuần 1    │ 01 Tổng quan + 02 Thuật ngữ  │   Hiểu bức tranh lớn
            └──────────────┬───────────────┘
                           │
            ┌──────────────▼───────────────┐
  Tuần 2    │ 03 Events & User Profiles    │   Dữ liệu từ đâu ra
            └──────────────┬───────────────┘
                           │
            ┌──────────────▼───────────────┐
  Tuần 3    │ 04 Segmentation + 05 Analytics│  Hiểu người dùng
            └──────────────┬───────────────┘
                           │
            ┌──────────────▼───────────────┐
  Tuần 4    │ 06 Campaigns + 07 Journeys   │   Tác động tới người dùng
            └──────────────┬───────────────┘
                           │
            ┌──────────────▼───────────────┐
  Tuần 5    │ 08 Labs + 09 Best practices  │   Làm thật
            └──────────────────────────────┘
```

**Nguyên tắc vàng khi học CleverTap:** đừng học theo menu, hãy học theo luồng dữ liệu.

```
SDK/API  →  Events + User Properties  →  User Profile
                                            │
                                            ▼
                                        Segments
                                       ╱        ╲
                                      ▼          ▼
                                 Analytics    Campaigns / Journeys
                                      ╲          ╱
                                       ▼        ▼
                                        Reports
```

---

## Trạng thái ghi chép

- [x] Tổng quan nền tảng
- [x] Mô hình dữ liệu (event / profile / segment)
- [x] Segmentation
- [x] Analytics cơ bản
- [x] Campaigns & Journeys
- [x] Labs thao tác
- [ ] Ghi chú thực tế từ project của công ty (cập nhật dần)
- [ ] Tích hợp SDK (Android / iOS / Web) — học ở khóa dành cho developer

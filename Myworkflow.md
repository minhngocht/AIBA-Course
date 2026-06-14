# Quy trình làm việc — Business Analyst công ty E-commerce

> Mô tả luồng làm việc thực tế của em từ lúc nhận requirement đến khi tính năng được nghiệm thu. Mỗi bước nhận **input** từ bước trước và tạo ra **output** làm đầu vào cho bước sau.

---

## Pipeline tổng quan

```
[1] Tiếp nhận & Nghiên cứu
        Requirement → User Story (Jira) → Research đối thủ/solution
        │
        ▼
[2] Đề xuất & Chốt phương án
        Brainstorm → Đề xuất phương án (+ wireframe) → PO duyệt
        │
        ▼
[3] Phân tích & Thiết kế UI
        Phân tích chi tiết + Business Rule → UI/UX design → PO duyệt design
        │
        ▼
[4] Đặc tả (SRS)
        Viết SRS .docx: Mô tả màn hình + Use case (rule + activity diagram)
        │
        ▼
[5] Bàn giao & Phát triển
        Present cho team dev (đầu sprint) → Bổ sung AC vào US trên Jira
        │
        ▼
[6] Kiểm thử & Nghiệm thu
        Review/UAT trên staging → Present stakeholder các tính năng UAT done
```

**Đọc mũi tên:** 1 → 6

---

## Chi tiết từng bước

| # | Bước | Input (từ bước trước) | Việc tôi làm | Output |
|---|------|------------------------|--------------|--------|
| 1 | **Tiếp nhận requirement** | Requirement từ sếp | Tạo User Story trên Jira để quản lý | US trên Jira |
| 2 | **Research** | US đã tạo | Nghiên cứu đối thủ & các solution đang có trên thị trường | Insight + tư liệu tham khảo |
| 3 | **Brainstorm & Đề xuất** | Kết quả research | Brainstorm hướng giải quyết, gửi sếp + đề xuất các phương án khả thi (kèm **wireframe** nếu cần) | Bản đề xuất phương án + wireframe |
| 4 | **PO duyệt phương án** | Bản đề xuất | PO (sếp) xem & chốt hướng đi | Phương án được duyệt |
| 5 | **Phân tích chi tiết** | Phương án đã duyệt | Phân tích sâu, xác định **business rule** | Rule + luồng nghiệp vụ |
| 6 | **Thiết kế UI/UX** | Rule + wireframe | Chuyển UI/UX Designer dựng bản design → gửi lại PO duyệt | Bản design được PO duyệt |
| 7 | **Viết SRS** | Rule + design đã duyệt | Viết tài liệu đặc tả: **mô tả màn hình** + **use case** (kèm rule + activity diagram) | File **SRS (.docx)** |
| 8 | **Present & Bàn giao** | SRS hoàn chỉnh | Đầu sprint: thuyết trình cho team phát triển; bổ sung **AC (Acceptance Criteria)** vào US trên Jira cho tester | US có đủ AC trên Jira |
| 9 | **Review / UAT** | Tính năng đã build trên staging | Review & UAT khi tính năng lên staging | Tính năng UAT done |
| 10 | **Demo stakeholder** | Tính năng UAT done | Thuyết trình cho stakeholder các tính năng đã hoàn thành | Stakeholder nắm & xác nhận |

---

## Khi nào vẽ gì

| Artifact | Vẽ ở bước | Mục đích |
|----------|-----------|----------|
| **Wireframe** | Bước 3 (đề xuất phương án) | Phác nhanh để PO hình dung luồng & chốt hướng. Vẽ trên **Figma**, gần đây chuyển dần sang **HTML** để click thử được |
| **UI/UX Design** | Bước 6 | Bản thiết kế hoàn chỉnh do Designer dựng, làm cơ sở để viết SRS, gần đây chuyển thử lên Github tạo file .md để dev có thể đưa AI đọc |
| **Activity diagram** | Bước 7 (trong SRS) | Mô tả luồng xử lý của use case (các nhánh quyết định, điều kiện) |

---

## Công cụ & sản phẩm bàn giao

- **Jira** — quản lý User Story & Acceptance Criteria
- **Figma / HTML** — wireframe và prototype
- **SRS (.docx)** — tài liệu đặc tả chính, gồm mô tả màn hình + use case + rule + activity diagram
- **Github .md** - chuyển dần lên Github đưa rule + use case
- **Staging** — môi trường để review/UAT trước khi nghiệm thu

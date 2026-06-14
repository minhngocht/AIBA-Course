# WORKFLOW — Quy trình làm việc qua các skills

> File này map toàn bộ skills trong workspace thành 1 luồng làm việc: **dùng khi nào**, **quan hệ upstream/downstream**, và **các kịch bản end-to-end**. Đọc kèm `CLAUDE.md` (cấu trúc vault) + `.claude/rules/` (luật chi tiết).

---

## 1. Bản đồ tổng (pipeline 7 giai đoạn)

```
[0] RESEARCH        /discover  /deep-research
        │
        ▼
[1] CAPTURE         /brainstorm  /meet  /legacy
        │
        ▼
[2] SPECIFY         /urd → /brd → /prd → /srs
        │                              │
        │                              ├─ diagrams: /sequence /activity /state /erd /usecase-diagram
        │                              ├─ screens:  /wireframe → /figma /prototype
        │                              └─ functions: /usecase
        ▼
[3] INTEGRATE       /api-doc → /api-map        (chỉ feature tích hợp API)
        │
        ▼
[4] DELIVER         /usecase → /userstory → /ac
        │
        ▼
[5] TEST            /test-checklist → /test-cases        (UI/behavior)
                    /api-checklist  → /api-test          (API own + 3rd-party)
        │
        ▼
[6] VALIDATE        /review   /gap
        │
        ▼
[7] SHARE/SHIP      /preview  /dashboard  /export-{html,pdf,docx}  /confluence

   CROSS-CUTTING (chạy bất cứ lúc nào):
     /health  /dashboard  /cr  /update-overview
```

**Quy tắc đọc mũi tên:** `A → B` = B thường cần output của A làm input (upstream → downstream). Soft gate: thiếu upstream vẫn chạy được (warn + proceed), **trừ** `/jira --push` và `/confluence --push` (hard gate on stale).

---

## 2. Giai đoạn 0 — RESEARCH (nghiên cứu trước khi quyết định làm)

| Skill | Dùng khi | Input | Output |
|-------|----------|-------|--------|
| `/discover <keyword>` | Cân nhắc có nên làm 1 feature vào dự án không; cần so sánh đối thủ (English-learning apps) | keyword | `docs/_research/{date}-{keyword}.md` (6 mục: keyword/match dự án/competitor/integration/ROI-UX/next steps) |
| `/deep-research` | Cần report đa nguồn, fact-checked về 1 chủ đề bất kỳ (không chỉ English-app) | câu hỏi | report có citation |

**Quan hệ:** `/discover` grep sẵn URD + brainstorms để tìm feature đã có → output gợi ý `next steps` chỉ thẳng sang `/brainstorm` hoặc `/urd`.

---

## 3. Giai đoạn 1 — CAPTURE (gom ý tưởng / nguồn thô)

| Skill | Dùng khi | Output |
|-------|----------|--------|
| `/brainstorm <idea>` | Có ý tưởng feature, cần clarify purpose/scope/constraints/success trước khi viết requirements | `docs/{feature}/brainstorms/{idea}.md` |
| `/meet <type> <slug>` | Có transcript/notes cuộc họp cần extract decisions/blockers/action items | `docs/meetings/{date}-{type}-{slug}.md` (1 file gộp) |
| `/legacy <source>` | Có docs cũ (docx/pdf/xlsx/md) cần import vào vault v2.0 | docs phân loại + gap report |

**Quan hệ:**
- `/brainstorm` là **gốc** của chuỗi specification — front-load ASCII flow, scenario matrix, exact values (error msg, limit) để URD/PRD/SRS sau này không phải hỏi lại.
- 1 feature có thể có **nhiều** brainstorm ideas (không gộp).
- `/meet` và `/legacy` chạy độc lập, feed context cho mọi giai đoạn sau.

---

## 4. Giai đoạn 2 — SPECIFY (đặc tả nghiệp vụ → kỹ thuật)

### 4.1 Chuỗi requirements (chính)

| Skill | Dùng khi | Upstream | Output |
|-------|----------|----------|--------|
| `/urd <feature>` | Đặc tả **người dùng** cần gì (personas, needs, journeys, success) | brainstorm | `docs/{feature}/urd.md` |
| `/brd <feature>` | Đặc tả **lý do kinh doanh** (stakeholders, objectives SMART, ROI, risks) | URD > brainstorm | `docs/{feature}/brd.md` |
| `/prd <feature>` | Đặc tả **phạm vi sản phẩm** (capabilities P0/P1/P2, flows, release plan) | URD + BRD | `docs/{feature}/prd.md` |
| `/srs <feature>` | **Kỹ thuật hoá** PRD → FR/NFR/BR/Error Matrix + auto-chain flows/erd/screens | PRD | `docs/{feature}/srs/spec.md` + chain |

> **URD và BRD chạy song song** (không bắt URD trước BRD). `/prd` consolidate cả hai. `/srs` mặc định **full-chain** — tự sinh spec → flows → erd → screens (ASCII inline) trong 1 session.

### 4.2 Diagrams (thường do `/srs` gọi, hoặc chạy lẻ để bổ sung)

| Skill | Dùng khi | Output (cố định) |
|-------|----------|------------------|
| `/sequence "<flow>" --feature` | ≥2 actor tương tác theo thời gian (login, payment, webhook) | `srs/flows.md` |
| `/activity "<process>" --feature` | Process nhiều decision/parallel/sub-process (refund, approval) | `srs/flows.md` (cùng file) |
| `/state <entity> --feature` | Entity ≥3 trạng thái + transition (Account, Order) | `srs/states.md` |
| `/erd --feature` | Data model ≥2 entity + relationship | `srs/erd.md` |
| `/usecase-diagram --feature` | High-level scope: actors + use cases trong 1 hình | `usecases/diagram.md` |

> Chọn diagram nào: xem `.claude/rules/diagram-selection.md` — *Time → Sequence, State → State, Process → Activity, Scope → Use Case, Data → ERD*. Mọi diagram skill **skip L3** (chat không render mermaid; review từ file).

### 4.3 Screens / giao diện

| Skill | Dùng khi | Output |
|-------|----------|--------|
| `/wireframe <screen> --feature` | Vẽ ASCII wireframe cho 1 screen (low-fi, default) | fill `ascii-screen/{slug}.md` Mục 1 |
| `/srs-add-screen <feature> <screen>` | Thêm 1 screen mới vào SRS đã có | tạo `ascii-screen/{slug}.md` + cập nhật `_index.md` |
| `/figma <screen> --feature` | Vẽ **thật** lên Figma (hi-fi). Hard gate: ASCII Mục 1 phải có sẵn | Figma URL → `ascii-screen/_index.md` |
| `/prototype <feature>` | HTML clickable multi-screen, navigation thật, design tokens | `html-design/prototype.html` |

> Thứ tự fidelity: **`/wireframe` (ASCII) → `/figma` (Figma) hoặc `/prototype` (HTML)**. Figma + prototype đọc `docs/design.md` (design tokens).

### 4.4 Functions (use cases dạng prose)

| Skill | Dùng khi | Output |
|-------|----------|--------|
| `/usecase <feature>` | Mô tả function dạng business black-box (8 mục prose) | `usecases/uc-{slug}.md` |

> **UC = business black-box** (actor goal + steps + expected result). **KHÔNG** embed sequence/state vào UC — chúng thuộc `srs/flows.md` / `srs/states.md`. UC vừa là output của SPECIFY vừa là input cho `/userstory`.

---

## 5. Giai đoạn 3 — INTEGRATE (chỉ feature tích hợp API bên ngoài)

| Skill | Dùng khi | Upstream | Output |
|-------|----------|----------|--------|
| `/api-doc <source> --feature` | Có tài liệu API đối tác (OpenAPI/PDF/URL) cần tóm tắt theo lăng kính nghiệp vụ | tài liệu đối tác | `integration/api-summary.md` |
| `/api-map --feature` | Cần map field 3 tầng: API ↔ entity hệ thống ↔ UI field; phát hiện gap field | api-summary + srs/erd + screens | `integration/api-map.md` |

**Quan hệ:**
- `/api-map` là input cho `/wireframe` (cột UI field) và nguồn test case cho QC.
- `/api-summary` được `/api-checklist` và `/api-test` đọc để sinh test.
- Quét brainstorms toàn dự án → tìm gap "API có field nhưng chưa có brainstorm/screen tương ứng".

---

## 6. Giai đoạn 4 — DELIVER (backlog-ready)

| Skill | Dùng khi | Upstream | Output |
|-------|----------|----------|--------|
| `/userstory <feature>` | Biến SRS FR/UC/screens thành user stories backlog-ready | SRS (soft gate) + UC + screens | `userstories/_index.md` + `us-{NNN}.md` |
| `/ac <feature>` | Sinh/sửa/review acceptance criteria (Given/When/Then) per story | SRS FR/screens/errors + US | AC inline trong `us-{NNN}.md` |

**Quan hệ:**
- `/userstory` **auto-chain `/ac`** sau khi tạo stories (hỏi Y/n/ids) → sinh AC cùng session.
- AC link ngược về FR + screen + error code → là input cho `/test-*` và `/jira`.
- Lên Jira/Confluence: `/confluence --push` (hard gate refuse nếu doc `status: stale`).

---

## 7. Giai đoạn 5 — TEST (QC / IT-BA)

### 7.1 Hai nhánh song song

```
UI / behavior:   /test-checklist  →  /test-cases
API:             /api-checklist   →  /api-test
```

| Skill | Dùng khi | Upstream | Output |
|-------|----------|----------|--------|
| `/test-checklist <feature>` | Outline scenarios cần test (chưa data) để review TRƯỚC | mọi context feature (brainstorm→SRS→US/UC/screens) | `test/checklist/checklist-*.md` |
| `/test-cases <feature>` | Sinh test case CHI TIẾT 1:1 từ checklist (Step/Action/Expected/Data) | checklist (hard gate) | `test/testcases/` + preview.html |
| `/api-checklist <feature>` | Build checklist test API qua discovery interview (own + 3rd-party) | OpenAPI/SRS/api-summary | `test/api/api-checklist.md` |
| `/api-test <feature>` | Test API kiểu Postman, sinh Bruno collection chạy được, ghi PASS/FAIL ngược bảng | api-checklist (ưu tiên) > api-summary | `test/api/api-tests.md` + `bruno/` |

**Quan hệ quan trọng:**
- **Checklist trước, cases/test sau** (hard gate). Checklist để review scope rẻ trước khi viết detail đắt.
- `/test-checklist` (story scope) có thể chain xuống `/ac`; (integration scope) chain xuống `/api-test`.
- `/api-test` đọc `/api-checklist` để expand 1:1; secret chỉ ở `bruno/.env` (gitignored), AI không đọc.

---

## 8. Giai đoạn 6 — VALIDATE (kiểm tra chất lượng + truy vết)

| Skill | Dùng khi | Output |
|-------|----------|--------|
| `/review <file>` | Multi-agent review 1 doc (URD/BRD/PRD/SRS/US/AC/wireframe/UC) | findings + cập nhật status + changelog |
| `/gap <feature>` | Traceability matrix + phát hiện gap/conflict cross toàn bộ docs 1 feature | `docs/_shared/traceability.md` |

**Quan hệ:**
- `/review` đọc `type:` frontmatter → đề xuất agent set (vd SRS spec → senior-ba + tech + qa; wireframe → uxui). Aggregate findings (2+ agent WARNING → escalate BLOCKING).
- `/gap` phát hiện orphaned FR/US/AC, missing wireframe, link asymmetry, stale chain.
- Trigger tự nhiên: status `→ in-review` gợi ý `/review`; `→ approved`/`shipped` gợi ý `/gap`.
- `/cr` **auto-run `/gap`** sau khi apply change.

---

## 9. Giai đoạn 7 — SHARE / SHIP (đưa cho stakeholder)

| Skill | Dùng khi | Output |
|-------|----------|--------|
| `/preview <feature>` | Xem nhanh 1 feature dạng HTML viewer (TOC + mermaid zoom), local file:// | `docs/{feature}/preview.html` |
| `/dashboard [feature]` | Visual tracking: kanban + charts + action items + stale tree | `docs/_shared/dashboard.html` |
| `/export-html <feature>` | Package HTML self-contained gửi stakeholder | `docs/exports/...package.html` |
| `/export-pdf <feature>` | Package PDF (mermaid → PNG, font tiếng Việt) | `...package.pdf` |
| `/export-docx <feature>` | Package Word editable | `...package.docx` |
| `/confluence <feature>` | Preview/push lên Confluence (sub-pages, mermaid macro/PNG) | Confluence pages |

> Export cần tool: `mmdc` + `pandoc` (+ Chrome cho PDF). `/confluence --push` hard gate on stale; auto-chain từ `/jira --push`.

---

## 10. CROSS-CUTTING (chạy bất cứ lúc nào)

| Skill | Dùng khi |
|-------|----------|
| `/health` | Check nhanh 5-7 dòng "việc gì cần làm tiếp" trước khi bắt đầu |
| `/dashboard` | Visual deep view toàn workspace (xem giai đoạn 7) |
| `/cr "<change>"` | Áp 1 change request lên docs đã có: detect feature → analyze impact → L2 diff apply → auto `/gap`. Luôn tạo CR record cho audit |
| `/update-overview <target>` | Quản lý 5 shared docs project-level (`docs/_shared/`: definitions/env/conventions/system/patterns) |

---

## 11. Kịch bản end-to-end (copy-paste được)

### Kịch bản A — Feature mới từ con số 0 (nghiệp vụ thuần)
```
/discover dark-mode               # (tùy chọn) nghiên cứu trước
/brainstorm <ý tưởng dark mode>   # gom ý + clarify
/urd dark-mode                    # user needs
/brd dark-mode                    # business case (song song urd)
/prd dark-mode                    # scope P0/P1/P2
/srs dark-mode                    # full-chain: spec + flows + erd + screens
/wireframe settings-screen --feature dark-mode
/usecase dark-mode                # functions prose
/userstory dark-mode              # → auto-chain /ac
/review docs/dark-mode/srs/spec.md
/gap dark-mode
/export-pdf dark-mode             # gửi stakeholder
```

### Kịch bản B — Feature tích hợp API đối tác (vd cổng thanh toán)
```
/api-doc paygate-spec.yaml --feature payment   # tóm tắt API nghiệp vụ
/brainstorm <thanh toán qua PayGate>
/urd payment → /brd payment → /prd payment → /srs payment
/api-map --feature payment        # map field API ↔ entity ↔ UI
/wireframe checkout --feature payment   # dùng cột UI field từ api-map
/userstory payment → /ac payment
/api-checklist payment → /api-test payment --run   # test integration
/gap payment
```

### Kịch bản C — Pha test (QC vào sau khi có SRS/US)
```
/test-checklist payment           # outline review trước
/test-cases payment               # detail 1:1 từ checklist
/api-checklist payment            # (nếu có API)
/api-test payment --run
```

### Kịch bản D — Có thay đổi sau khi đã approve
```
/cr "thêm yêu cầu OTP khi thanh toán > 5tr"   # detect feature, analyze impact
   → L2 diff apply từng file
   → auto /gap
/review docs/payment/srs/spec.md  # nếu thay đổi material
/export-pdf payment               # regen package
```

### Kịch bản E — Bắt đầu phiên làm việc hằng ngày
```
/health                           # 1-liner: việc gì cần làm
/dashboard                        # visual nếu cần xem sâu
```

---

## 12. Bảng tra "Tôi đang ở đâu → chạy gì tiếp"

| Tôi vừa có... | Chạy tiếp |
|---------------|-----------|
| Ý tưởng thô | `/brainstorm` |
| Brainstorm xong | `/urd` + `/brd` |
| URD + BRD xong | `/prd` |
| PRD xong | `/srs` (full-chain) |
| SRS spec xong, thiếu diagram | `/sequence` `/activity` `/state` `/erd` `/usecase-diagram` |
| SRS xong, cần màn hình | `/wireframe` → `/figma` / `/prototype` |
| SRS xong, cần functions | `/usecase` |
| Có tài liệu API đối tác | `/api-doc` → `/api-map` |
| SRS approved | `/userstory` → `/ac` |
| US + AC xong | `/test-checklist` → `/test-cases` (và `/api-checklist` → `/api-test` nếu có API) |
| Doc cần review | `/review` |
| Cần check truy vết/gap | `/gap` |
| Cần gửi stakeholder | `/preview` / `/export-*` / `/confluence` |
| Có yêu cầu thay đổi | `/cr` |
| Không biết làm gì tiếp | `/health` → `/dashboard` |

---

## 13. Nguyên tắc xuyên suốt (rút gọn từ rules)

1. **Approval gate**: L3 (iterate nếu sáng tạo) → L1 (plan Y/n) → L2 (diff nếu update) → Write. Không skill nào tự ghi file im lặng.
2. **Soft gate mặc định**: thiếu upstream → warn + proceed. **Hard gate** chỉ ở `/jira --push` và `/confluence --push` (refuse on stale).
3. **No re-ask**: không hỏi lại câu user đã trả lời (scan context + file đã có).
4. **IT-BA framing**: skill nghiệp vụ hỏi business language, KHÔNG hỏi DB/schema/endpoint/SDK (trừ `/api-*` đọc/test API là việc BA/QC).
5. **Resolve OQs (Phase E)**: sau mỗi Write doc, resolve open questions + cascade scan downstream trước khi qua skill tiếp.
6. **Changelog routing**: file phụ trợ route changelog lên review-unit file cha (xem `.claude/rules/changelog.md`).
7. **Diagram skip L3**: chat không render mermaid → write thẳng, review từ file rendered.

---

*Cập nhật bản đồ này khi thêm/sửa skill. Nguồn chân lý chi tiết: `.claude/skills/*/SKILL.md` + `.claude/rules/*.md`.*

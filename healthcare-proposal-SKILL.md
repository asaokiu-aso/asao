---
name: healthcare-proposal
description: >
  Creates polished new business proposal documents for healthcare ventures.
  Use this skill whenever the user asks to create, draft, or generate a new business
  proposal, business plan, pitch deck, or proposal document in the healthcare or
  medical domain. Triggers include: "ヘルスケア 提案書", "医療 新規事業", "healthcare proposal",
  "新規事業提案書", "ヘルスケア事業計画", or any request to make a business proposal
  for a health-related product or service. Always use this skill when the user
  mentions healthcare AND proposal/plan/pitch in the same request — even if phrased
  informally like "ヘルスケアの事業提案まとめて" or "医療系のビジネスプラン作って".
  Default output: PowerPoint (.pptx). Supports Word (.docx) or inline chat display
  if the user requests. Accepts uploaded sales data / market research files or
  generates with built-in sample data if none provided.
---

# Healthcare New Business Proposal Skill

Generates a complete, professional new business proposal for a healthcare venture.
Default output is a PowerPoint deck. Word (.docx) and inline chat are also supported.

---

## Step 0 — Gather Inputs (ask once, upfront)

Before generating anything, ask the user **two questions** using `ask_user_input_v0`:

1. **Data source** — Do you have sales data / market research files to upload, or should I use sample data?
   - Options: `ファイルをアップロードする` / `サンプルデータで作成する`

2. **Output format** — Which format do you want?
   - Options: `PowerPoint (.pptx)` / `Word文書 (.docx)` / `チャット内で表示`

If the user has already answered either question in the conversation, skip asking it again.

---

## Step 1 — Collect Context (if files uploaded)

Read the pptx skill before doing anything else:
```
view /mnt/skills/public/pptx/SKILL.md
view /mnt/skills/public/pptx/pptxgenjs.md   ← required for creating from scratch
```

If files were uploaded, check `/mnt/user-data/uploads/` and extract key figures:
- Revenue figures by year / product line
- Market size, CAGR, target segments
- Competitive landscape data
- Any KPIs or financial projections

Replace the sample numbers in the template below with real figures wherever available.

---

## Step 2 — Proposal Structure (Standard 7-Chapter)

Use this structure for every healthcare proposal. Do not skip chapters.

| # | Chapter | Key content |
|---|---------|-------------|
| 1 | Executive Summary | 4 KPI metrics, background table |
| 2 | Market Environment | Market size table, competitive positioning matrix |
| 3 | Business Concept | Value proposition per customer segment, tech architecture bullets |
| 4 | Revenue Model & Financials | Pricing table, 3-year P&L, KPI targets |
| 5 | Execution Roadmap | 4-phase table (Phase 1–4) |
| 6 | Risk Analysis | Risk × severity × probability × mitigation table |
| 7 | Approval Request | Budget breakdown table, decision schedule, approval sign-off box |

---

## Step 3 — Output by Format

### PowerPoint (default)

Read `/mnt/skills/public/pptx/SKILL.md` and `/mnt/skills/public/pptx/pptxgenjs.md` first.

**Color palette — Healthcare "Teal Trust":**
- Primary:   `028090` (teal)
- Accent:    `02C39A` (mint)
- Dark bg:   `0A2E36` (deep teal, for title/closing slides)
- Light bg:  `F0F9FA` (light teal, for content slides)
- Text:      `1A1A1A`
- Muted:     `6B7280`

**Slide map (minimum 12 slides):**

| Slide | Type | Layout |
|-------|------|--------|
| 1 | Cover | Dark bg, large title, subtitle, date/department |
| 2 | Agenda / TOC | Clean numbered list |
| 3 | Executive Summary | 4 KPI stat callouts (big numbers) |
| 4 | Market Background — data table | Two-column: text left, table right |
| 5 | Market Environment — sizing | Big market number + segment breakdown |
| 6 | Competitive Positioning | 2×2 or bubble chart description as table |
| 7 | Business Concept Overview | Icon + text rows for each customer segment |
| 8 | Value by Segment (detail) | 2-column table |
| 9 | Revenue Model | Pricing table |
| 10 | 3-Year P&L | Financial table with colored header |
| 11 | Execution Roadmap | 4-column phase table |
| 12 | Risk Analysis | Risk table |
| 13 | Approval Request | Budget table + sign-off area |
| 14 | Closing | Dark bg, call to action text |

**Every slide must have:**
- A visual element (shape, table, large stat, or icon rectangle) — no text-only slides
- Left-aligned body text (center only slide titles)
- 0.5" minimum margins

**Run QA after generation:**
```bash
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
rm -f slide-*.jpg && pdftoppm -jpeg -r 150 output.pdf slide
# Inspect slide images for overflow, overlap, and missing content
extract-text output.pptx | grep -iE "lorem|ipsum|TODO|\[insert"
```

Fix overflow, overlap, and missing content. Stop after one fix cycle.

Copy final file to `/mnt/user-data/outputs/` and call `present_files`.

---

### Word (.docx)

Read `/mnt/skills/public/docx/SKILL.md` first.

Use the same 7-chapter structure. Include:
- Header with document title + "機密資料" label
- Footer with page numbers (current / total)
- KPI summary table after Executive Summary intro paragraph
- Data tables for market analysis, financials, risk, and approval
- Approval sign-off table at end (3 columns: 事業企画部長 / 担当役員 / 代表取締役)
- Teal (`028090`) for heading colors and table header backgrounds

Validate after generation:
```bash
python scripts/office/validate.py output.docx
```

Copy to `/mnt/user-data/outputs/` and call `present_files`.

---

### Inline chat display

Use `visualize:read_me` (modules: `chart`, `data_viz`) then `visualize:show_widget`.

Build an interactive HTML widget with:
- Cover header (title, subtitle, date)
- Metric cards (4 KPIs)
- Bar/line charts using Chart.js (CDN: `cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js`)
- Feature comparison tables
- Roadmap grid
- Risk list with severity dots
- Approval budget table
- CTA buttons (Word出力 / PowerPoint出力 / 競合分析を深掘り) wired with `sendPrompt()`

---

## Sample Data (use when no files provided)

### Market figures
| Item | Value |
|------|-------|
| 国内市場規模（2025） | 4.8兆円 |
| CAGR（2025–2030） | 18.3% |
| 初期投資額 | 2.4億円 |
| 黒字化目標 | 3年目（ARR 8億円） |

### Revenue history (sample)
| Year | 健診サービス | デジタルヘルス |
|------|-------------|---------------|
| 2022 | 42億円 | 8億円 |
| 2023 | 44億円 | 14億円 |
| 2024 | 43億円 | 23億円 |
| 2025（見込） | 45億円 | 38億円 |

### 3-Year P&L (sample)
| Item | Year 1 | Year 2 | Year 3 |
|------|--------|--------|--------|
| 売上高 | 1.2億 | 4.2億 | 8.0億 |
| 売上原価 | 0.5億 | 1.4億 | 2.4億 |
| 粗利益 | 0.7億 | 2.8億 | 5.6億 |
| 販管費 | 2.6億 | 2.6億 | 2.9億 |
| 営業利益 | ▲1.9億 | 0.2億 | 2.7億 |

### Pricing model
| Plan | Customer | Price |
|------|----------|-------|
| Enterprise | 大企業（1,000名以上） | @400円/人/月 |
| Business | 中堅企業（100〜999名） | @500円/人/月 |
| Personal | 個人 | @980円/人/月 |
| Medical | 医療機関 | @30万円/施設/月 |
| Data API | 保険会社 | 個別見積 |

### Roadmap phases
| Phase | Period | Milestones |
|-------|--------|------------|
| 1 開発・実証 | 2025 Q3–Q4 | MVP開発、パイロット3社、薬機法対応 |
| 2 市場投入 | 2026 Q1–Q2 | 正式ローンチ、導入30社、アプリ公開 |
| 3 スケール | 2026 Q3–2027 Q2 | 医療機関展開、保険会社提携、ARR 8億円 |
| 4 拡張 | 2027 Q3〜 | 海外展開、遺伝子連携、IPO/M&A検討 |

### Risks
| Risk | Severity | Probability | Mitigation |
|------|----------|-------------|------------|
| 規制・薬機法 | 高 | 中 | 規制当局へ事前相談、顧問医師登用 |
| データ品質 | 中 | 中 | 欠損補完アルゴリズム、複数デバイス対応 |
| 競合参入 | 中 | 高 | 健保組合と独占契約、健診DBロックイン |
| 継続率低下 | 中 | 高 | 健診連携の強制接点設計、ゲーミフィケーション |
| 採用難 | 高 | 中 | 大学共同研究、リモート採用拡大 |

---

## Notes

- Always add a sample-data disclaimer when using built-in figures: "※ サンプルデータです。実際のデータに差し替えてご使用ください。"
- Service name default: **「HealthLoop」** — change if the user specifies another name
- If the user uploads files but the format is unclear, read `/mnt/skills/public/file-reading/SKILL.md` first

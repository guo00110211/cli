# Planning Layer

新建演示文稿或大幅改写页面时，必须先写 `.lark-slides/plan/<deck-or-task-id>/slide_plan.json`，再生成 XML。这个文件是 deck 的设计中间层，用来把叙事、页面角色、布局、视觉重点和文字密度固定下来，避免从用户提示直接跳到 XML。

小型已有页编辑可豁免，例如只替换一个标题、改一个数字、插入一个块、上传并插入一张图。只要任务会重排多页、生成新 deck、替换整页结构，仍然需要规划层。

## Required Flow

1. 理解用户需求，必要时澄清主题、受众、页数、风格。
2. 如果适合模板，先用 `template_tool.py search` 检索，锁定模板后用 `summarize` 获取主题和页型信息。
3. 选择唯一 plan 目录：`.lark-slides/plan/<deck-or-task-id>/`。
4. 先创建目录：`mkdir -p .lark-slides/plan/<deck-or-task-id>`。
5. 写入 `.lark-slides/plan/<deck-or-task-id>/slide_plan.json`。
6. 读取 `xml-schema-quick-ref.md`、`visual-planning.md` 和 `asset-planning.md`。
7. 按 plan、visual planning 和 asset planning 规则逐页生成 XML，把 `layout_type`、`visual_focus`、`text_density` 转成具体页面几何和文本量约束，并把缺失素材转成可执行兜底视觉。
8. 创建 PPT 后用 `xml_presentations.get` 回读，核对页面数量、关键元素和 plan 到 XML 的对应关系。

模板不能代替 plan。模板搜索和摘要只能影响 `theme_style`、页面流、布局选择和局部布局骨架；最终仍必须有 `.lark-slides/plan/<deck-or-task-id>/slide_plan.json`。

## Plan Path

Use a separate plan directory per deck or task so multiple presentations in the same workspace cannot overwrite each other.

Recommended IDs:

- New deck before creation: title slug plus date/time, such as `q3-review-20260507-1805`.
- Existing PPT rewrite: the `xml_presentation_id`.
- Ambiguous or untitled task: short task slug plus date/time.

Rules:

- Do not reuse `.lark-slides/plan/slide_plan.json` as a shared path.
- Create the directory before writing the file.
- Reuse the same plan path for XML generation and post-create verification for that deck.

## JSON Shape

```json
{
  "presentation_goal": "Explain the proposal and secure approval for the next phase.",
  "audience": "Product and engineering leaders who know the domain but need a concise decision narrative.",
  "theme_style": "Clean business style, light background, restrained blue accent, strong visual hierarchy.",
  "slides": [
    {
      "page": 1,
      "title": "Proposal Title",
      "key_message": "The initiative is ready for a focused pilot.",
      "layout_type": "title-cover",
      "visual_focus": "Large title area with one concise supporting statement.",
      "asset_need": {
        "asset_type": "logo",
        "purpose": "Signal product or team identity on the opening page.",
        "suggested_query": "product logo",
        "fallback_if_missing": "Use a small text badge and abstract shape motif instead of a real logo."
      },
      "text_density": "low",
      "speaker_intent": "Frame the decision and establish the deck's point of view."
    }
  ]
}
```

## Required Fields

Top-level fields:

- `presentation_goal`: what the whole deck is trying to achieve.
- `audience`: target readers or listeners and their assumed background.
- `theme_style`: visual tone, palette direction, and professional style.
- `slides`: ordered page plans.

Each slide must include:

- `page`: 1-based page number.
- `title`: slide title.
- `key_message`: the one idea this page must land.
- `layout_type`: planned page structure.
- `visual_focus`: dominant visual object or region.
- `asset_need`: planning-only structured asset metadata; no search, download, or upload required. Follow `asset-planning.md`.
- `text_density`: `low`, `medium`, or `high`.
- `speaker_intent`: why the speaker needs this page and how it advances the story.

## Layout Vocabulary

Use one of these `layout_type` values unless the user explicitly needs a custom structure:

- `title-cover`
- `section-divider`
- `two-column`
- `image-left-text-right`
- `image-right-text-left`
- `big-number`
- `timeline`
- `comparison`
- `architecture-diagram`
- `process-flow`
- `quote-highlight`
- `conclusion`

The value must affect XML geometry, not just appear as a label. For example, `timeline` should create a horizontal or vertical sequence, `comparison` should create distinct side-by-side regions, and `big-number` should reserve dominant space for a large metric.

## Text Density Rules

- `low`: title plus 1 short statement, or 1-3 very short labels.
- `medium`: title plus 2-4 concise bullets or labeled regions.
- `high`: allowed only when the user needs detail; use tables, columns, or grouped regions instead of a long bullet list.

Do not let all pages become title + bullet slides. For decks of 4 or more pages, aim for at least 4 different `layout_type` values when the content allows it.

## Asset Planning

`asset_need` is metadata. It can describe a desired figure, diagram, chart, icon, logo, screenshot, or fallback shape-based visual, but it must not require web search, local download, or media upload.

Use an object for one planned asset, an array for multiple real needs, or `asset_type: "none"` when no asset is useful. Each planned asset must include:

- `asset_type`: one of `paper_figure`, `architecture_diagram`, `icon`, `logo`, `chart`, `infographic`, `screenshot`, `flow_diagram`, or `none`.
- `purpose`: why this asset helps the page's key message.
- `suggested_query`: short future lookup hint only; do not execute it unless separately requested.
- `fallback_if_missing`: concrete XML-native visual plan using shapes, arrows, labels, tables, simple charts, or placeholder panels.

For detailed rules and examples, read `asset-planning.md`.

Good examples:

- `{"asset_type":"architecture_diagram","purpose":"Explain component relationships.","suggested_query":"service architecture diagram","fallback_if_missing":"Draw grouped boxes and arrows with short labels."}`
- `{"asset_type":"logo","purpose":"Identify the customer context.","suggested_query":"customer logo","fallback_if_missing":"Use a text label in a small badge."}`
- `{"asset_type":"chart","purpose":"Show adoption trend.","suggested_query":"monthly adoption trend chart","fallback_if_missing":"Draw a simple line chart with shapes and value labels."}`

## XML Generation Contract

Before writing each slide XML, map the plan fields to concrete decisions:

- `key_message` determines the headline, dominant claim, or main takeaway.
- `layout_type` determines the coordinate structure and element types. Use `visual-planning.md` for concrete layout rules.
- `visual_focus` determines the largest visual region or emphasized object.
- `text_density` caps visible text volume.
- `asset_need` informs placeholder diagrams, icons, charts, screenshots, or shape-based fallback visuals only. Missing real assets must use `fallback_if_missing`, not blank regions.

After creating the PPT, fetch the presentation and verify:

- Page count matches the plan.
- Every page has the planned title and key message represented.
- At least several pages have visibly different XML layout structures.
- Planned `visual_focus` appears as a dominant visual region or object.
- At least 3 pages actively plan `asset_need` when the deck topic allows, and each planned asset has a visible fallback if no real asset was used.
- `text_density` is reflected in the amount of visible text.
- Pages are not crowded, and any planned `timeline`, `comparison`, or `architecture-diagram` page uses its matching visual structure.

# Planning Layer

新建演示文稿或大幅改写页面时，必须先写 `.lark-slides/plan/<deck-or-task-id>/slide_plan.json`，再生成 XML。这个文件是 deck 的设计中间层，用来把叙事、页面角色、布局、视觉重点和文字密度固定下来，避免从用户提示直接跳到 XML。

小型已有页编辑可豁免，例如只替换一个标题、改一个数字、插入一个块、上传并插入一张图。只要任务会重排多页、生成新 deck、替换整页结构，仍然需要规划层。

## Required Flow

1. 理解用户需求，必要时澄清主题、受众、页数、风格。
2. 如果适合模板，先用 `template_tool.py search` 检索，锁定模板后用 `summarize` 获取主题和页型信息。
3. 选择唯一 plan 目录：`.lark-slides/plan/<deck-or-task-id>/`。
4. 先创建目录：`mkdir -p .lark-slides/plan/<deck-or-task-id>`。
5. 写入 `.lark-slides/plan/<deck-or-task-id>/slide_plan.json`。
6. 读取 `xml-schema-quick-ref.md`，按 plan 逐页生成 XML。
7. 创建 PPT 后用 `xml_presentations.get` 回读，核对页面数量、关键元素和 plan 到 XML 的对应关系。

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
      "asset_need": "Planning only: optional product logo or abstract shape-based motif.",
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
- `asset_need`: planning-only asset need; no search, download, or upload required in Phase 1.
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

`asset_need` is metadata in this phase. It can describe a desired figure, diagram, chart, icon, logo, screenshot, or fallback shape-based visual, but it must not require web search, local download, or media upload.

Good examples:

- `Planning only: simplified architecture diagram with boxes and arrows; fallback to shape-based diagram.`
- `Planning only: optional customer logo; fallback to text label in a small badge.`
- `Planning only: chart-like visual for adoption trend; fallback to simple line made from shapes.`

## XML Generation Contract

Before writing each slide XML, map the plan fields to concrete decisions:

- `key_message` determines the headline, dominant claim, or main takeaway.
- `layout_type` determines the coordinate structure and element types.
- `visual_focus` determines the largest visual region or emphasized object.
- `text_density` caps visible text volume.
- `asset_need` informs placeholder diagrams, icons, charts, or shape-based fallback visuals only.

After creating the PPT, fetch the presentation and verify:

- Page count matches the plan.
- Every page has the planned title and key message represented.
- At least several pages have visibly different XML layout structures.
- Planned `visual_focus` appears as a dominant visual region or object.
- `text_density` is reflected in the amount of visible text.

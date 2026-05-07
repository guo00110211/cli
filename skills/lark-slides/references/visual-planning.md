# Visual Planning

新建演示文稿或大幅改写页面时，在 `slide_plan.json` 完成后、生成 XML 前读取本文件。目标是让 `layout_type`、`visual_focus`、`text_density` 变成实际页面几何，而不是只写在 plan 里。

默认画布按 `960 x 540` 规划。模板 XML 可以覆盖具体坐标，但不能覆盖这些原则：页面要有主视觉区域、文本要受密度约束、不同 `layout_type` 必须产生明显不同的坐标结构。

## Core Rules

- `layout_type` must change geometry: element positions, region sizes, alignment, and visual rhythm must differ across page types.
- `visual_focus` determines the largest or highest-contrast region. It can be an image, diagram, metric, quote, table, or shape-based placeholder.
- `text_density` caps visible text:
  - `low`: title plus one short statement, or 1-3 labels.
  - `medium`: title plus 2-4 concise bullets or labeled regions.
  - `high`: use a table, columns, grouped labels, or annotations. Do not use one long bullet box.
- Do not create a deck where every content page is title plus bullets. For 4 or more pages, use at least 4 different layout structures when the content allows.
- Keep generous margins. Use `60-80` px outer margins on standard content pages unless a full-bleed image or cover treatment is intentional.
- Reserve vertical space for titles. A typical content title area is `y=36..90`; main content should usually start at `y>=110`.
- Avoid crowding the bottom edge. Keep non-background content above `y=500` unless it is a footer.
- Prefer fewer, larger objects over many small text boxes.

## Layout Types

### `title-cover`

Purpose: introduce the deck's point of view.

Geometry:
- Use one dominant title block, usually `x=70..120`, `y=150..250`, `width=700..820`.
- Add one subtitle or context line, not a bullet list.
- Optional visual focus can be a full-bleed background, large side image, accent band, or abstract shape motif.

Text:
- `low` only unless the user explicitly asks for detail.

### `section-divider`

Purpose: reset rhythm and mark a new chapter.

Geometry:
- Use a large section number, chapter label, or single centered claim.
- Keep the page sparse. A divider is not a content page.
- Visual focus can be one oversized number, a vertical accent bar, or a full-width band.

Text:
- Title plus one phrase. No bullets.

### `two-column`

Purpose: compare two related ideas or pair explanation with evidence.

Geometry:
- Split main region into two balanced columns, for example left `x=60,width=400`, right `x=500,width=400`.
- Each column needs its own heading or visual anchor.
- Do not place one full-width bullet box under a normal title; that is not a two-column layout.

Text:
- `medium`: 2-3 short items per column.
- `high`: use grouped rows or mini table structure inside columns.

### `image-left-text-right`

Purpose: let a visual establish context, with text explaining implication.

Geometry:
- Left visual region should occupy roughly `35-45%` of slide width, often full height or tall crop.
- Right text region starts around `x=420` and should have a strong headline plus short support.
- If no real image is available, create a shape-based placeholder visual that matches `asset_need`.

Text:
- Keep right-side text short. Avoid more than 4 bullets.

### `image-right-text-left`

Purpose: lead with a message, then reinforce it with a visual.

Geometry:
- Left text region starts around `x=60..90`, width `400..460`.
- Right visual region occupies roughly `35-45%` of slide width.
- Align the image or placeholder with the main text block, not only with the title.

Text:
- Use one main claim and 2-3 supporting points.

### `big-number`

Purpose: make one metric or fact memorable.

Geometry:
- Reserve the largest object for the metric: font size often `64-110`, region at least `300 x 120`.
- Pair the number with one explanation and optional 2-3 small supporting labels.
- Do not bury the number in a bullet list or small card.

Text:
- `low` or `medium`. If detail is needed, add small annotations around the metric.

### `timeline`

Purpose: show sequence, roadmap, history, or phases.

Geometry:
- Create a horizontal or vertical spine with 3-6 milestones.
- Each milestone should have a dot/card/date label connected by a line or arrow.
- Title is separate from the sequence. The sequence is the visual focus.

Text:
- Each milestone gets a short label and optional one-line explanation.
- Do not use paragraph-length milestone descriptions.

### `comparison`

Purpose: make a choice, before/after, old/new, or option tradeoff clear.

Geometry:
- Use two or three distinct panels, columns, or a table-like structure.
- Headings must be visually aligned so differences are easy to scan.
- Use color, border, icon, or label treatment to highlight the preferred option or key difference.

Text:
- Use parallel wording across columns.
- Avoid uneven long bullet lists that destroy comparability.

### `architecture-diagram`

Purpose: explain components, dependencies, or system flow.

Geometry:
- Main visual area should be a diagram, not prose.
- Use grouped boxes, lanes, arrows or lines, and short labels.
- Keep diagram labels concise. Put explanation in notes or a small side caption if needed.

Text:
- Prefer labels of 1-5 words.
- Use no more than one short explanatory text block.

### `process-flow`

Purpose: show operational steps, workflow, or cause-effect path.

Geometry:
- Use numbered steps connected by arrows or lines.
- 3-5 steps is ideal for one slide. If there are more, group them into phases.
- The flow direction must be visually obvious.

Text:
- Each step gets a verb-led label and one short descriptor at most.

### `quote-highlight`

Purpose: emphasize a customer voice, principle, thesis, or decision statement.

Geometry:
- Quote or claim is the dominant text object.
- Use large type, generous whitespace, and optional attribution or context badge.
- Do not combine a quote-highlight page with a normal bullet section.

Text:
- One quote or statement, plus optional attribution. No bullets.

### `conclusion`

Purpose: close with decision, recommendation, or next action.

Geometry:
- Use one dominant closing statement or call to action.
- Add up to 3 next-step cards, checklist items, or owner/date labels.
- Visual focus should be the recommendation or action, not decorative filler.

Text:
- Keep the final page easy to remember. Avoid recap overload.

## Plan To XML Checklist

Before creating XML for each page, answer these checks:

1. Which region is the visual focus, and is it the largest or most prominent object?
2. Does the XML geometry match the `layout_type` description above?
3. Does `text_density` limit the number of paragraphs, bullets, labels, and text boxes?
4. Would this page still be recognizable if the `layout_type` label were removed from the plan?
5. Across the deck, do multiple pages use genuinely different structures?

After fetching the created presentation, verify:

- Use `timeline`, `comparison`, and `architecture-diagram` only when the content calls for them; do not force irrelevant page types.
- Any planned `timeline`, `comparison`, or `architecture-diagram` page uses the matching sequence, side-by-side comparison, or component-and-connection structure.
- Pages are not crowded and do not rely on long bullet boxes.
- Main claim, supporting detail, and visual focus have clear hierarchy.

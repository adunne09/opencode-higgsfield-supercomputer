---
name: infographic
description: |
  Renders data visualizations and infographics inline in chat as live React components.
  Agent writes JSX inside ```jsx_preview``` fenced code blocks in normal text responses.
  Frontend renders them via JSXPreview from @ai-sdk/elements with a pre-loaded component library.
  Use when user asks to visualize, chart, compare, summarize stats, or create a dashboard view.
---
# Infographic Skill

Embed live infographics in your text response using `jsx_preview` fenced code blocks. The frontend renders them as interactive React components — not code blocks.

## Output Format

Write raw JSX fragments inside triple-backtick fences with the `jsx_preview` language tag. No imports, no `export default`, no module syntax. Just JSX.

Rules:
- Raw JSX only — no `import`, no `export`, no `function Component()`
- Use component names from the library below (they are pre-loaded in scope)
- Tailwind classes for all layout and styling
- Use the design token CSS variables listed below — never hardcode colors
- All data inline in JSX props — no external fetches, no `useState`, no hooks
- Multiple `jsx_preview` blocks in one message are fine — each renders independently
- Keep blocks focused: one visualization per block, not entire dashboards
- Surround blocks with normal markdown text for context and insights

## Design Tokens (CSS Variables)

Use these via `var(--token-name)` in `style` props or Tailwind arbitrary values.

### Surfaces
- `--color-surface-primary` — Card / container backgrounds
- `--color-surface-tertiary` — Subtle background, secondary containers
- `--color-neutral-surface` — Tooltip / popover surfaces
- `--color-neutral-surface-subtle` — Hover states, muted fills
- `--color-button-secondary` — Muted button / pill backgrounds

### Brand
- `--color-surface-brand` (#4FCEE4) — Primary brand accent
- `--color-surface-brand-alpha-1` — Brand tint backgrounds
- `--color-surface-brand-alpha-2` — Stronger brand tint
- `--color-surface-brand-secondary` (#FF005B) — Secondary accent
- `--color-surface-brand-secondary-alpha-1` — Secondary accent tint

### State
- `--color-surface-success` (#53C546) — Positive trends
- `--color-surface-success-alpha-1` — Success tint
- `--color-surface-error` (#E72930) — Negative trends, errors
- `--color-surface-error-alpha-1` — Error tint
- `--color-surface-warning` (#FFBE4C) — Caution, in-progress

### Typography
- `--color-font-primary` — Headings, primary text
- `--color-font-primary-reverted` — Text on dark/brand backgrounds
- `--color-font-secondary` — Labels, captions, muted text
- `--color-font-brand` — Brand-colored text
- `--color-font-error` — Error text, negative trend values
- `--color-font-success` — Success text, positive trend values
- `--color-font-warning` — Warning text
- `--color-font-disabled` — Disabled / inactive text

### Dividers
- `--color-divider-primary` — Primary divider lines
- `--color-separator-card` — Card borders
- `--color-separator-brand` — Brand-colored borders

## Component Library

### BarChart
Vertical or horizontal bar chart.
Props: `data` ({label, value}[]), `color`, `horizontal` (false), `showValues` (true), `height` (200)

### LineChart
Trend line with optional smoothing.
Props: `data`, `color`, `smooth` (false), `showDots` (true), `showArea` (false), `height` (200)

### PieChart
Proportional breakdown as a donut/pie.
Props: `data` ({label, value, color?}[]), `donut` (true), `showLabels` (true), `showPercent` (true), `size` (200)

### AreaChart
Filled area trend chart.
Props: `data`, `color`, `gradient` (true), `smooth` (true), `height` (200)

### StatCard
Single metric with optional trend indicator.
Props: `label`, `value`, `trend` (+8%/-3%), `icon` (dollar/users/chart/eye/heart/star/clock/zap), `variant` (default/highlight/muted)

### StatsGrid
Responsive grid of StatCard children.
Props: `columns` (3), `children`

### ProgressBar
Labeled progress indicator.
Props: `label`, `value`, `max` (100), `color`, `showPercent` (true)

### ComparisonTable
Side-by-side comparison.
Props: `headers` (string[]), `rows` (string[][]), `highlight` (0-based col index)

### Timeline / TimelineItem
Vertical event timeline.
TimelineItem props: `date`, `title`, `description`, `variant` (completed/active/upcoming/default)

### Badge
Inline colored label.
Props: `children`, `variant` (default/success/warning/danger/info)

### Tag
Smaller outlined label.
Props: `children`, `color`

### Section
Titled content block.
Props: `title`, `children`

### Grid
CSS grid layout.
Props: `columns` (2), `gap` (4), `children`

### Stack
Vertical flex layout.
Props: `gap` (3), `children`

### Heading
Props: `children`, `level` (1-4)

### Text
Props: `children`, `muted` (false)

### Caption
Props: `children`

## When to Use

Use jsx_preview when:
- User asks to visualize, chart, graph, or plot data
- Comparing multiple items/options side by side
- Summarizing metrics or KPIs (stat cards)
- Showing a timeline or project progress
- Data-heavy research results that benefit from visual structure
- User says dashboard, infographic, breakdown, overview

Do NOT use when:
- Simple text answer is sufficient
- User asks for a file export (PDF, CSV)
- The data is a single number or short list
- Image/video generation tasks

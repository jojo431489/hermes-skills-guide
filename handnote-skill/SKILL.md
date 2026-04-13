---
name: handnote
description: "Generate a beautiful handwritten-style one-page HTML note using Excalidraw fonts (Excalifont + LXGW WenKai TC + Comic Mono)"
user_invocable: true
command: handnote
arguments: "[topic or data source]"
argument-hint: "[topic]"
---

# Handnote — Excalidraw-Style Handwritten Note Generator

Generate a single-page, print-ready HTML document styled as a **handwritten notebook page** using the official Excalidraw font stack.

## Font Stack (MUST USE)

These fonts are the core identity of the skill. Always load them via CDN:

```html
<!-- 1. Excalifont — Excalidraw official hand-drawn font (Latin/Greek/Cyrillic) -->
<style>
@font-face {
  font-family: 'Excalifont';
  src: url('https://excalidraw.nyc3.cdn.digitaloceanspaces.com/fonts/Excalifont-Regular.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}
</style>

<!-- 2. Virgil — Excalidraw legacy hand-drawn font (fallback) -->
<style>
@font-face {
  font-family: 'Virgil';
  src: url('https://raw.githubusercontent.com/excalidraw/virgil/main/Virgil.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}
</style>

<!-- 3. LXGW WenKai TC — Hand-drawn Traditional Chinese (CJK fallback, lazy-loaded chunks) -->
@import url('https://cdn.jsdelivr.net/npm/lxgw-wenkai-tc-web@latest/lxgwwenkaitc-regular/result.css');

<!-- 4. Comic Mono — Monospace code font (Excalidraw Code font web equivalent) -->
@import url('https://cdn.jsdelivr.net/npm/comic-mono@0.0.1/index.css');
```

### CSS Variables (define in `:root`)

```css
:root {
  --font-hand:  'Excalifont', 'Virgil', 'LXGW WenKai TC', sans-serif;
  --font-cjk:   'LXGW WenKai TC', 'Excalifont', sans-serif;
  --font-code:  'Comic Mono', 'Comic Shanns', monospace;
  --font-mixed: 'Excalifont', 'LXGW WenKai TC', 'Virgil', sans-serif;
}
```

### Font Usage Rules

| Content Type | CSS Variable | When |
|---|---|---|
| English headings & labels | `var(--font-hand)` | Title, subtitle, tags |
| Chinese/Japanese text | `var(--font-cjk)` | CJK-heavy paragraphs |
| Mixed EN + CJK | `var(--font-mixed)` | General body text (DEFAULT) |
| Numbers, prices, code, data | `var(--font-code)` | Flight numbers, prices, dates, technical data |

## Visual Design Elements (MUST INCLUDE)

The note must look like a **real physical notebook page**. Include ALL of these:

### 1. Notebook Paper
- **Background**: Cream/off-white (`#fffef5`)
- **Blue ruled lines**: Repeating horizontal lines (`rgba(120,160,210,0.18)`, ~35px apart)
- **Red margin line**: Vertical line on the left (`rgba(220,80,80,0.25)`, ~72px from left edge)
- **Overall page background**: Warm beige (`#d4c5a9`, like a desk surface)

### 2. Physical Artifacts
- **Washi tape**: Two strips at top corners, semi-transparent with stripe pattern, slightly rotated
- **Spiral holes**: 3 circular holes on the left margin with subtle shadow
- **Coffee stain**: A subtle ring mark in the bottom-right area (very faint)

### 3. Card Styling (Excalidraw-inspired)
- Use **asymmetric border-radius** to mimic hand-drawn rectangles (e.g., `border-radius: 2px 6px 4px 3px`)
- Borders should be **slightly transparent** colored lines, not solid blocks
- Light tinted background per card category

### 4. Typography Details
- **Highlighted text**: Use yellow marker effect (`background: linear-gradient(transparent 55%, rgba(255,234,167,0.55) 55%)`)
- **Strikethrough**: Red wavy line for rejected/expensive items
- **Circled numbers**: Hand-drawn circle border around numbers
- **Tags/badges**: Rounded pill shapes with soft colors

### 5. Optional Decorations
- **Sticky note**: A small yellow post-it in the corner for side notes
- **Dashed dividers**: Dotted/dashed lines between sections
- **Checklist**: `☐` / `☑` checkbox items

## Page Structure Template

```
┌──────────────────────────────────────┐
│ [tape]                        [tape] │
│ ○                                    │
│ ○  TITLE (Excalifont)                │
│ ○  subtitle                          │
│    [date badge]                      │
│    ─ ─ ─ ─ ─ ─ ─ ─ ─ ─             │
│    ┌─ Card 1 ──────────────┐        │
│    │  heading + price/data  │        │
│    │  detail line 1         │        │
│    │  detail line 2         │        │
│    └────────────────────────┘        │
│    ┌─ Card 2 ──────────────┐        │
│    │  ...                   │        │
│    └────────────────────────┘        │
│    ─ ─ ─ ─ ─ ─ ─ ─ ─ ─             │
│    ┌─ Recommendation ──────┐        │
│    │  highlighted summary   │   ☕   │
│    └────────────────────────┘        │
│    ─ ─ ─ ─ ─ ─ ─ ─ ─ ─             │
│    ☐ Checklist item 1               │
│    ☐ Checklist item 2               │
│                      * footnote      │
└──────────────────────────────────────┘
```

## Step-by-Step Workflow

### Step 1: Understand the Content

Read the user's input. It could be:
- Raw data (flight search results, price comparisons, research findings)
- A topic to summarize (meeting notes, travel plans, study notes)
- A reference to existing data in the conversation

Identify:
- **Title** and **subtitle**
- **Date** or time range
- **Main items** to compare or list (3-5 items ideally)
- **Recommendation** or conclusion
- **Action items** / checklist

### Step 2: Design the Color Scheme

Pick 3-4 accent colors for cards based on the data. Use soft, muted tones:

```
Blue:   rgba(116,185,255,0.08) border rgba(116,185,255,0.35)
Purple: rgba(162,155,254,0.08) border rgba(162,155,254,0.35)
Pink:   rgba(253,164,175,0.08) border rgba(253,164,175,0.35)
Green:  rgba(85,239,196,0.08)  border rgba(0,184,148,0.5)
Orange: rgba(255,190,118,0.08) border rgba(255,165,79,0.35)
```

### Step 3: Generate the HTML

Create a single self-contained HTML file:
- All CSS inline in `<style>` (no external stylesheets except fonts)
- No JavaScript required (pure CSS)
- Responsive: works on desktop and mobile
- Print-friendly: clean output when printed

### Step 4: Save and Open

- Save to `~/[descriptive-name]-note.html`
- Open in browser with `open ~/[name]-note.html`

## Language Handling

- Detect the user's language from conversation context
- For Chinese (Traditional): Use `lang="zh-TW"` and `var(--font-cjk)` for Chinese text
- For Japanese: Use `lang="ja"` 
- For English: Use `lang="en"` and `var(--font-hand)`
- For mixed content: Use `var(--font-mixed)` as default body font
- Data values (numbers, codes, prices) ALWAYS use `var(--font-code)`

## Quality Checklist

Before delivering, verify:
- [ ] All 4 font families loaded (Excalifont, Virgil, LXGW WenKai TC, Comic Mono)
- [ ] CSS variables defined and used consistently
- [ ] Notebook paper: blue lines + red margin + cream background
- [ ] Physical artifacts: washi tape (2), spiral holes (3), coffee stain
- [ ] Cards use asymmetric border-radius
- [ ] Numbers/prices use `var(--font-code)`
- [ ] Responsive layout (mobile-friendly)
- [ ] Print styles included (`@media print`)
- [ ] File saved and opened in browser

## Example Invocations

```
/handnote 日本旅行機票比較
/handnote meeting notes from today
/handnote 本月 OKR 進度
/handnote compare React vs Vue vs Svelte
```

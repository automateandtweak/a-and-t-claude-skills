# WCAG 2.1 AA Compliance Reference

Companion reference for the WCAG audit skill. Target: WCAG 2.1 AA — zero WAVE errors on every page, desktop and mobile viewports. [WAVE](https://wave.webaim.org/) (Web Accessibility Evaluation Tool) is the recommended testing tool.

---

## Color Contrast Rules

The #1 source of WAVE errors. Memorize these ratios.

### Minimum Ratios

| Text Type | Ratio | Rule of Thumb |
|-----------|-------|---------------|
| Normal text (< 24px, or < 18.66px bold) | **4.5:1** | Most body text, links, labels |
| Large text (>= 24px, or >= 18.66px bold) | **3:1** | Headings, large buttons |
| UI components (borders, icons, focus rings) | **3:1** | Input borders, icon-only buttons |

### Safe Color Palette

Colors verified to pass WCAG AA contrast:

**Dark text on white/light backgrounds:**

| Color | Hex | Use For |
|-------|-----|---------|
| Minimum gray | `#767676` | Absolute minimum for normal text on white |
| Slate gray | `#64748b` | Secondary text, descriptions |
| Dark slate | `#556477` | Small text (11-13px) — needs this or darker |
| Gray | `#6b7280` | Muted labels, helper text |
| Dark gray | `#4a5568` | Stronger secondary text |

**White text on colored backgrounds:**

| Color | Hex | Use For |
|-------|-----|---------|
| Blue | `#1976D2` | Primary buttons, action links |
| Link blue | `#2b6cb0` | Badge backgrounds, tag buttons |
| Red | `#d32f2f` | Error states, destructive actions |
| Gray-green | `#62706d` | Neutral badges, status labels |

**Dark mode text on dark backgrounds:**

| Color | Hex | Use For |
|-------|-----|---------|
| Minimum light | `#aaa` / `#aaaaaa` | Absolute minimum for text in dark mode |
| Light gray | `#9ca3af` | Secondary text in dark mode |
| Off-white | `#cbd5e1` | Primary text in dark mode |

### Known-Failing Colors — Do Not Use

These all fail WCAG AA contrast. If you see them in existing code, fix them.

| Hex | Why It Fails |
|-----|-------------|
| `#3498db` | Blue too light on white (3.3:1) |
| `#2196F3` | Material blue too light on white (3.1:1) |
| `#4a90e2` | Medium blue fails on white |
| `#e74c3c` | Red too light on white (3.9:1) |
| `#999` / `#999999` | Gray too light on white (2.8:1) |
| `#94a3b8` | Slate too light on white |
| `#8e8e8e` | Gray too light on white |
| `#888` / `#888888` | Gray too light on white (3.5:1) |

### Contrast Anti-Patterns

- **Never use `opacity` on text elements** — it reduces effective contrast. Set explicit colors instead.
- **`<a>` tags must set explicit `color`** — inherited/default link colors often fail contrast.
- **Small text (11-13px) needs higher contrast** — use `#556477` or darker on white, not the `#767676` minimum.

---

## Dark Mode

### Common Patterns

Projects implement dark mode differently. Match your project's existing pattern:

| Pattern | Selector | When It's Used |
|---------|----------|----------------|
| Class toggle (JS) | `body.dark-mode .element` or `[data-theme="dark"] .element` | Sites with a manual toggle button. Most common. |
| Media query (OS) | `@media (prefers-color-scheme: dark)` | Sites that follow the user's OS setting only. |
| Both | Class toggle + media query fallback | Sites that offer a toggle but default to OS preference. |

**Important**: Using the wrong selector means dark mode styles won't apply. Check how the project toggles dark mode before writing CSS:
- Look for a toggle button that adds/removes a class → use class selector
- Look for `matchMedia('(prefers-color-scheme: dark)')` without a class toggle → use media query
- If both exist, match the existing convention

### Dark Mode Checklist

- Every light-mode color override needs a dark-mode counterpart
- Minimum dark mode text color: `#aaa` on dark backgrounds
- Test both themes after every CSS change
- Form inputs need explicit `background-color` and `color` in both modes

---

## Focus Indicators

### Rules

- Always use `:focus-visible`, never plain `:focus` (avoids showing outlines on mouse clicks)
- **Never write `outline: none` without a visible replacement**
- Standard pattern:

```css
.interactive-element:focus-visible {
    outline: 2px solid #1976D2;
    outline-offset: 2px;
}
```

- Add a dark-mode variant for focus indicators:

```css
/* Class toggle example */
body.dark-mode .interactive-element:focus-visible {
    outline-color: #60a5fa;
}

/* Media query example */
@media (prefers-color-scheme: dark) {
    .interactive-element:focus-visible {
        outline-color: #60a5fa;
    }
}
```

- Focus must be visible on all interactive elements: links, buttons, inputs, custom controls

---

## Semantic HTML

### Heading Hierarchy

- **No skipping levels**: `h1` -> `h2` -> `h3` (never `h1` -> `h3`)
- Each page gets exactly one `h1`
- Non-heading messages (empty states, notices) use `<div>` or `<p>` with a class — not `<h3>`/`<h4>`

```html
<!-- WRONG: using heading for a status message -->
<h3 class="no-results">No tools found</h3>

<!-- CORRECT -->
<div class="no-results">No tools found</div>
```

### Element Choice

| Purpose | Element | Not This |
|---------|---------|----------|
| Clickable action (no navigation) | `<button type="button">` | `<div onclick>`, `<span onclick>`, `<a href="#">` |
| Navigation to another page/section | `<a href="...">` | `<div onclick>`, `<button>` |
| Centering content | CSS (`text-align: center`, flexbox) | `<center>` |
| Form field label | `<label for="inputId">` | `<span>`, `<div>` |
| Group label (radio/checkbox set) | `<span id="x">` + `aria-labelledby="x"` | `<label>` wrapping the group |

### Labels and Inputs

- Every visible `<input>`, `<select>`, `<textarea>` needs a `<label for="id">` pointing to it
- For visually hidden labels: use `sr-only` class, not `display: none` (screen readers skip `display: none`)
- For grouped controls (radio buttons, star ratings): use `aria-labelledby` referencing a visible `<span>`

---

## ARIA Patterns

Use ARIA only when native HTML semantics are insufficient.

### Decorative Content

```html
<!-- Emojis/icons that add no information -->
<span aria-hidden="true">&#x1f527;</span>
<i class="fas fa-star" aria-hidden="true"></i>
```

### Star Rating / Radio Groups

```html
<div role="radiogroup" aria-labelledby="rating-label">
    <span id="rating-label">Rating</span>
    <input type="radio" name="rating" value="1" aria-label="1 star">
    <input type="radio" name="rating" value="2" aria-label="2 stars">
    <!-- ... -->
</div>
```

### Clickable Non-Button Elements

When a `<div>` or `<span>` must be clickable (last resort — prefer `<button>`):

```html
<div role="button" tabindex="0" onclick="doThing()" onkeydown="if(event.key==='Enter'||event.key===' ')doThing()">
    Click me
</div>
```

### Modal Close Buttons

```html
<!-- WRONG -->
<span class="close" onclick="closeModal()">&times;</span>

<!-- CORRECT -->
<button type="button" class="close" aria-label="Close" onclick="closeModal()">
    <span aria-hidden="true">&times;</span>
</button>
```

---

## Text Size

- **Minimum text size: 11px** — WAVE flags anything smaller as a "very small text" alert
- Text at 11-13px needs **higher contrast** than the 4.5:1 minimum — use `#556477` or darker on white
- Prefer `14px`+ for body text for readability

---

## Mobile-Specific

- Mobile-only elements (hamburger menus, mobile filters, sticky bars) need **all the same checks**: contrast, labels, focus, semantics
- Elements hidden at desktop with `display: none` or `@media` still need to pass when visible at mobile widths
- **Test WAVE at mobile viewport width** — resize the browser or use device emulation before running the audit
- Mobile filter/sort buttons commonly fail contrast — always verify

---

## Quick Checklist

Run through this before marking any CSS/HTML work as done:

| # | Check | Details |
|---|-------|---------|
| 1 | Text contrast | All text >= 4.5:1 (normal) or >= 3:1 (large). Use safe palette. |
| 2 | Dark mode contrast | All dark-mode overrides (using the project's dark mode selector) also meet ratios. Min text: `#aaa`. |
| 3 | No opacity on text | Use explicit colors, not `opacity: 0.7` on text. |
| 4 | Focus visible | All interactive elements have `:focus-visible` outline. |
| 5 | Heading hierarchy | No skipped levels. One `h1` per page. |
| 6 | Labels on inputs | Every form control has `<label for>` or `aria-label`. |
| 7 | Button semantics | Actions = `<button>`, links = `<a>`. No `<div onclick>`. |
| 8 | Decorative ARIA | Emojis and icons have `aria-hidden="true"`. |
| 9 | Mobile elements | Mobile-only UI passes all checks at mobile width. |
| 10 | Min text size | Nothing below 11px. Small text uses higher contrast. |

---

## Testing Workflow

1. **Write** — follow the rules above during implementation
2. **WAVE desktop** — run WAVE at normal desktop width. Fix all errors. Alerts are worth reviewing but not mandatory.
3. **WAVE mobile** — resize to ~375px width, run WAVE again. Mobile-only elements appear here.
4. **Fix** — address every error. Use the safe palette. Check dark mode variant.
5. **Verify dark mode** — toggle dark mode on, run WAVE one more time. Dark mode contrast failures are easy to miss.

---

**Remember**: The most common fix is replacing a failing color with one from the safe palette. When in doubt, go darker on light backgrounds and lighter on dark backgrounds.

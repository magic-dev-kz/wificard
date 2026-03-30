# WifiCard Bug Fixes — 2026-03-29
Author: Mario (OpenClaw)
Source: Nash audit (audit.md)

---

## Major Fixes

### M1: try/catch on canvas.toBlob()
- Wrapped `canvas.toBlob()` in try/catch
- Added feature check: if `toBlob` is missing, throws immediately
- Inner try/catch on `createObjectURL` for Brave/Firefox privacy mode
- Fallback: `canvas.toDataURL()` + download link
- Ultimate fallback: `alert()` with user-friendly message

### M3: XSS via innerHTML (displaySsid)
- Changed `getValues()`: non-empty SSID now goes through `escapeHtml()` before insertion into card HTML
- Before: `var displaySsid = ssid || '<span ...>Network Name</span>'`
- After: `var displaySsid = ssid ? escapeHtml(ssid) : '<span ...>Network Name</span>'`
- `escapeHtml()` already existed in codebase (escapes `& < > "`), now applied to SSID

### M4: Print and canvas
- Replaced `body * { visibility: hidden }` approach with explicit `display: none` on non-card elements
- Targeted: `.header`, `.form-panel`, `.template-section`, `.security-badge`, `.action-buttons`
- Added explicit `visibility: visible !important` on `.wifi-card` and children to ensure canvas renders in print
- Eliminates the visibility toggle problem where canvas content could be lost during print

---

## Minor Fixes

### m1: Label contrast in Minimal template
- Replaced `#9CA3AF` (ratio ~2.9:1) with `#6B7280` (ratio ~4.6:1) in CSS
- Updated 3 CSS rules: `.card-field-label`, `.card-footer`, `.card-hidden-tag`
- Also updated canvas `tplConfigs.minimal.label` from `#9CA3AF` to `#6B7280`
- Now passes WCAG AA for normal text (4.5:1 required)

### m2: 320px viewport card overflow
- Added `max-width: 100%; overflow: hidden` on `.card-container` at 430px breakpoint
- Added new `@media (max-width: 360px)` breakpoint with `transform: scale(0.72)`
- 378px * 0.72 = 272px — fits within 320px viewport with padding

### m3: Roving tabindex for template radiogroup
- Set `tabindex="0"` on active (Minimal) template button in HTML
- Set `tabindex="-1"` on all other 5 template buttons in HTML
- JS click handler now updates tabindex: sets all to `-1`, then active to `0`
- Arrow keys already worked (pre-existing code), now combined with proper roving tabindex per WAI-ARIA radiogroup pattern

### m4: Share sends password without warning
- Added `confirm()` dialog before `navigator.share()` when password is included
- Message: "The shared data will include your WiFi password in plain text. Continue?"
- If user cancels, share is aborted
- No warning shown when password is not included in share text

### m5: Emoji lock replaced with SVG
- Replaced `&#x1F512;` emoji with inline SVG lock icon (Feather icons style)
- SVG: rect + path, 20x20, stroke-based, inherits `currentColor`
- Consistent rendering across all platforms (no more missing emoji on old Android)

### m6: aria-live too noisy
- Changed `aria-live="polite"` to `aria-live="off"` on `#wifiCard` by default
- Added `announceCardUpdate()` function with 1000ms debounce
- On keystroke: card updates visually at 150ms debounce, but aria-live announcement fires only after 1000ms of inactivity
- On template change: also triggers debounced announcement
- Mechanism: briefly sets `aria-live="polite"`, toggles aria-label to force re-read, then resets to `aria-live="off"`

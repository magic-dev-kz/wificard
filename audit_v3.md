# WifiCard v2.5 — QA Audit Report

- **Auditor:** Nash (OpenClaw QA)
- **Date:** 2026-03-29
- **File:** `index.html` (2761 lines), `sw.js` (23 lines)
- **Scope:** v2.5 delta vs v2.1 + regression check

---

## Score: 6/10

---

## P1 — Critical

### P1-1: Airbnb Canvas export skips welcome text truncation

**File:** `index.html`, line 2296

All other templates route welcome text through `drawWelcomeText()` which truncates at 50 chars (line 2132). The Airbnb canvas branch bypasses this helper and calls `ctx.fillText(welcomeText, PAD, ay)` directly with the raw, un-truncated `welcomeText`. A 50-character welcome message will overflow the card boundary on PNG export.

**Fix:** Replace line 2296 with truncation logic matching `drawWelcomeText`:
```js
var truncWelcome = welcomeText.length > 50 ? welcomeText.substring(0, 50) + '...' : welcomeText;
ctx.fillText(truncWelcome, PAD, ay);
```

### P1-2: Copy WiFi leaks password regardless of "Show password" toggle

**File:** `index.html`, lines 2654-2658

The Copy button builds its text as:
```js
var text = 'Network: ' + (v.ssid || '');
if (v.security !== 'nopass' && v.password) {
  text += '\nPassword: ' + v.password;
}
```

It does not check `v.showPass`. If the user has "Show password on card" toggled OFF (intending to keep the password hidden, sharing the QR-only card), clicking Copy still copies the plaintext password to the clipboard. This defeats the privacy intent of the toggle and creates an information-disclosure risk when the user pastes the copied text.

**Fix:** Gate password inclusion on `v.showPass`:
```js
if (v.security !== 'nopass' && v.password && v.showPass) {
```

---

## P2 — Significant

### P2-1: SVG Airbnb welcome text uses accent red (#FF5A5F), not #6B6B6B

**File:** `index.html`, line 2581

The SVG export for Airbnb renders the welcome message with `cfg.accent` (#FF5A5F). The HTML card renders it via `.card-welcome` also in #FF5A5F. However, the v2.5 spec states Airbnb welcome text color should be #6B6B6B. The CSS rule `.card-airbnb .card-welcome-text { color: #6B6B6B; }` (line 1142) exists but is dead code because the Airbnb HTML template uses class `.card-welcome` (line 1827), never `.card-welcome-text`.

**Impact:** The P3 fix from the previous audit (welcome text color #767676 -> #6B6B6B) was applied to a CSS selector that Airbnb never hits. All three outputs (HTML, Canvas, SVG) show #FF5A5F for Airbnb welcome text.

**Fix:** Either:
- (a) Change `.card-airbnb .card-welcome` color to `#6B6B6B` and update Canvas/SVG to match, or
- (b) Refactor Airbnb template to use `welcomeHtml()` and `.card-welcome-text` like the other templates.

### P2-2: SVG export does not sanitize font-family in `<style>` block

**File:** `index.html`, line 2489

```js
svg += '<defs><style>text { font-family: ' + svgEsc(cfg.font) + '; }</style></defs>';
```

`svgEsc()` escapes XML entities (`& < > " '`), but inside a `<style>` block, the content is CDATA-like — XML entity escaping does not protect against CSS injection. A crafted font string containing `} text { fill: red; } /*` or similar would break out of the font-family declaration. Currently the font strings are hardcoded constants (not user input), so this is not immediately exploitable, but it is a latent injection vector if font selection ever becomes user-configurable.

**Fix:** Wrap the style content in `<![CDATA[ ... ]]>` or validate font values against an allowlist.

### P2-3: `fallbackCopy()` uses deprecated `document.execCommand('copy')`

**File:** `index.html`, lines 2677-2686

The clipboard fallback uses `document.execCommand('copy')` which is deprecated and may be removed from browsers. The primary `navigator.clipboard.writeText` path is correct, but the fallback silently swallows failures (empty `catch` block, line 2684) — user gets "Copied!" feedback even if the copy actually failed.

**Fix:** Track success and only show "Copied!" if `execCommand` returns `true`; otherwise show an error state.

---

## P3 — Minor

### P3-1: `aria-live="polite"` on char count wraps the entire div, not just the count

**File:** `index.html`, line 1469

```html
<div class="welcome-char-count" aria-live="polite"><span id="welcomeCharCount">0</span>/50</div>
```

This is functionally correct and matches the v2.5 spec. Screen readers will announce "N/50" on every keystroke, which may be verbose. Consider `aria-live` only on the `<span>` with `aria-atomic="true"` on the parent for cleaner announcements. Minor a11y polish.

### P3-2: Template radiogroup arrow-key navigation not implemented

**File:** `index.html`, lines 1542-1579

The template selector uses `role="radiogroup"` and `role="radio"` with proper `aria-checked` and `tabindex` management. However, there is no keyboard arrow-key handler for left/right navigation between radio items — the standard WAI-ARIA radio pattern. Users must Tab through each item individually.

### P3-3: SVG export drops Airbnb box-shadow visual

The HTML Airbnb card has `box-shadow: 0 2px 12px rgba(0,0,0,0.08)` (line 787). The SVG export does not include a `<filter>` for this shadow. The SVG card appears flat compared to the HTML preview. Cosmetic only.

### P3-4: Print CSS `.action-buttons` selector does not match actual class

**File:** `index.html`, line 1247

```css
.action-buttons { display: none !important; }
```

The actual class in the HTML is `.actions` (line 1514), not `.action-buttons`. The action buttons are still hidden during print because they are children of `.preview-panel` content flow and the card container repositions, but the explicit hide rule is a no-op. Should be `.actions`.

---

## v2.5 Feature Verification

| # | Feature | Status | Notes |
|---|---------|--------|-------|
| 1 | `escapeHtml` escapes single quotes | PASS | Line 1759: `replace(/'/g,'&#39;')` present |
| 2 | Canvas welcome truncation = 50 chars | PARTIAL | Works for minimal/warm/hotel/tech/kids via `drawWelcomeText()`. **FAILS for Airbnb** (P1-1) |
| 3 | Airbnb welcome text color #6B6B6B | FAIL | Dead CSS rule; actual color is #FF5A5F (P2-1) |
| 4 | `aria-live="polite"` on welcome char count | PASS | Line 1469 |
| 5 | SVG Export button | PASS | Button present (line 1523), handler generates valid SVG with `svgEsc()` sanitization, Blob download works |
| 6 | Copy WiFi button | PARTIAL | Clipboard API + fallback present. But ignores showPassword toggle (P1-2) |
| 7 | SW cache `wificard-v2.5` | PASS | `sw.js` line 1 |

---

## Regression Check

| Area | Status | Notes |
|------|--------|-------|
| Canvas PNG export | OK | All 6 templates render correctly; QR generation intact |
| Print CSS | MINOR | `.action-buttons` selector is dead (P3-4), but print layout functional |
| QR encoding | OK | `escapeWifi()` properly escapes `\ ; , " :` |
| Security toggle (nopass) | OK | Password field collapses, QR string omits `P:` field |
| localStorage save/restore | OK | Saves network, password, template, welcome; restores on load |
| Onboarding overlay | OK | Shows once, stores flag, dismisses cleanly |
| Responsive scaling | OK | Three breakpoints (768/430/360) with progressive scale-down |
| prefers-reduced-motion | OK | Kills all animations/transitions |

---

## Verdict: FIX

Two P1 issues must be resolved before ship:
1. Airbnb canvas welcome truncation (data overflow on PNG export)
2. Copy button password leak when "Show password" is off

One P2 (dead CSS for Airbnb welcome color) means a claimed v2.5 fix is not actually applied.

**Estimated fix time:** ~30 minutes for all P1+P2 items.

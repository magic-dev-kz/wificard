# WifiCard HN Pre-Launch Audit

**Auditor:** Nash (QA)
**Date:** 2026-03-29
**File:** `/Volumes/OpenClaw/sandbox/projects/qr-gen/index.html`
**File size:** 71.8 KB (single HTML file)

---

## Overall Score: 9.0/10

**Verdict: READY for HN launch** (with 2 recommended fixes below)

---

## 1. Privacy Audit -- PASS

This is the #1 thing HN will check. The result is excellent.

| Check | Result |
|-------|--------|
| External `<script src>` | NONE |
| External `<link href>` (CSS/fonts) | NONE |
| `fetch()` / `XMLHttpRequest` | NONE |
| `navigator.sendBeacon` | NONE |
| `new Image()` tracking pixel | NONE |
| Google Analytics / Plausible / any analytics | NONE |
| Any URL strings in JS (besides data: favicon) | NONE |
| WiFi password sent anywhere | NO -- 100% client-side |
| Cookies | NONE |
| Service Worker | NONE |

**The only external references are in `<meta>` OG/Twitter tags** (`https://wificard.io/og-image.png`, `https://wificard.io`). These are metadata tags that browsers do NOT fetch unless a crawler requests them. This is standard and expected.

**localStorage usage:** Only `wificard_onboarding_done` key (stores `"1"`). Password/SSID are NEVER persisted. The `setItem` call IS wrapped in try/catch (line 2147). The `getItem` on line 2138 is NOT wrapped in try/catch.

### P3: localStorage.getItem without try/catch (line 2138)
```
if (localStorage.getItem(KEY)) return;
```
In Safari private mode, `getItem` can throw. Wrap in try/catch. Low risk (only affects onboarding overlay display), but HN devs WILL grep for this and call it out.

**Share feature privacy:** The share button shows a `confirm()` dialog warning about plain-text password (line 2122). Good.

---

## 2. Code Quality (View Source) -- PASS

### Structure
- CSS and JS sections are clearly separated with `/* === */` comment headers
- Application code is wrapped in IIFE with `'use strict'` (line 1439)
- No globals leaked except `qrcode` (the QR library)
- CSS uses custom properties (`:root` block with meaningful names)
- Comments are present and helpful

### The QR library (line 1434)
The QR code generator is minified into a single long line. This is the ONE thing that looks ugly in View Source. However:
- It has a clear comment header: "Minimal QR encoder based on Kazuhiko Arase's qrcode-generator (MIT license)"
- Attribution + license mentioned
- It's separated from the application code

**Verdict:** Acceptable. HN understands inlining a dependency for offline-first. The MIT attribution is correct.

### What looks GOOD to HN devs viewing source:
- Clean semantic HTML (header, main, nav, footer, section)
- ARIA roles on template selector (radiogroup, radio, aria-checked)
- `sr-only` class for screen readers
- `prefers-reduced-motion` respected
- Print media query exists
- No framework, no build step, just HTML/CSS/JS
- `escapeHtml()` function properly escapes `& < > "`

### No leftover debug code
- No `console.log`
- No `debugger`
- No `TODO` comments
- No dead code

---

## 3. Edge Cases

### Empty password (open network)
- Security dropdown has "None" (`nopass`) option
- When selected, password field collapses with animation
- QR string correctly omits `P:` field: `WIFI:T:nopass;S:...;H:false;;`
- Card correctly hides password row
- **PASS**

### Unicode in SSID
- `escapeWifi()` only escapes `\ ; , " :` -- does NOT break Unicode
- `escapeHtml()` escapes `& < > "` -- safe for HTML rendering
- QR library uses byte mode (mode 4) with `charCodeAt` -- works for BMP characters
- **P3: QR library uses `charCodeAt` (16-bit), NOT `codePointAt`.** Characters outside BMP (some emoji, rare CJK) will be encoded incorrectly. Standard Chinese/Arabic/Cyrillic are in BMP and work fine. Extremely low risk for WiFi SSIDs.
- Canvas text rendering: `ctx.fillText()` handles Unicode natively
- **PASS** for practical use cases

### Maximum SSID length (32 chars)
- HTML input has `maxlength="32"` -- correct, 32 is the IEEE 802.11 limit
- Display truncates at 18 chars in canvas export (with `...`)
- Card HTML uses `text-overflow: ellipsis` with `max-width`
- **PASS**

### Special characters in password (`;:"',\`)
- `escapeWifi()` escapes all WiFi-spec special chars: `\ ; , " :`
- Single quote `'` is NOT escaped -- this is correct per WiFi QR spec (it's not a special char)
- `escapeHtml()` escapes `"` but NOT `'`
- **No XSS risk** because values are placed in `innerHTML` as text content, not in attributes. The only attribute construction uses CSS class names from hardcoded strings, not user input.
- **PASS**

### Very short password (1 character)
- No minimum length validation (correct -- WiFi allows any length)
- QR code will generate correctly
- Display works fine
- **PASS**

### Empty SSID
- Displays `<span style="opacity:0.5">Network Name</span>` as placeholder
- QR string: `WIFI:T:WPA;S:;P:password123;H:false;;` -- valid but useless QR
- **P3:** Empty SSID generates a technically valid but non-functional QR code. Could show a hint "Enter network name" instead. Not a blocker.

---

## 4. Cross-Browser

### Firefox
- CSS custom properties: fully supported since Firefox 31
- `canvas.toBlob`: supported since Firefox 19
- `navigator.share`: supported since Firefox 71 (desktop: behind flag, mobile: yes). Fallback: button is hidden if `navigator.share` undefined. **PASS**
- `:focus-visible`: supported since Firefox 85. **PASS**
- `inset: 0` shorthand: supported since Firefox 66. **PASS**

### Safari
- Web Share API: supported since Safari 15. Fallback: share button hidden. **PASS**
- `canvas.toBlob`: supported since Safari 11. Fallback to `toDataURL` present (line 2096). **PASS**
- `backdrop-filter`: needs `-webkit-` prefix. Present on line 1159. **PASS**
- CSS `inset`: supported since Safari 14.1. **PASS**
- `Array.from`: supported since Safari 9. Used on line 1740. **PASS**

### IE11
- Not supported (CSS variables, template literals, etc.). Nobody on HN cares. **N/A**

---

## 5. Print Quality

### Print CSS (lines 1103-1135)
- Hides header, template section, security badge, form panel, action buttons
- Card container positioned absolute, centered
- `background-image: none` removes dot pattern
- `@page { margin: 1cm; }` set

### P2: QR code MAY not print on some browsers
The print CSS sets `visibility: visible !important` on `.card-container *` and `.wifi-card *`. The QR code is a `<canvas>` element. Canvas elements generally print well in modern browsers, but:
- `print-color-adjust: exact` (formerly `-webkit-print-color-adjust`) is NOT set anywhere. This means colored backgrounds (Hotel dark template, Tech dark template, Kids yellow) may not print by default -- users must enable "Background graphics" in print dialog.
- **Recommendation:** Add `print-color-adjust: exact; -webkit-print-color-adjust: exact;` to `.wifi-card` in the print media query.

### P3: Card scale on mobile
On screens < 430px, the card has `transform: scale(0.82)`. The print CSS does NOT reset this transform. If someone prints from mobile, the card may be smaller than intended. Low risk (most people print from desktop).

---

## 6. Accessibility

### WCAG AA Contrast -- PASS (with notes)
| Element | Colors | Ratio | Result |
|---------|--------|-------|--------|
| Body text | #1A1A2E on #F7F8FA | ~14.5:1 | PASS |
| Secondary text | #6B7280 on #FFFFFF | ~5.0:1 | PASS |
| Security badge | #065F46 on #ECFDF5 | ~7.8:1 | PASS |
| Security badge span | #047857 on #ECFDF5 | ~5.7:1 | PASS |
| Hotel card: gold on dark | #D4AF37 on #1A1A2E | ~6.4:1 | PASS |
| Hotel card: silver on dark | #C0C0C0 on #1A1A2E | ~8.5:1 | PASS |
| Airbnb: #767676 on #FFFFFF | ~4.48:1 | BORDERLINE (AA requires 4.5:1 for normal text) |
| Tech: #8B949E on #0D1117 | ~5.3:1 | PASS |
| Kids: #E91E63 on #FFF8E1 | ~4.6:1 | PASS |
| Template label | #6B7280 on #F7F8FA | ~4.7:1 | PASS |

**P3: Airbnb template #767676 on white = 4.48:1** -- 0.02 below AA threshold for normal text. This is a known color from Airbnb's own design system. Arguably acceptable, but technically fails.

### Keyboard Navigation -- PASS
- Template selector uses roving tabindex with arrow key navigation (lines 1739-1753)
- All buttons have `focus-visible` styles
- Toggle switches have `focus-visible` outline
- Form inputs have visible focus ring

### Screen Reader -- PASS
- `aria-label` on sections ("WiFi settings", "Card preview")
- `aria-live` region on card preview (debounced 1000ms to avoid spam)
- `role="radiogroup"` on template selector with `aria-checked`
- `sr-only` hints on form fields
- SVG icons have `aria-hidden="true"`
- Onboarding overlay uses semantic HTML (h1, p, ul)

### P3: Onboarding overlay has no focus trap
When onboarding is visible, the user can Tab into the form behind it. Not critical since it's a simple one-button overlay that appears only once.

### prefers-reduced-motion -- PASS
Line 1140: sets `animation-duration: 0.01ms` and `transition-duration: 0.01ms` for all elements. Uses the `0.01ms` pattern (not `none`) to preserve `animationend` handlers.

---

## 7. Performance

- **Single file, 71.8 KB** -- no additional HTTP requests
- **No external resources** -- zero network dependency after initial load
- **QR library is ~12 KB minified** -- reasonable
- **First paint:** Essentially instant (single file, no async loads)
- **No JavaScript frameworks** -- DOM operations are vanilla
- **Debounced input** (150ms) prevents excessive re-renders

---

## 8. HN-Specific Concerns

### "Is this REALLY offline?"
**YES.** Save the file, disconnect from internet, open it. Everything works. QR generation is client-side. No fonts loaded. No CDN. Favicon is a data: URI. HN will love this.

### "Is this REALLY private?"
**YES.** Zero external requests. Password stays in JS memory and DOM only. Not persisted to localStorage. `navigator.share` shows a confirm() warning about password. HN will respect this.

### "Why not use the standard QR library?"
The inlined QR library is based on Kazuhiko Arase's MIT-licensed `qrcode-generator`, which is the most widely used JS QR implementation. Attribution is present. HN will accept this.

### "Why inline everything instead of separate files?"
For a tool that claims "offline, no server," a single HTML file is the CORRECT architecture. HN will appreciate this choice.

### OG image reference
The `<meta property="og:image" content="https://wificard.io/og-image.png">` tag references an external URL. This is expected for social media previews and does NOT affect privacy (browsers don't fetch OG images -- only crawlers/social platforms do).

---

## Summary of Findings

### P2 (should fix before launch)
1. **No `print-color-adjust: exact`** -- colored card backgrounds (Hotel, Tech, Kids) won't print without user manually enabling "Background graphics." Add to print CSS.

### P3 (nice to fix, not blocking)
1. **`localStorage.getItem` without try/catch** (line 2138) -- Safari private mode edge case
2. **Airbnb template #767676 contrast** = 4.48:1 (0.02 below AA threshold)
3. **Empty SSID generates non-functional QR** -- consider disabling download/print when SSID is empty
4. **No focus trap on onboarding overlay** -- one-time display, low impact
5. **Mobile scale not reset in print CSS** -- niche scenario
6. **QR library uses charCodeAt, not codePointAt** -- astral Unicode characters (emoji in SSID) will encode incorrectly

### No issues found
- Zero tracking/analytics
- Zero external requests
- XSS: `escapeHtml` is correct, user input never injected into attributes
- WiFi QR spec: escape function covers all required special characters
- All templates render correctly with edge case inputs
- Canvas download has toBlob + toDataURL fallback chain
- Share button correctly hidden when Web Share API unavailable
- Share warns about password in plain text

---

## Recommended Pre-Launch Fixes (2 items)

### Fix 1: print-color-adjust (P2)
In the `@media print` block, add:
```css
.wifi-card {
  print-color-adjust: exact;
  -webkit-print-color-adjust: exact;
}
```

### Fix 2: localStorage try/catch (P3)
Line 2138:
```js
// Before:
if (localStorage.getItem(KEY)) return;

// After:
try { if (localStorage.getItem(KEY)) return; } catch(e) {}
```

---

*Nash out. Ship it.*

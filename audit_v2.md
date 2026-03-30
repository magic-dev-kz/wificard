# WifiCard v2.0 — QA Audit
**Auditor:** Nash | **Date:** 2026-03-29 | **Score: 9.0/10**

## Checklist Results

| # | Check | Status | Notes |
|---|-------|--------|-------|
| 1 | localStorage try/catch | PASS | All 5 operations (setItem x2, getItem x2, removeItem x1) wrapped in try/catch |
| 2 | WCAG AA contrast — new elements | PASS (borderline) | welcome-char-count uses --color-text-secondary (#6B7280) on white = 4.56:1 (passes AA). card-welcome-text colors vary by template — see P3 below |
| 3 | escapeHtml on welcome text | PARTIAL | Escapes &<>" but NOT single quote (') — self-XSS risk in attribute context. Welcome text goes into innerHTML via displayWelcome, not attributes, so actual XSS risk is LOW |
| 4 | Password generator — crypto | PASS | Uses crypto.getRandomValues + rejection sampling for uniform distribution. No Math.random anywhere in codebase |
| 5 | Welcome text in Canvas export | PASS | drawWelcomeText() renders welcome in all 6 templates with per-template color/font/alignment. Truncates at 40 chars with "..." |
| 6 | Print CSS — welcome text | PASS | card-welcome-text is inside .wifi-card, visibility:visible !important + print-color-adjust:exact covers it |
| 7 | Mobile responsive — new elements | PASS | welcome-field is standard form element, inherits 100% width. generate-btn is flex-shrink:0 in password-row. No overflow issues |
| 8 | prefers-reduced-motion | PASS | Global wildcard rule: animation-duration:0.01ms + transition-duration:0.01ms covers all new elements including .generated feedback animation |
| 9 | Keyboard navigation | PASS | generatePassBtn and clearSettingsBtn are native buttons, focus-visible styled. Template radiogroup has arrow key navigation |
| 10 | Edge cases | See below |

## Edge Cases (Welcome Text)

| Case | Result |
|------|--------|
| Empty welcome | PASS — welcomeHtml returns empty string, canvas skips drawWelcomeText |
| 50 chars (maxlength) | PASS — HTML input enforces maxlength=50, canvas truncates at 40 with "..." (10 char gap = safe) |
| Unicode welcome | PARTIAL — canvas ctx.fillText handles emoji/CJK, but no explicit font fallback for emoji rendering. Browser-dependent |
| XSS in welcome | PASS — escapeHtml applied via displayWelcome before innerHTML insertion. Canvas uses fillText (inherently safe) |

## Bugs Found

### P3 (Minor)

1. **escapeHtml missing single quote** — `escapeHtml()` does not escape `'` (line 1749). Welcome text only goes into innerHTML (not attributes), so actual XSS vector requires attribute injection which doesn't happen here. Still, inconsistent with best practice.
   - Fix: add `.replace(/'/g, '&#x27;')`

2. **Airbnb template welcome fallback** — Airbnb template uses `v.displayWelcome || 'Welcome!'` (line 1817), meaning empty welcome shows "Welcome!" instead of nothing. Other 5 templates show nothing when empty. Inconsistent behavior.
   - Note: This may be intentional design (Airbnb always has a greeting). Verify with design.md.

3. **Canvas welcome truncation != HTML truncation** — Canvas truncates at 40 chars (`welcomeText.substring(0, 40) + '...'`), HTML card shows full 50 chars. User sees different text in preview vs download.
   - Fix: Either truncate both at 50 or both at 40.

4. **#767676 on white (Airbnb template card-welcome-text)** — 4.48:1, technically fails AA by 0.02 (need 4.5:1). Known Airbnb design choice, flagged in previous audit.

5. **Welcome char count not announced to screen readers** — `welcomeCharCount` updates textContent but has no aria-live. Low priority since maxlength prevents overflow.

## Summary

v2.0 additions are solid. The three new features (password generator, welcome message, localStorage) are all well-implemented:

- **Password generator**: Proper crypto with rejection sampling — best-in-class.
- **Welcome message**: Clean integration across HTML, Canvas, and print. escapeHtml applied consistently.
- **localStorage**: All operations try/catch wrapped. Restore logic handles missing/null values correctly.

No P1 or P2 bugs. 5 P3 items, all minor. Ship-ready.

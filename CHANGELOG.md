# WifiCard — Changelog

## v5.0 (2026-03-29)
### Added
- **PDF Export (P0)** — "Download PDF" button next to Download PNG; uses browser's print-to-PDF via `window.print()` with existing `@media print` CSS rules for clean card-only output
- **Custom Accent Color (P1)** — color picker in the form panel to override template accent colors (title, QR foreground, decorative elements); selected color persists in localStorage; "Reset" button restores template default
- **Additional Note Field** — new "Additional note" text input (100 char limit) for instructions like "Password changes monthly"; displayed on card below main content in all 6 templates
- **Extended Welcome Message** — welcome message character limit increased from 50 to 200; canvas/SVG export truncates at 80 characters for visual fit

### Changed
- Version bump to v5.0
- SW cache bumped to wificard-v5.0
- localStorage now saves/restores note text and accent color
- Clear Settings button resets note and accent color

## v4.0 (2026-03-29)
### Added
- **Dark Mode** — toggle in header, CSS custom properties for dark theme, preference saved to localStorage, respects `prefers-color-scheme` system setting on first visit; card templates retain their own styles
- **Copy Password Button** — dedicated "Copy" button next to the password input field, uses Clipboard API with `document.execCommand('copy')` fallback
- **Animated Card Preview** — smooth fade-out/fade-in crossfade (0.25s) when switching templates, subtle shadow pulse animation on first render
- **Onboarding Illustration** — CSS-only animated illustration (phone + WiFi waves + QR icon) replaces the emoji icon on the onboarding overlay

### Changed
- Template crossfade timing extended from 150ms to 250ms for smoother transitions
- Version bump to v4.0
- SW cache bumped to wificard-v4.0

## v3.0 (2026-03-29)
### Added — Premium Visual Redesign (CSS-only)
- **Inter font** — Google Fonts Inter (400–800), weight 800 for headings, tighter letter-spacing
- **Body gradient** — multi-layered radial-gradient (subtle indigo/purple tones over #F7F8FA)
- **Glassmorphism header** — backdrop-filter blur + saturation, sticky positioning
- **Shadow system** — dual-layer `--shadow-sm`/`--shadow-md`, new `--shadow-lg` and `--shadow-glow`
- **Micro-animations** — `@keyframes cardIn` entrance for form/preview panels and wifi card, `@keyframes shimmer` sweep for accent buttons
- **Accent buttons** — gradient backgrounds (`--gradient-accent`), glow shadow on hover, shimmer `::before` sweep
- **Template selector** — hover lift (`translateY(-3px)`), glow shadow, active state with accent box-shadow ring
- **Input fields** — focus ring (4px accent), hover border hint (#C7C9CE), 1.5px borders, 10px radius
- **Preview card** — shadow escalation on hover (`--shadow-lg`), subtle lift, cardIn entrance animation
- **Action buttons** — primary gets gradient CTA style with shimmer sweep, secondary gets accent border + ring on hover
- **Onboarding CTA** — gradient background, glow shadow, shimmer sweep on hover
- **Mobile responsive** — touch targets min 44px on action buttons, template thumbs, toggle switches, generate button

### Changed
- Version bump to v3.0
- Border-radius increased to 10–16px on key surfaces (form-panel, card-container, inputs)
- `--color-accent-light` custom property added for consistent focus rings

## v2.6 (2026-03-29)
### Added
- **SVG Export** — vector download for high-quality printing
- **Copy WiFi Settings** — copy SSID + password to clipboard (respects "Show password" toggle)

### Fixed
- escapeHtml now escapes single quotes (&#39;)
- Canvas welcome text truncation matched to HTML (50 chars, all 6 templates)
- Airbnb welcome text color #FF5A5F → #6B6B6B (WCAG AA on white)
- Airbnb Canvas welcome text now properly truncated (was bypassing drawWelcomeText)
- Copy WiFi respects "Show password" toggle — no password leak when hidden
- SVG `<style>` block wrapped in CDATA (CSS injection prevention)
- fallbackCopy now checks execCommand success before showing "Copied!"
- Print CSS selector fixed (.action-buttons → .actions)
- Welcome char count has aria-live="polite"

### Changed
- SW cache bumped to wificard-v2.6

## v2.1 (2026-03-29)
### Added
- Welcome text field for personalized greeting on cards
- Password generator (crypto-secure, rejection sampling)
- localStorage autosave for settings
- Clear saved settings button

## v2.0 (2026-03-29)
### Added
- 6 card templates (Minimal, Warm, Hotel, Airbnb, Tech, Kids)
- Canvas PNG export (756x528 @2x retina)
- QR encoder inline (Kazuhiko Arase ~8KB)
- Print CSS with media queries
- Share with password confirmation
- Roving tabindex on template radiogroup

## v1.0 (2026-03-29)
- Initial release
- WiFi QR code generator
- PWA (service worker + manifest)

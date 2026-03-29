# WifiCard — Changelog

## v13.0 (2026-03-29) — Print-Out Animation

### Added
- **Card print-out animation** -- on first card generation, the wifi card "prints out" with a slide-down + unfold CSS keyframe animation (clip-path reveal, perspective rotateX unfold, shadow pulse); replaces the simple fade-in for the initial render

### Changed
- Version bumped to v13.0
- SW cache bumped to `wificard-v13.0`

### Preserved
- All v12.x features intact (zoom, favorites, sound)

---

## v12.0 (2026-03-29) — Zoom, Favorites & Sound

### Added
- **Card Preview Zoom** — pinch/scroll zoom on the card preview area (Ctrl+scroll or two-finger pinch); CSS transform scale with +/- controls and 1:1 reset button; range 50%-250%
- **Template Favorites** — star button on each template thumbnail for quick access; favorited templates reorder to the front via CSS `order: -1`; persisted in localStorage
- **Generation Sound** — soft "print" click sound synthesized via Web Audio API, plays on Print, Download PNG, Download PDF, and Download SVG actions

### Changed
- Version bumped to v12.0
- SW cache bumped to `wificard-v12.0`

### Preserved
- All v11.x features intact (onboarding steps, card breathing shadow)

---

## v11.0 (2026-03-29) — Micro-improvements

### Added
- **Onboarding 3-steps visual** — numbered step circles (1 Enter WiFi, 2 Pick a style, 3 Print or share) with arrow connectors shown above the feature list in the onboarding overlay
- **Card breathing shadow** — idle card preview pulses with a subtle indigo glow via `cardBreathe` keyframes (3s cycle), giving the card a living feel

### Changed
- Version bumped to v11.0
- SW cache bumped to `wificard-v11.0`

### Preserved
- All v10.x features intact (print toast, card count badge, welcome placeholder)

---

## v10.0 (2026-03-29)
### Added
- **Welcome message placeholder** — placeholder text changed to "e.g. Welcome! Enjoy your stay" for a more helpful hint
- **Print toast** — clicking Print shows a small toast notification "Tip: Choose 'Save as PDF' for digital sharing" that auto-dismisses after 3.5 seconds
- **Card count badge** — when batch mode is active (2+ networks), a pill badge "1 of N cards" appears below the card preview

### Changed
- Version bumped to v10.0
- SW cache bumped to `wificard-v10.0`

---

## v9.0 (2026-03-29)
### Added
- **Card Hover Parallax Effect** — subtle 3D tilt on the preview card when hovering; CSS perspective (800px) with rotateX/rotateY driven by mouse position (max ±5deg); smooth reset transition on mouse leave
- **Share on X Button** — "Share on X" action button opens a pre-filled tweet ("Just made a beautiful WiFi card for my guests") with link to WifiCard; uses X intent URL in a popup window
- **Card Stats Info Bar** — small info bar below the preview card showing current card dimensions, QR size, and active template name; auto-updates on template switch, card resize, and all renders

### Changed
- Version bumped to v9.0
- SW cache bumped to `wificard-v9.0`

---

## v8.0 (2026-03-29)
### Added
- **Batch Mode** — "Add Another Network" button saves current form as a batch entry (up to 3 networks total); batch list shown below form with remove buttons; Print renders all cards on one page for multi-network households/venues
- **QR Error Correction Level** — dropdown to select Low (7%), Medium (15%, default), or High (30%) error correction; higher levels make QR codes scannable even when partially obscured or damaged
- **Card Flip Animation** — template switching now uses a 3D flip transition (rotateY) instead of fade, giving a card-turning visual effect

### Changed
- Version bumped to v8.0
- SW cache bumped to `wificard-v8.0`
- QR generation now respects selected error correction level across all renders (HTML card, PNG export, SVG export)

---

## v7.0 (2026-03-29)
### Added
- **Auto-save form (complete)** — security type and hidden network checkbox now also persisted in localStorage alongside SSID, password, welcome, note, template, and accent color; all fields fully restored on load
- **PWA install prompt** — custom banner appears after 2+ visits when the browser fires `beforeinstallprompt`; "Install" triggers native install flow, "Not now" dismisses and remembers choice in localStorage
- **Keyboard shortcuts** — `Ctrl+P` / `Cmd+P` triggers Print/PDF, `Ctrl+D` / `Cmd+D` triggers Download PNG; shortcut hints displayed as `<kbd>` badges on the action buttons

### Changed
- Version bump to v7.0
- SW cache bumped to wificard-v7.0
- Hidden network checkbox now triggers `scheduleUpdateAndSave` (was `scheduleUpdate` only)

## v6.0 (2026-03-29)
### Added
- **Template Scroll Indicator** — fade-out gradient and animated arrow hint on the right edge of the template gallery when more templates are scrollable (mobile); disappears once user scrolls to the end
- **QR Crossfade Animation** — smooth opacity transition (fade-out 150ms, fade-in 250ms) when QR code updates on input changes, instead of instant redraw
- **Card Size Selector** — radio buttons to choose "A6 Card" (148x105mm, default), "Business Card" (85x55mm), or "Custom" (user-defined width/height in mm); updates `--card-width`, `--card-height`, and `--qr-size` CSS custom properties in real time
- **Focus-visible on all interactive elements** — added `:focus-visible` outline to dark mode toggle, password visibility toggle, clear settings button, accent reset button, header logo, onboarding CTA, form inputs, selects, welcome field, note field, custom size inputs, and card size radio buttons

### Changed
- Version bump to v6.0
- SW cache bumped to wificard-v6.0
- Template gallery wrapped in `.template-scroll-wrapper` container for scroll indicator positioning

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

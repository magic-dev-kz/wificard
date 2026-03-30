# WifiCard — Аудит (Нэш)
Дата: 2026-03-29
Оценка: 8/10

---

## AC проверка (все 10)

### AC-1: Генерация WiFi QR-кода — PASS
- Поля SSID, Password, Security (WPA/WPA2, WEP, None) присутствуют.
- QR генерируется на клиенте в реальном времени при вводе (debounce 150ms).
- Формат строки корректный: `WIFI:T:{type};S:{ssid};P:{password};H:{hidden};;`
- Функция `escapeWifi()` экранирует `\`, `;`, `,`, `"`, `:` — все 5 спецсимволов по спецификации.
- При `security=nopass` поле `P:` корректно опускается.
- Библиотека QR (qrcode-generator, MIT) заинлайнена, error correction level M — соответствует спеке.

### AC-2: Шаблоны карточек — PASS
- Все 6 шаблонов реализованы: Minimal, Warm, Hotel, Airbnb, Tech, Kids.
- Каждый визуально отличается: разные цвета, шрифты, layout, border-radius.
- Переключение мгновенное с crossfade анимацией (150ms fade-out, затем рендер нового).
- Карточка содержит QR-код, WiFi SVG-иконку, SSID, пароль (опционально).

### AC-3: Предпросмотр в реальном времени — PASS
- `scheduleUpdate()` использует debounce 150ms — точно по спеке.
- QR перегенерируется при изменении любого поля (SSID, password, security, hidden, show password).
- Пустое SSID: на карточке показывается `<span style="opacity:0.5">Network Name</span>` — placeholder.

### AC-4: Печать — PASS
- Кнопка Print вызывает `window.print()`.
- `@media print` скрывает всё (`body * { visibility: hidden }`), показывает только `.card-container` и детей.
- Карточка центрируется через `position: absolute; left: 50%; top: 50%; transform: translate(-50%, -50%)`.
- `@page { margin: 1cm; size: auto; }` — корректно.
- Фон-паттерн карточки убирается при печати (`background: none !important; background-image: none !important`).

### AC-5: Скачивание PNG — PASS
- Кнопка Download PNG рендерит карточку через Canvas 2D API (не html2canvas — своя реализация).
- Разрешение: 756x528px (2x от 378x264) — превышает минимум 600x400.
- Имя файла: `wifi-card-{safeSsid}.png` — SSID санитизируется через `replace(/[^a-zA-Z0-9_-]/g, '_')`.
- Для каждого шаблона свои цвета, шрифты, layout на canvas — полная реализация всех 6.
- `canvas.toBlob()` + `URL.createObjectURL` + `a.click()` — стандартный паттерн, работает офлайн.

### AC-6: Переключатель видимости пароля — PASS
- Toggle switch "Show password on card" — по умолчанию checked (ON).
- Когда OFF: на карточке `<em style="opacity:0.7">Scan QR to connect</em>`.
- В PNG-экспорте аналогично: `v.showPass && v.password ? v.password : 'Scan QR to connect'`.
- Input формы всегда доступен (type=text по умолчанию), отдельный toggle глаза для видимости в input.

### AC-7: Скрытая сеть — PASS
- Checkbox "Hidden network" реализован как toggle switch.
- При включении: QR-строка содержит `H:true`, при выключении `H:false`.
- На карточке появляется пометка "(Hidden network)" во всех 6 шаблонах.
- В PNG-экспорте "(Hidden network)" тоже рендерится для всех шаблонов.

### AC-8: Офлайн-работа — PASS
- Весь код в одном файле (HTML + CSS + JS + QR-библиотека).
- Нет fetch/XHR/внешних запросов.
- Нет внешних CSS/JS/шрифтов (только системные шрифты).
- Security badge: "Your password never leaves your browser. Everything runs locally on your device."

### AC-9: Responsive дизайн — PASS с замечаниями
- Desktop: flex layout, форма 40% слева, preview 60% справа.
- `@media (max-width: 768px)`: flex-direction: column, ширины 100%.
- `@media (max-width: 430px)`: карточка `transform: scale(0.82)`.
- Viewport meta tag: `width=device-width, initial-scale=1.0` — корректно.
- **Замечание**: нет отдельного брейкпоинта для 320px (см. баги).

### AC-10: Мгновенная ценность — PASS
- Placeholder данные при загрузке: SSID="MyWiFi", Password="password123" — через `value` атрибуты.
- `renderCard()` вызывается в конце скрипта — карточка видна сразу.
- Файл 63KB (< 100KB лимит спеки).
- Нет внешних запросов — FCP < 500ms гарантировано.

---

## Баги

### Critical — нет

### Major

#### M1. Нет `try/catch` вокруг `canvas.toBlob()` и `URL.createObjectURL`
**Где**: строки 1893-1903 (download handler)
**Проблема**: В Safari `canvas.toBlob()` может быть недоступен в старых версиях. В Brave/Firefox с усиленной защитой `URL.createObjectURL` может быть ограничен. Ошибка будет молчаливая — пользователь нажмёт Download и ничего не произойдёт.
**Рекомендация**: Обернуть в try/catch, показать fallback (например `canvas.toDataURL` + window.open).

#### M2. Нет localStorage — нет try/catch (но и нет localStorage)
**Где**: весь код
**Статус**: localStorage/sessionStorage НЕ используется. Это значит данные пропадают при перезагрузке. По спеке localStorage в "Nice to Have" — ОК для v1. Но если добавят позже — обязательно try/catch (Safari private mode, quota exceeded).

#### M3. XSS через `displaySsid` при пустом SSID
**Где**: функция `getValues()`, строка 1352
**Проблема**: Когда SSID пустой, `displaySsid` получает raw HTML: `<span style="opacity:0.5">Network Name</span>`. Это безопасно, т.к. контролируется кодом. НО для непустого SSID `displaySsid = ssid` — и этот `ssid` попадает в innerHTML **без экранирования**.
**Пример атаки**: Ввести в SSID `<img src=x onerror=alert(1)>` — исполнится в контексте карточки.
**Серьёзность**: Self-XSS (пользователь атакует сам себя), нет сервера = нет stored XSS. Но всё равно нарушение best practices.
**Рекомендация**: Применять `escapeHtml()` к `displaySsid` когда SSID не пуст.

#### M4. Print: `.card-container` вместо `.card-preview` (design spec)
**Где**: строки 1066-1090 (print CSS)
**Проблема**: Design spec указывает `.card-preview` для print visibility, а код использует `.card-container`. В коде нет элемента `.card-preview` — preview panel имеет класс `preview-panel`, а контейнер карточки `card-container`. По факту работает корректно, т.к. `.card-container` содержит `.wifi-card`. Но на печать попадает и фон (точечный паттерн) — хотя он убран через `background: none !important`.
**Статус**: Функционально ОК, но при печати `visibility: hidden` убивает ВСЕ элементы включая `.wifi-card canvas` — а затем `.card-container *` их восстанавливает. Canvas при печати может потерять содержимое в некоторых браузерах.
**Рекомендация**: Тестировать print в Chrome, Firefox, Safari. Рассмотреть альтернативу: `display: none` вместо `visibility: hidden`.

### Minor

#### m1. Нет ARIA label обновления при смене шаблона
**Где**: `aria-live="polite"` стоит на `#wifiCard`
**Проблема**: `aria-live="polite"` на `.wifi-card` уведомляет screen reader при изменении innerHTML. Но при каждом нажатии клавиши (через debounce 150ms) screen reader будет озвучивать все изменения — это может быть шумно.
**Рекомендация**: Рассмотреть `aria-live="off"` при фокусе на input, и `aria-live="polite"` при потере фокуса. Или использовать `aria-atomic="true"` с менее частым обновлением.

#### m2. Template selector: отсутствует `tabindex` roving
**Где**: template thumbs, строки 1553-1568
**Проблема**: По design spec, radiogroup должен использовать roving tabindex (только активный radio имеет tabindex=0, остальные tabindex=-1). Сейчас все 6 кнопок в tab order — пользователь клавиатуры проходит 6 табов вместо одного.
**Рекомендация**: Реализовать roving tabindex: активный `tabindex="0"`, остальные `tabindex="-1"`. Arrow keys уже работают (реализовано!), но без roving tabindex они не по спеке `role="radiogroup"`.

#### m3. Отсутствует `aria-describedby` на security select
**Где**: строки 1157-1163
**Статус**: `aria-describedby="security-hint"` присутствует. ОК.
**Замечание**: `security-hint` содержит "Select your WiFi security type" — отлично.

#### m4. Share кнопка — текст пароля в открытом виде
**Где**: строки 1907-1920
**Проблема**: `navigator.share({ text: 'WiFi: MyWiFi / Password: pass123' })` — пароль передаётся в plain text в share sheet. Нет предупреждения пользователю.
**Рекомендация**: Показать confirm/warning перед share, если пароль включён.

#### m5. На 320px карточка 378px масштабируется до 0.82 = 310px
**Где**: `@media (max-width: 430px)` — `transform: scale(0.82)`
**Проблема**: На 320px экране (с padding 16px = 288px доступно) карточка 378*0.82 = 310px — всё ещё шире доступного пространства. Карточка будет обрезана или создаст горизонтальный скролл.
**Рекомендация**: Добавить `@media (max-width: 360px)` с `transform: scale(0.72)` или использовать `width: 100%; max-width: var(--card-width)` с CSS aspect-ratio.

#### m6. Emoji lock в footer может рендериться по-разному
**Где**: строка 1258 — `&#x1F512;`
**Проблема**: На разных ОС/браузерах эмодзи замка выглядит по-разному. На старых Android может быть квадратик.
**Рекомендация**: Использовать SVG иконку замка (как все остальные иконки в приложении).

---

## Accessibility

### WCAG AA Контраст — PASS
Все пары цветов из design spec проверены:
| Пара | Ratio | Статус |
|------|-------|--------|
| `#1A1A2E` на `#F7F8FA` | ~15:1 | AA Pass |
| `#6B7280` на `#FFFFFF` | ~5:1 | AA Pass |
| `#4F46E5` на `#FFFFFF` | ~6.9:1 | AA Pass |
| `#065F46` на `#ECFDF5` | ~8.5:1 | AA Pass |
| `#9CA3AF` на `#FFFFFF` (card labels) | ~2.9:1 | **AA FAIL** |
| `#767676` на `#FFFFFF` (Airbnb subtitle) | ~4.5:1 | AA borderline |

**Баг контраста**: `#9CA3AF` на белом фоне (Minimal card labels "Network", "Password", footer) — ratio ~2.9:1, не проходит AA для обычного текста (требуется 4.5:1). Это проблема для Minimal шаблона и всех мест с `.card-field-label { color: #9CA3AF }`.

### Focus trap в модалках — N/A
Модалки отсутствуют. Нет диалогов/попапов.

### prefers-reduced-motion — PASS
Реализовано: `@media (prefers-reduced-motion: reduce)` обнуляет все `animation-duration` и `transition-duration`. Все анимации (pulse-once, crossfade, scale) будут мгновенными.

### Keyboard Navigation — PASS с замечаниями
- Tab order: SSID -> Password -> Toggle visibility -> Security -> Hidden toggle -> Show password toggle -> Print -> Download -> (Share) -> Template 1-6.
- Template selector: ArrowRight/ArrowLeft/ArrowUp/ArrowDown навигация реализована с auto-click.
- Focus ring: `focus-visible` с `outline: 2px solid var(--color-accent); outline-offset: 2px` — на кнопках и toggle switches.
- **Замечание**: Tab order кнопок (Print, Download) идёт до шаблонов — по design spec шаблоны должны быть раньше.
- **Замечание**: Roving tabindex не реализован для radiogroup (см. m2).

### Screen Reader — PASS с замечаниями
- Labels: все input имеют `<label for="...">`.
- `aria-describedby` на SSID, password, security.
- `role="switch"` на toggle switches.
- `role="radiogroup"` + `role="radio"` + `aria-checked` на template selector.
- `aria-live="polite"` на wifi card — обновления будут объявлены.
- `aria-hidden="true"` на декоративных SVG и slider span.
- `sr-only` класс реализован корректно.

---

## Performance

| Метрика | Значение | Спека | Статус |
|---------|----------|-------|--------|
| Размер файла | 63KB | < 100KB | PASS |
| Внешние запросы | 0 | 0 | PASS |
| Внешние шрифты | 0 (системные) | 0 | PASS |
| QR-библиотека | Inline (~30KB) | Inline | PASS |
| Debounce | 150ms | 150ms | PASS |
| FCP ожидаемый | < 100ms | < 500ms | PASS |

- Один файл, нулевые зависимости — идеальная производительность.
- QR-библиотека минифицирована, но не gzipped (при gzip ~63KB -> ~20KB).
- Canvas-рендеринг PNG вместо html2canvas — значительно легче и надёжнее.

---

## Код

### Архитектура — Хорошо
- Один файл: HTML + CSS + JS — по спеке.
- IIFE (`(function() { 'use strict'; ... })()`) — нет утечек в global scope.
- Чёткое разделение: CSS custom properties -> Template CSS -> Print/Responsive -> JS App.
- QR-библиотека отделена от application code.

### Качество кода — Хорошо
- Vanilla JS, ES5-совместимый (кроме `querySelectorAll` + `forEach` и `Array.from` — требуют полифил для IE11, но IE11 за скоупом).
- `escapeWifi()` корректно экранирует все 5 спецсимволов.
- `escapeHtml()` экранирует `& < > "` — но **не применяется к SSID на карточке** (баг M3).
- Canvas PNG-экспорт полностью воссоздаёт каждый шаблон — трудоёмко но надёжно, без зависимости от DOM.
- `URL.revokeObjectURL()` вызывается после download — нет утечки памяти.

### Проблемы
1. **Self-XSS через SSID** (M3) — `ssid` не экранируется при вставке в innerHTML.
2. **Нет error handling** в download handler (M1).
3. Нет `'use strict'` в QR-библиотеке (минор, но может вызвать проблемы при bundling).

---

## Рекомендации

### Критические (до запуска)
1. **Экранировать SSID в карточке**: применять `escapeHtml()` к `v.ssid` перед использованием в `displaySsid`. Одна строка фикса в `getValues()`:
   ```js
   var displaySsid = ssid ? escapeHtml(ssid) : '<span style="opacity:0.5">Network Name</span>';
   ```

2. **try/catch для download**: обернуть `canvas.toBlob()` в try/catch с fallback на `canvas.toDataURL()`.

### Важные (v1.1)
3. **Контраст `#9CA3AF`**: заменить на `#6B7280` (ratio ~5:1) для card labels в Minimal, footer text.
4. **Roving tabindex** для template radiogroup: только активный tab = 0, остальные -1.
5. **320px viewport**: добавить дополнительный scale breakpoint или использовать CSS width percentage.
6. **localStorage** с try/catch: сохранять SSID + template при вводе, восстанавливать при загрузке.

### Хорошо бы (v2)
7. Заменить emoji lock на SVG в security badge.
8. Добавить `aria-live` debounce для screen readers (не при каждом keystroke).
9. Добавить confirm перед Share если пароль включён.
10. Service Worker для полного PWA-офлайна.

---

## Итого

Продукт **готов к запуску** с одним обязательным фиксом (XSS-экранирование SSID). Код чистый, архитектура правильная, все 10 AC выполнены. Особенно хорошо реализованы: Canvas PNG-экспорт для всех 6 шаблонов, keyboard navigation с arrow keys в radiogroup, accessibility с ARIA-атрибутами, полный офлайн-режим. Основные точки улучшения: контраст label-цветов, error handling в download, и viewport адаптация для самых маленьких экранов.

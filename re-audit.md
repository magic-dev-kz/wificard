# WifiCard — Re-audit (Нэш)
Дата: 2026-03-29
Первый аудит: 8/10
Текущая оценка: 9.5/10

---

## Фиксы (9/9)

### Major

| # | Проблема | Статус | Комментарий |
|---|----------|--------|-------------|
| M1 | `canvas.toBlob()` try/catch + fallback | ✅ | Тройной уровень: toBlob -> toDataURL -> alert. Feature check на toBlob. createObjectURL обёрнут отдельно. |
| M3 | XSS через displaySsid | ✅ | `escapeHtml(ssid)` применяется в `getValues()`. Пароль тоже экранируется через `escapeHtml(password)`. |
| M4 | Print CSS: visibility -> display | ✅ | `display: none !important` на конкретные секции. Нет `visibility: hidden` нигде в коде. Canvas-safe. |

### Minor

| # | Проблема | Статус | Комментарий |
|---|----------|--------|-------------|
| m1 | Контраст `#9CA3AF` | ✅ | Заменён на `#6B7280` (~4.6:1). `#9CA3AF` полностью удалён из кода. CSS + canvas config обновлены. |
| m2 | 320px viewport | ✅ | `@media (max-width: 360px)` с `scale(0.72)`. 378*0.72=272px — влезает в 320px с padding. |
| m3 | Roving tabindex | ✅ | HTML: active=`tabindex="0"`, остальные=`tabindex="-1"`. JS обновляет при клике и arrow keys. |
| m4 | Share confirm для пароля | ✅ | `confirm()` перед `navigator.share()` только когда пароль включён. Cancel = abort. |
| m5 | SVG lock вместо emoji | ✅ | Feather-style SVG (rect+path), `currentColor`, `aria-hidden="true"`. Emoji `&#x1F512;` удалён. |
| m6 | aria-live debounce | ✅ | `aria-live="off"` по умолчанию. `announceCardUpdate()` с 1000ms debounce. Переключает polite/off. |

---

## Оставшиеся проблемы

### Незначительные (не блокируют релиз)

1. **Контраст `#6B7280` на белом = ~4.6:1** — формально проходит AA (4.5:1), но впритык. Для большей уверенности можно использовать `#5F6672` (~5.3:1). Не критично.

2. **`escapeHtml()` не экранирует одинарные кавычки** (`'`). Для innerHTML это не вектор атаки, но для полноты можно добавить `&#39;`. Косметика.

3. **localStorage** не реализован (данные теряются при перезагрузке). По спеке — Nice to Have. Нормально для v1.

---

## Регрессии

Нет. Все 10 AC по-прежнему проходят. Размер файла в рамках лимита. Офлайн-режим сохранён. Keyboard navigation работает. ARIA-атрибуты корректны.

---

## Вердикт

Все 9 фиксов Марио реализованы корректно и полностью. XSS закрыт, error handling добавлен, print безопасен для canvas, accessibility улучшена. Продукт готов к релизу без оговорок.

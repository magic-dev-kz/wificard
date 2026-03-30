# WifiCard — Design Specification
**Product #10 | OpenClaw Sandbox**
**Date:** 2026-03-29
**Author:** Скай (Creative Designer)

---

## 1. Общая концепция

### Настроение
Чистый, спокойный, доверительный. Пользователь вводит пароль от WiFi — интерфейс должен чувствоваться как сейф, а не как вечеринка. Минимум отвлекающих элементов, максимум фокуса на результате (карточке).

### Цветовая палитра UI

| Роль | Цвет | HEX |
|------|-------|-----|
| Background | Светло-серый | `#F7F8FA` |
| Surface (форма, панели) | Белый | `#FFFFFF` |
| Text Primary | Почти-чёрный | `#1A1A2E` |
| Text Secondary | Серый | `#6B7280` |
| Accent / CTA | Индиго | `#4F46E5` |
| Accent Hover | Тёмный индиго | `#4338CA` |
| Success | Зелёный | `#059669` |
| Border | Светлый серый | `#E5E7EB` |
| Security badge bg | Мятный | `#ECFDF5` |
| Security badge text | Тёмно-зелёный | `#065F46` |

Почему индиго: нейтральный, профессиональный, не конфликтует ни с одним шаблоном карточки. Карточки — главный визуал, UI не должен перетягивать внимание.

### Типографика

```
--font-ui: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
--font-mono: ui-monospace, "SF Mono", "Cascadia Code", "Segoe UI Mono", Consolas, monospace;
```

| Элемент | Размер | Вес | Шрифт |
|---------|--------|-----|-------|
| Заголовок h1 | 28px | 700 | UI |
| Подзаголовок h2 | 18px | 600 | UI |
| Label формы | 14px | 500 | UI |
| Input текст | 16px | 400 | UI |
| Hint / описание | 13px | 400 | UI |
| Кнопка | 15px | 600 | UI |
| Security badge | 13px | 500 | UI |

Размер input — строго 16px (предотвращает auto-zoom на iOS Safari).

---

## 2. Layout

### Desktop (>768px)

```
┌──────────────────────────────────────────────────────────┐
│  🔒 WifiCard                              [Security ▸]  │
├────────────────────┬─────────────────────────────────────┤
│                    │                                     │
│   ФОРМА ВВОДА      │     ПРЕДПРОСМОТР КАРТОЧКИ           │
│   (40% ширины)     │     (60% ширины)                    │
│                    │                                     │
│   ┌──────────┐    │     ┌─────────────────────┐         │
│   │ SSID     │    │     │                     │         │
│   └──────────┘    │     │   WIFI CARD         │         │
│   ┌──────────┐    │     │   PREVIEW           │         │
│   │ Password │    │     │                     │         │
│   └──────────┘    │     └─────────────────────┘         │
│   ┌──────────┐    │                                     │
│   │ Security │    │     ┌─Print──┬─PNG──┬─Share─┐       │
│   └──────────┘    │     └───────┴──────┴───────┘       │
│   □ Hidden net    │                                     │
│   ☑ Show pass     │                                     │
│                    │                                     │
├────────────────────┴─────────────────────────────────────┤
│  [ Min ][ Warm ][ Hotel ][ Airbnb ][ Tech ][ Kids ]     │
│  ◄──────────── template selector scroll ──────────────►  │
├──────────────────────────────────────────────────────────┤
│  🔒 Your password never leaves your browser              │
└──────────────────────────────────────────────────────────┘
```

- Форма: `width: 40%; padding: 32px;` в белом блоке с `border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.08);`
- Предпросмотр: `width: 60%;` — карточка центрирована вертикально и горизонтально, фон `#F7F8FA` с точечным паттерном (как в Figma, показывает что это "холст")
- Между формой и превью — зазор 24px
- Шаблоны — горизонтальная полоса под основным контентом (full width)

### Mobile (<768px)

```
┌──────────────────────┐
│  🔒 WifiCard         │
├──────────────────────┤
│                      │
│  ФОРМА ВВОДА          │
│  (100% ширины)        │
│                      │
├──────────────────────┤
│ [Min][Warm][Hotel]►  │
│  template scroll     │
├──────────────────────┤
│                      │
│  ПРЕДПРОСМОТР        │
│  КАРТОЧКИ             │
│  (100%, scale-to-fit) │
│                      │
├──────────────────────┤
│ [Print] [PNG] [Share]│
├──────────────────────┤
│ 🔒 Password safe     │
└──────────────────────┘
```

- Форма: `padding: 20px;`
- Шаблоны: между формой и превью (пользователь сначала заполняет, потом выбирает стиль, потом видит результат)
- Карточка масштабируется через `transform: scale()` чтобы помещаться в экран, сохраняя пропорции
- Кнопки действий — sticky bar внизу экрана

### Selector шаблонов (Instagram-style)

```
┌──────────────────────────────────────────────────────┐
│ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ...    │
│ │▓▓▓▓▓▓▓▓│ │▒▒▒▒▒▒▒▒│ │████████│ │░░░░░░░░│        │
│ │▓ Mini ▓│ │▒ Warm ▒│ │█Hotel █│ │░Airbnb░│        │
│ │▓▓▓▓▓▓▓▓│ │▒▒▒▒▒▒▒▒│ │████████│ │░░░░░░░░│        │
│ └────────┘ └────────┘ └────────┘ └────────┘        │
│  Minimal    Warm       Hotel      Airbnb            │
└──────────────────────────────────────────────────────┘
```

- Контейнер: `overflow-x: auto; scroll-snap-type: x mandatory; gap: 12px; padding: 16px 0;`
- Каждый превью: `width: 100px; height: 70px; border-radius: 8px; scroll-snap-align: start;`
- Превью — уменьшенная копия карточки (не иконка, а реальный мини-рендер)
- Активный шаблон: `outline: 2px solid #4F46E5; outline-offset: 2px;`
- Неактивные: `opacity: 0.7;` при hover `opacity: 1; transform: scale(1.05);`
- Подпись шаблона: `font-size: 12px; color: #6B7280;` под каждым превью
- Скрыт scrollbar: `-webkit-scrollbar: none; scrollbar-width: none;`

---

## 3. Форма ввода

### Общие стили input

```css
input, select {
  width: 100%;
  height: 48px;
  padding: 12px 16px;
  border: 1px solid #E5E7EB;
  border-radius: 8px;
  font-size: 16px;
  color: #1A1A2E;
  background: #FFFFFF;
  transition: border-color 0.15s, box-shadow 0.15s;
}

input:focus, select:focus {
  outline: none;
  border-color: #4F46E5;
  box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.1);
}
```

### Поля

#### WiFi Network Name (SSID)
- Label: `"Network name (SSID)"`
- Placeholder: `"MyWiFi"`
- Первое поле, autofocus
- Максимальная длина: 32 символа
- Иконка WiFi слева внутри input (padding-left: 44px)

#### Password
- Label: `"Password"`
- Placeholder: `"Enter WiFi password"`
- `type="text"` (не password — пользователь вводит пароль для карточки, не для авторизации)
- Справа — кнопка toggle видимости (иконка глаз)
- Toggle: при клике переключает `type` между `text` и `password`
- Иконка глаза: SVG inline, 20x20px, цвет `#6B7280`, при hover `#1A1A2E`

#### Security Type
- Label: `"Security"`
- Dropdown `<select>` с опциями:
  - `WPA/WPA2` (default, selected)
  - `WEP`
  - `None`
- При выборе "None" — поле Password прячется с анимацией collapse

#### Hidden Network
- Toggle switch (не чекбокс — более современно)
- Label: `"Hidden network"`
- По умолчанию: OFF
- Размер switch: 44x24px (тач-friendly)
- Цвет ON: `#4F46E5`, OFF: `#D1D5DB`

#### Show Password on Card
- Toggle switch
- Label: `"Show password on card"`
- По умолчанию: ON
- Когда OFF — на карточке вместо пароля текст "Scan QR to connect"

### Расстояния между полями
- Между label и input: 6px
- Между полями: 20px
- Между группами (inputs vs toggles): 28px

---

## 4. Шаблоны карточек

### Общие параметры
- Размер карточки: `378 x 264px` (соотношение ~10:7 см при 96dpi)
- Border-radius контейнера: зависит от шаблона (4-16px)
- QR-код: `120 x 120px` (достаточно для надёжного сканирования)
- Иконка WiFi: CSS-only (три дуги + точка, через pseudo-элементы или SVG inline)

### WiFi иконка (CSS-only, общая для всех шаблонов)

```css
.wifi-icon {
  width: 24px;
  height: 24px;
  position: relative;
}
/* Три дуги через box-shadow или border на вложенных элементах */
/* Цвет адаптируется к шаблону через currentColor */
```

---

### Шаблон 1: Minimal

**Настроение:** Чистый, нейтральный, универсальный. Как хорошо отформатированный документ.

| Параметр | Значение |
|----------|----------|
| Фон | `#FFFFFF` |
| Текст | `#111827` |
| Вторичный текст | `#6B7280` |
| Рамка | `1px solid #E5E7EB` |
| Border-radius | `8px` |
| Шрифт | System sans-serif |
| QR foreground | `#111827` |
| QR background | `#FFFFFF` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│                                      │
│  ╔══════╗   WiFi                     │
│  ║  QR  ║                            │
│  ║ CODE ║   Network: MyWiFi          │
│  ║      ║   Password: ********       │
│  ╚══════╝                            │
│                                      │
│  ─────────────────────────────────   │
│  Scan QR code to connect             │
└──────────────────────────────────────┘
```

- QR слева (padding: 24px), текст справа
- WiFi иконка рядом с заголовком "WiFi" (цвет `#111827`)
- Разделитель: тонкая линия `1px solid #E5E7EB`
- Нижний текст "Scan QR code to connect" — `12px`, цвет `#9CA3AF`
- SSID: `font-weight: 600; font-size: 16px;`
- Password: `font-size: 14px; color: #6B7280;`

---

### Шаблон 2: Warm

**Настроение:** Уютный, крафтовый, как меню в хорошем кафе. Тёплый и приглашающий.

| Параметр | Значение |
|----------|----------|
| Фон | `#FDF6EC` (тёплый кремовый) |
| Текст | `#5D4037` (тёмно-коричневый) |
| Вторичный текст | `#8D6E63` |
| Акцент | `#D84315` (терракотовый) |
| Рамка | `2px solid #D7CCC8` |
| Border-radius | `4px` |
| Шрифт | Georgia, "Times New Roman", serif (системный serif) |
| QR foreground | `#5D4037` |
| QR background | `#FDF6EC` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│                                      │
│          ☕  WiFi                     │
│       ──────────────                 │
│                                      │
│       Network: MyWiFi                │
│       Password: ********             │
│                                      │
│          ╔══════╗                    │
│          ║  QR  ║                    │
│          ║ CODE ║                    │
│          ╚══════╝                    │
│       Scan to connect                │
└──────────────────────────────────────┘
```

- QR по центру внизу
- Текст по центру сверху
- WiFi иконка заменена на стилизованную (3 дуги, цвет `#D84315`)
- Декоративная линия под заголовком: `border-top: 1px solid #D7CCC8`
- Весь текст center-aligned
- SSID: `font-size: 18px; font-weight: bold; font-family: Georgia;`
- Подпись "Scan to connect": `font-style: italic; font-size: 12px; color: #8D6E63;`

---

### Шаблон 3: Hotel

**Настроение:** Премиальный, как карточка-ключ в 5-звёздочном отеле. Тёмный, уверенный, с золотым акцентом.

| Параметр | Значение |
|----------|----------|
| Фон | `#1A1A2E` (тёмно-синий/чёрный) |
| Текст | `#D4AF37` (золотой) |
| Вторичный текст | `#C0C0C0` (серебро) |
| Акцент | `#D4AF37` |
| Рамка | `1px solid #D4AF37` |
| Border-radius | `4px` |
| Шрифт | Georgia, "Palatino Linotype", serif |
| QR foreground | `#D4AF37` |
| QR background | `#1A1A2E` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│
│▓                                    ▓│
│▓  WiFi                ╔══════╗      ▓│
│▓  ════                ║  QR  ║      ▓│
│▓                      ║ CODE ║      ▓│
│▓  Network: MyWiFi     ║      ║      ▓│
│▓  Password: ********  ╚══════╝      ▓│
│▓                                    ▓│
│▓   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─   ▓│
│▓   Scan QR code to connect to WiFi  ▓│
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│
└──────────────────────────────────────┘
```

- Тёмный фон по всей карточке
- QR справа, текст слева
- Заголовок "WiFi" с декоративной золотой линией снизу (`border-bottom: 2px solid #D4AF37; width: 40px;`)
- WiFi иконка золотая
- Нижний текст — по центру, серебряный, `letter-spacing: 1px; text-transform: uppercase; font-size: 10px;`
- QR-код золотой на тёмном фоне (инвертированный, выглядит премиально)
- Inner padding: 28px

---

### Шаблон 4: Airbnb

**Настроение:** Дружелюбный, гостеприимный. "Добро пожаловать! Вот вам WiFi." Яркий, но не кричащий.

| Параметр | Значение |
|----------|----------|
| Фон | `#FFFFFF` |
| Заголовок | `#FF5A5F` (Airbnb red / coral) |
| Текст | `#484848` |
| Вторичный текст | `#767676` |
| Акцент фон | `#FF5A5F` (верхняя полоса) |
| Border-radius | `12px` |
| Шрифт | System sans-serif |
| QR foreground | `#484848` |
| QR background | `#FFFFFF` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│█████████████████████████████████████ │ <- цветная полоса
│                                      │
│  Welcome! 👋                         │
│  Connect to our WiFi                 │
│                                      │
│  ╔══════╗   Network: MyWiFi          │
│  ║  QR  ║   Password: ********       │
│  ║ CODE ║                            │
│  ╚══════╝   Scan QR with your phone  │
│              to connect instantly     │
└──────────────────────────────────────┘
```

- Верхняя акцентная полоса: `height: 6px; background: #FF5A5F; border-radius: 12px 12px 0 0;`
- Welcome текст: `font-size: 20px; font-weight: 700; color: #FF5A5F;`
- Подзаголовок "Connect to our WiFi": `font-size: 14px; color: #767676;`
- QR слева, текст справа (как Minimal, но с welcome-блоком сверху)
- WiFi иконка цвета `#FF5A5F`
- Мягкая тень: `box-shadow: 0 2px 12px rgba(0,0,0,0.08);`
- Без жёсткой рамки (тень вместо border)

---

### Шаблон 5: Tech

**Настроение:** Хакерский, современный, как терминал. Для коворкингов и IT-офисов.

| Параметр | Значение |
|----------|----------|
| Фон | `#0D1117` (GitHub dark) |
| Текст | `#C9D1D9` (светло-серый) |
| Акцент | `#58A6FF` (неоновый синий) |
| Вторичный акцент | `#39D353` (зелёный, как terminal) |
| Рамка | `1px solid #30363D` |
| Border-radius | `6px` |
| Шрифт | ui-monospace, "SF Mono", Consolas, monospace |
| QR foreground | `#58A6FF` |
| QR background | `#0D1117` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│
│▓  $ wifi-connect           [icon]   ▓│
│▓  ─────────────────────────────     ▓│
│▓                                    ▓│
│▓  SSID     MyWiFi                   ▓│
│▓  PASS     ********                 ▓│
│▓                                    ▓│
│▓       ╔══════╗                     ▓│
│▓       ║  QR  ║  > scan to connect  ▓│
│▓       ║ CODE ║                     ▓│
│▓       ╚══════╝                     ▓│
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│
└──────────────────────────────────────┘
```

- Стилизация под терминал
- Заголовок: `"$ wifi-connect"` — моноширинный, с символом `$` как в bash, цвет `#39D353`
- Разделитель: `border-top: 1px solid #30363D;`
- Labels (SSID, PASS): `color: #58A6FF; text-transform: uppercase; font-size: 11px; letter-spacing: 2px;`
- Values: `color: #C9D1D9; font-size: 15px;`
- QR слева, подпись `"> scan to connect"` справа, моноширинным шрифтом, цвет `#39D353`
- WiFi иконка цвета `#58A6FF`
- Subtle glow на QR: `box-shadow: 0 0 20px rgba(88, 166, 255, 0.1);`

---

### Шаблон 6: Kids

**Настроение:** Весёлый, яркий, игривый. Как открытка для детского праздника.

| Параметр | Значение |
|----------|----------|
| Фон | `#FFF8E1` (светло-жёлтый) |
| Текст | `#5D4037` |
| Заголовок | `#E91E63` (розовый) |
| Акцент 1 | `#FF9800` (оранжевый) |
| Акцент 2 | `#4CAF50` (зелёный) |
| Акцент 3 | `#2196F3` (синий) |
| Рамка | `3px solid #FF9800` |
| Border-radius | `16px` |
| Шрифт | System sans-serif, bold |
| QR foreground | `#5D4037` |
| QR background | `#FFF8E1` |

**Layout карточки:**

```
┌──────────────────────────────────────┐
│                                      │
│      📶  WiFi for You!               │
│                                      │
│   Network:  MyWiFi                   │
│   Password: ********                 │
│                                      │
│          ╔══════╗                    │
│          ║  QR  ║                    │
│          ║ CODE ║                    │
│          ╚══════╝                    │
│     Point your camera here! 📱       │
│                                      │
└──────────────────────────────────────┘
```

- Все элементы по центру
- Заголовок "WiFi for You!" — `font-size: 22px; font-weight: 800; color: #E91E63;`
- WiFi иконка многоцветная: дуги чередуют `#FF9800`, `#4CAF50`, `#2196F3`
- Рамка — скруглённая, толстая, оранжевая
- QR по центру
- Нижний текст "Point your camera here!" — `font-size: 13px; font-weight: 600; color: #4CAF50;`
- Labels (Network, Password): `font-weight: 700; color: #5D4037;`
- Фон можно дополнить subtle паттерном: CSS repeating dots (`radial-gradient(circle, #FFE0B2 1px, transparent 1px)`, size: 20px 20px, opacity 0.3)

---

## 5. Кнопки действий

### Общий стиль кнопок

```css
.action-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  height: 44px;
  padding: 0 20px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s, transform 0.1s;
}

.action-btn:active {
  transform: scale(0.97);
}
```

### Print

| Параметр | Значение |
|----------|----------|
| Фон | `#4F46E5` |
| Текст | `#FFFFFF` |
| Hover | `#4338CA` |
| Иконка | Принтер (SVG inline, 18x18px) |
| Текст кнопки | `"Print"` |

Primary action. Самая заметная кнопка.

### Download PNG

| Параметр | Значение |
|----------|----------|
| Фон | `#FFFFFF` |
| Текст | `#1A1A2E` |
| Border | `1px solid #E5E7EB` |
| Hover | `background: #F9FAFB` |
| Иконка | Стрелка вниз в рамку (download icon, SVG inline, 18x18px) |
| Текст кнопки | `"Download PNG"` |

Secondary action. Outlined style.

### Share

| Параметр | Значение |
|----------|----------|
| Фон | `#FFFFFF` |
| Текст | `#1A1A2E` |
| Border | `1px solid #E5E7EB` |
| Hover | `background: #F9FAFB` |
| Иконка | Share icon (стрелка вверх из коробки, SVG inline, 18x18px) |
| Текст кнопки | `"Share"` |
| Видимость | Показывать только если `navigator.share` доступен |

Tertiary action. Тот же стиль что и Download.

### Расположение

- Desktop: горизонтально, под карточкой, center-aligned. Print первая (слева).
- Mobile: полная ширина, стопкой или в ряд. Print — primary (фиолетовая), остальные — outlined.
- Gap между кнопками: `12px`

---

## 6. Security Badge

### Внешний вид

```
┌─────────────────────────────────────────────────┐
│  🔒  Your password never leaves your browser.   │
│      Everything runs locally on your device.     │
└─────────────────────────────────────────────────┘
```

### Стили

```css
.security-badge {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  background: #ECFDF5;
  border: 1px solid #A7F3D0;
  border-radius: 8px;
  font-size: 13px;
  font-weight: 500;
  color: #065F46;
  line-height: 1.4;
}
```

### Расположение
- Desktop: в самом низу страницы, full-width, center-aligned текст
- Mobile: под кнопками действий, перед footer
- Иконка замка: эмодзи `🔒` (кросс-платформенно, не нужен SVG)
- Вторая строка ("Everything runs locally on your device.") — `color: #047857; font-weight: 400;`

### Дополнительно
- При первой загрузке badge слегка пульсирует один раз (привлечение внимания к безопасности)
- Анимация: `animation: pulse-once 0.6s ease 1s 1;` — одна пульсация через 1 секунду после загрузки

---

## 7. Микроанимации

### Переключение шаблонов — Crossfade

```css
.card-preview {
  transition: opacity 0.25s ease, transform 0.25s ease;
}
```

При переключении шаблона:
1. Текущая карточка: `opacity: 0; transform: scale(0.98);` (150ms)
2. Меняется CSS-класс
3. Новая карточка: `opacity: 1; transform: scale(1);` (150ms)

Вместо жёсткой замены — плавное затухание и появление. Нет дёргания.

### QR-код обновляется — Subtle fade

```css
.qr-canvas {
  transition: opacity 0.2s ease;
}
```

При перегенерации QR (debounce 150ms после ввода):
1. `opacity: 0.4;` (100ms)
2. Рендер нового QR
3. `opacity: 1;` (100ms)

Пользователь видит "мигание" — понимает что QR обновился.

### Hover на шаблонах — Scale up

```css
.template-thumb {
  transition: transform 0.15s ease, opacity 0.15s ease;
}

.template-thumb:hover {
  transform: scale(1.05);
  opacity: 1;
}

.template-thumb.active {
  transform: scale(1);
  opacity: 1;
}
```

### Кнопки — Press effect

```css
.action-btn:active {
  transform: scale(0.97);
}
```

Мгновенный feedback при нажатии — кнопка слегка "утапливается".

### Security badge — Pulse once

```css
@keyframes pulse-once {
  0%, 100% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); }
  50% { box-shadow: 0 0 0 6px rgba(16, 185, 129, 0.2); }
}

.security-badge {
  animation: pulse-once 0.6s ease 1s 1;
}
```

### Password visibility toggle — Icon swap

```css
.toggle-icon {
  transition: opacity 0.15s ease;
}
```

Иконка глаза (открытый/закрытый) — crossfade при переключении.

### Form field appearance on Security "None"

При выборе `Security: None` поле Password сворачивается:

```css
.password-field {
  transition: max-height 0.25s ease, opacity 0.2s ease, margin 0.25s ease;
  overflow: hidden;
}

.password-field.hidden {
  max-height: 0;
  opacity: 0;
  margin: 0;
}
```

---

## 8. Accessibility

### Touch Targets
- Все интерактивные элементы: минимум `44 x 44px`
- Кнопки: `height: 44px;` (уже задано)
- Toggle switches: `44 x 24px` — touch area расширена через padding
- Template thumbs: `100 x 70px` — более чем достаточно
- Dropdown: `height: 48px;`

### Контраст (WCAG AA — ratio 4.5:1 minimum)

| Пара | Ratio | Статус |
|------|-------|--------|
| `#1A1A2E` на `#F7F8FA` | 15.2:1 | AA Pass |
| `#6B7280` на `#FFFFFF` | 5.0:1 | AA Pass |
| `#4F46E5` на `#FFFFFF` | 6.9:1 | AA Pass |
| `#FFFFFF` на `#4F46E5` | 6.9:1 | AA Pass |
| `#065F46` на `#ECFDF5` | 8.5:1 | AA Pass |
| Hotel: `#D4AF37` на `#1A1A2E` | 6.3:1 | AA Pass |
| Tech: `#C9D1D9` на `#0D1117` | 10.1:1 | AA Pass |
| Tech: `#58A6FF` на `#0D1117` | 6.7:1 | AA Pass |
| Kids: `#5D4037` на `#FFF8E1` | 7.1:1 | AA Pass |

### Keyboard Navigation

- Tab order: SSID -> Password -> Toggle visibility -> Security dropdown -> Hidden toggle -> Show password toggle -> Template 1-6 -> Print -> Download -> Share
- Template selector: Arrow Left/Right для переключения между шаблонами
- Enter/Space на шаблоне: активировать
- Visible focus ring: `outline: 2px solid #4F46E5; outline-offset: 2px;`
- Для toggle switches: Space для переключения

### Screen Reader Labels

```html
<label for="ssid">Network name (SSID)</label>
<input id="ssid" type="text" aria-describedby="ssid-hint">
<span id="ssid-hint" class="sr-only">Enter your WiFi network name</span>

<label for="password">Password</label>
<input id="password" type="text" aria-describedby="password-hint">
<button aria-label="Toggle password visibility">...</button>

<label for="security">Security type</label>
<select id="security" aria-describedby="security-hint">...</select>

<input type="checkbox" id="hidden" role="switch" aria-label="Hidden network">
<input type="checkbox" id="showpass" role="switch" aria-label="Show password on card">

<div role="radiogroup" aria-label="Card template">
  <button role="radio" aria-checked="true" aria-label="Minimal template">...</button>
  <button role="radio" aria-checked="false" aria-label="Warm template">...</button>
  <!-- ... -->
</div>

<div class="card-preview" aria-live="polite" aria-label="WiFi card preview">
  <!-- Карточка обновляется, screen reader объявит изменения -->
</div>

<button aria-label="Print WiFi card">Print</button>
<button aria-label="Download WiFi card as PNG">Download PNG</button>
<button aria-label="Share WiFi card">Share</button>
```

### Screen-reader-only class

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}
```

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

Пользователи с настройкой "Reduce motion" не увидят анимации — все переходы мгновенные.

---

## 9. Print CSS

```css
@media print {
  body * { visibility: hidden; }
  .card-preview, .card-preview * { visibility: visible; }
  .card-preview {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
  @page { margin: 1cm; size: auto; }
}
```

При печати — только карточка, по центру страницы, без UI.

---

## 10. Состояния и Edge Cases

### Пустые поля
- При загрузке: placeholder данные (SSID: "MyWiFi", Password: "password123")
- Карточка видна сразу с примером
- Когда пользователь очищает SSID: на карточке появляется `"Network Name"` серым placeholder-текстом

### Длинный SSID / пароль
- На карточке: `text-overflow: ellipsis; overflow: hidden;` после 20 символов
- Или: `font-size` уменьшается через CSS `clamp(12px, 3vw, 16px)` для текста на карточке

### Security "None"
- Поле пароля скрывается
- На карточке: нет строки Password
- QR-код: `WIFI:T:nopass;S:name;;;`

### Show password OFF
- На карточке вместо пароля: `"Scan QR to connect"` italic
- Input формы остаётся видимым

---

## 11. Сводка стилей (CSS Custom Properties)

```css
:root {
  /* UI Colors */
  --color-bg: #F7F8FA;
  --color-surface: #FFFFFF;
  --color-text: #1A1A2E;
  --color-text-secondary: #6B7280;
  --color-accent: #4F46E5;
  --color-accent-hover: #4338CA;
  --color-border: #E5E7EB;
  --color-success: #059669;
  --color-security-bg: #ECFDF5;
  --color-security-border: #A7F3D0;
  --color-security-text: #065F46;

  /* Typography */
  --font-ui: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  --font-mono: ui-monospace, "SF Mono", "Cascadia Code", Consolas, monospace;

  /* Spacing */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;

  /* Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;

  /* Shadows */
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.08);
  --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.08);

  /* Card dimensions */
  --card-width: 378px;
  --card-height: 264px;
  --qr-size: 120px;

  /* Transitions */
  --transition-fast: 0.15s ease;
  --transition-normal: 0.25s ease;
}
```

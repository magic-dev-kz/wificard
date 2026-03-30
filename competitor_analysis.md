# WifiCard -- Competitive Analysis (RECON)
**Agent:** Molot | **Date:** 2026-03-29 | **Format:** RECON + COMPARISON

---

## 1. Прямые конкуренты

### A. Web-инструменты

| # | Продукт | URL | Цена | Шаблоны | Офлайн | Приватность | Ключевые фичи | Слабости |
|---|---------|-----|------|---------|--------|-------------|----------------|----------|
| 1 | **QiFi** | qifi.org | Free, OSS | 0 | Да | Да (localStorage) | Чистый JS, голый QR | Дизайн 2012 года, не responsive, jQuery, нет карточек |
| 2 | **WiQRCode** | wiqrcode.com | Free | 4 пресета (Home/Restaurant/Hotel/Office) | Да | Да | PNG/SVG/PDF, кастом цвета, лейблы, 50K+ юзеров | Только QR + лейбл, не полноценная карточка для печати |
| 3 | **MyToolster** | mytoolster.com/wifi-qrcode | Free | 5 (Office/Cafe/Wellness/Beach/Tech) | Да | Да | Генератор безопасного пароля, hidden network | Нет PNG export, ограниченная кастомизация |
| 4 | **RouterQR** | routerqr.com | Free | Есть | Нет | Неизвестно | Шаблоны Wi-Fi QR, база паролей роутеров | Мало информации, серверный рендеринг |
| 5 | **WiFiQRCode.com** | wifiqrcode.com | Free | 0 | Да | Да | Простой генератор + печать | Минималистичный до убогости, нет шаблонов |
| 6 | **Canva** | canva.com/wifi-qr-code-generator/ | Free/Pro ($13/mo) | 1000+ дизайнов | Нет | Нет (облако) | Огромная библиотека дизайнов, коллаборация | Только static QR, нет нативного WiFi QR (только URL), требует аккаунт |
| 7 | **QR Code Generator** | qr-code-generator.com | $9.99/mo | Кастом | Нет | Нет | Кастомизация QR, аналитика | **Trustpilot 1.5/5!** Коды умирают после trial, скрытая годовая подписка |
| 8 | **QR Tiger** | qrcode-tiger.com | $7/mo | Кастом QR | Нет | Нет | Динамические QR, лого в QR, аналитика | 500 сканов в free, paywall на всё красивое |
| 9 | **QRCode Monkey** | qrcode-monkey.com | Free | Кастом QR | Нет | Неизвестно | Лого в QR, кастом цвета/формы, SVG/PDF | Trustpilot ~4.0, нет WiFi-специфичных шаблонов карточек |
| 10 | **GenQRCode** | genqrcode.com | Free | 0 | Нет | Неизвестно | WiFi QR генерация | Базовый, без карточек |

### B. iOS App Store

| # | Приложение | Разработчик | Цена | Рейтинг | Ключевые фичи | Слабости |
|---|-----------|-------------|------|---------|----------------|----------|
| 1 | **WiFi QR Code Generator** | Grima Hdez | Free (IAP) | ~4.5 | Офлайн, dark mode, все типы шифрования, share | Нет шаблонов карточек, требует iOS 18.6 |
| 2 | **QRFi** | James Tapping | Free (Pro IAP) | 4.0 | 3 стиля QR (square/circle/diamond), кастом цвета, widget | Face ID lock (Pro), нет печатных карточек |
| 3 | **My Wi-Fi with QR Code** | Tevfik Yucek | Free (IAP) | ~4.2 | Мульти-сети, мгновенное подключение | Нет шаблонов, нет offline гарантии |
| 4 | **WiFi QR Code Scan & Generator** | Various | Free (ads) | ~3.8 | Сканер + генератор, все типы QR | **Много рекламы**, базовый дизайн |
| 5 | **WIFI QR Maker & Scanner** | Various | Free (IAP) | ~3.5 | Сканирование + создание | Перегружен функциями, не фокус на WiFi card |

### C. Google Play

| # | Приложение | Цена | Ключевые фичи | Слабости |
|---|-----------|------|----------------|----------|
| 1 | **WIFI QR Generator and Scanner** | Free (ads) | Генерация + сканирование | Реклама, базовый UI |
| 2 | **WiFi QR Code Shower** | Free (ads) | Показ пароля + QR | Нагромождение функций |
| 3 | **QR Code Generator 2025** | Free (IAP) | Универсальный QR генератор | WiFi -- один из 20 типов, не специализирован |
| 4 | **WiFi QR Generator** (Aivoralab) | Free | Генерация и шаринг | Минимальный функционал |

### D. Open Source (GitHub)

| # | Проект | Stars | Стек | Шаблоны | Статус |
|---|--------|-------|------|---------|--------|
| 1 | **bndw/wifi-card** (wificard.io) | **7.1K** | React | Несколько | Заброшен (2021), i18n, Docker |
| 2 | **evgeni/qifi** | ~1K | jQuery | 0 | Поддерживается |
| 3 | **davefowler/wifiqr** | ~200 | Vanilla HTML | 0 | Простой, заброшен |

### E. Etsy шаблоны (непрямые конкуренты)

- Цена: **$2-10** за digital download
- Формат: Canva-шаблоны, PDF, PNG
- Топ-продавцы: Star Sellers с 4.8+ рейтингом
- Ключевая проблема: **требуют отдельный QR-генератор** -- двухшаговый процесс
- Аудитория: Airbnb хосты, отели, кафе
- Объём: сотни продавцов, тысячи продаж

---

## 2. Feature Comparison Table (TOP-7)

| Feature | **WifiCard** | QiFi | WiQRCode | MyToolster | wifi-card (GH) | Canva | QR Tiger |
|---------|:------------:|:----:|:--------:|:----------:|:--------------:|:-----:|:--------:|
| **Price** | Free | Free | Free | Free | Free/OSS | Free/$13mo | $7/mo |
| **WiFi QR generation** | In-browser | In-browser | In-browser | In-browser | In-browser | Cloud (URL only!) | Cloud |
| **Card templates** | **6** | 0 | 4 (preset labels) | **5** | 2-3 | 1000+ (generic) | Custom QR only |
| **Print-ready card** | **Yes** | No | No | Partial | Yes | Yes (manual) | No |
| **Offline** | **Yes** | Yes | Yes | Yes | Yes | No | No |
| **PNG export** | **Yes** | No | Yes | No | Yes | Yes | Yes (paid) |
| **SVG export** | No | No | Yes | No | No | Yes | Yes (paid) |
| **PDF export** | No | No | Yes | No | No | Yes | Yes (paid) |
| **Real-time preview** | **Yes** | No | Yes | Yes | Yes | No | No |
| **Privacy (no server)** | **Yes** | Yes | Yes | Yes | Yes | No | No |
| **Hidden network** | Yes | Yes | Yes | Yes | No | No | Yes |
| **Password visibility toggle** | **Yes** | No | No | No | No | No | No |
| **Custom branding/colors** | No | No | Yes | No | No | Yes | Yes (paid) |
| **Logo in QR** | No | No | No | No | No | No | Yes (paid) |
| **NFC support** | No | No | No | No | No | No | No |
| **Batch/multi-network** | No | No | No | No | No | No | Yes (paid) |
| **Mobile app** | No (web) | No | No | No | No | Yes | Yes |
| **Single HTML file** | **Yes** | Yes | No | No | No (React) | No | No |
| **i18n** | No | No | Yes | No | Yes | Yes | Yes |
| **Analytics** | No | No | No | No | No | No | Yes (paid) |
| **Safe password gen** | No | No | No | **Yes** | No | No | No |
| **Responsive** | Yes | No | Yes | Yes | Yes | Yes | Yes |

---

## 3. Gap Analysis -- что платные конкуренты делают ЛУЧШЕ

### Критичные разрывы (конкуренты лучше):

1. **SVG/PDF экспорт** -- WiQRCode и Canva дают SVG и PDF. Мы даём только PNG. Для печати в типографии SVG критичен.

2. **Кастомизация цветов/брендинга** -- WiQRCode позволяет менять цвета QR и карточки. У нас -- только выбор из 6 шаблонов.

3. **Мультиязычность** -- wificard.io (GitHub) имеет i18n на 10+ языков. WiQRCode тоже. Мы -- только English.

4. **Количество шаблонов** -- MyToolster имеет 5 с чёткой привязкой к venue (Office/Cafe/Wellness/Beach/Tech). Canva -- тысячи. Мы в середине.

5. **Генератор безопасного пароля** -- MyToolster предлагает "Generate Safe Password" (16 символов). Очень полезная штука, у нас нет.

### Некритичные разрывы:

6. **Мобильное приложение** -- нативные iOS/Android приложения дают push-напоминания, widgets. Мы -- PWA максимум.

7. **Логотип внутри QR** -- QR Tiger и QRCode Monkey позволяют вставить лого. Снижает надёжность сканирования, но маркетингово привлекательно.

8. **Аналитика сканов** -- только у платных (QR Tiger, qr-code-generator.com). Требует динамический QR + сервер.

9. **NFC** -- WiFi Porter ($40 устройство) комбинирует NFC + QR. Никто из веб-конкурентов этого не делает, но тренд растёт.

---

## 4. Анализ 1-2 звёздочных отзывов (= наш roadmap)

### Главные боли пользователей платных QR-генераторов:

| # | Боль | Источник | Частота | Наше решение |
|---|------|----------|---------|--------------|
| 1 | **"Free" = ловушка: коды умирают после trial** | qr-code-generator.com (Trustpilot 1.5/5), QR.io | Массовая | Наши QR -- static, работают вечно, бесплатно |
| 2 | **Скрытая годовая подписка (EUR178+ списание)** | qr-code-generator.com | 8+ упоминаний | Нет подписки, нет аккаунта |
| 3 | **Данные WiFi отправляются на чужой сервер** | Все облачные генераторы | Растущая | Всё в браузере, privacy by design |
| 4 | **Надо отдельно генерировать QR и отдельно делать дизайн (2 шага)** | Etsy + QiFi юзеры | Постоянная | Всё в одном: QR + дизайн + печать |
| 5 | **Слишком много рекламы в бесплатных приложениях** | Google Play/iOS apps | Частая | Нет рекламы |
| 6 | **Уродливый голый QR без контекста** | QiFi юзеры | Частая | 6 красивых шаблонов |
| 7 | **Невозможно связаться с поддержкой** | qr-code-generator.com | 5+ упоминаний | Open source, GitHub issues |
| 8 | **QR не работает (битый формат)** | Различные приложения | Редкая | Строгий формат WIFI:T:...;; с валидацией |

### Ключевой инсайт:

> Пользователи НЕНАВИДЯТ bait-and-switch. Главное конкурентное преимущество WifiCard -- это честность: бесплатно значит бесплатно, навсегда, без сервера, без аккаунта. Это надо ставить в центр маркетинга.

---

## 5. Рекомендации для WifiCard v2

### TIER 1: Быстрые wins (1-2 дня каждый, максимальный ROI)

| # | Фича | Зачем | Сложность |
|---|------|-------|-----------|
| 1 | **SVG экспорт** | Типографии хотят вектор. Выделяет нас среди ВСЕХ бесплатных конкурентов | Низкая |
| 2 | **PDF экспорт** | "Download PDF" -- стандартное ожидание для print-ready | Низкая |
| 3 | **Генератор безопасного пароля** | Одна кнопка "Generate secure password" -- и юзер не только делает карточку, но и улучшает безопасность WiFi | Низкая |
| 4 | **i18n (5 языков)** | English, Spanish, French, German, Portuguese покроют 80% аудитории. wificard.io это сделал -- мы можем лучше | Средняя |
| 5 | **PWA + Service Worker** | Иконка на домашнем экране, полный офлайн, "установи как приложение" | Низкая |
| 6 | **Web Share API** | Кнопка "Share" для отправки PNG через мессенджеры | Низкая |

### TIER 2: Серьёзные преимущества (3-5 дней)

| # | Фича | Зачем | Сложность |
|---|------|-------|-----------|
| 7 | **Custom colors** | Позволить выбрать акцентный цвет для любого шаблона. Персонализация без сложности | Средняя |
| 8 | **Ещё 6 шаблонов** (итого 12) | Spa, Restaurant, Coworking, Minimal Dark, Vintage, Tropical | Средняя |
| 9 | **Custom text field** | "Welcome to [название]!" -- персонализация карточки для конкретного места | Низкая |
| 10 | **localStorage** | Сохранять последние настройки. Вернулся -- всё на месте | Низкая |
| 11 | **Multiple card sizes** | Credit card, A6, A5, poster. Для разных use cases | Средняя |

### TIER 3: Killer features (разрушители рынка)

| # | Фича | Зачем | Сложность |
|---|------|-------|-----------|
| 12 | **Batch mode** | Ввести 5 сетей (гостевая, основная, 5GHz, офис) -- скачать все карточки разом | Средняя |
| 13 | **Custom logo upload** | Загрузить лого кафе/отеля на карточку (не в QR!) | Средняя |
| 14 | **NFC instructions** | Инструкция "Как записать WiFi на NFC-тег" + ссылка на покупку тегов | Низкая |
| 15 | **QR style options** | Rounded dots, colored QR (сохраняя читаемость) | Высокая |
| 16 | **Physical card order** | Интеграция с print-on-demand (Printful/Gelato) -- заказать пластиковую карточку | Высокая |

### TIER 4: Монетизация (опционально)

| # | Модель | Описание |
|---|--------|----------|
| 17 | **Premium шаблоны** | 6 бесплатных + 12 premium за one-time $3-5 |
| 18 | **White-label API** | Для Airbnb management companies -- bulk generation |
| 19 | **Physical cards** | Маржа на print-on-demand пластиковых карточек |
| 20 | **Affiliate NFC tags** | Ссылки на Amazon NFC теги с affiliate markup |

---

## 6. Вердикт

### Текущая позиция WifiCard:

**WifiCard уже лучше 90% конкурентов** по соотношению качество/простота/приватность. Ни один бесплатный конкурент не даёт 6 красивых print-ready шаблонов + real-time preview + PNG export + privacy + single HTML file.

### Главный конкурент:

**MyToolster** -- ближайший по концепции (5 шаблонов, офлайн, privacy). Но у них нет PNG export и карточки менее проработаны визуально.

**WiQRCode** -- лидер по функциям (SVG/PDF/PNG, 50K+ юзеров), но даёт QR с лейблом, а не полноценную карточку.

**bndw/wifi-card** (wificard.io) -- 7.1K stars на GitHub, но проект заброшен с 2021 года, React-based (тяжёлый).

### Стратегия:

> Добавить SVG/PDF export + генератор пароля + PWA + 6 новых шаблонов = WifiCard становится ЛУЧШИМ WiFi card generator в мире. Бесплатным, офлайн, приватным, с лучшими шаблонами. Платным конкурентам нечего противопоставить.

### Маркетинговый messaging:

1. **"Your WiFi password never leaves your browser"** -- главный USP против облачных генераторов
2. **"Free forever. No account. No trial."** -- удар по qr-code-generator.com и QR Tiger
3. **"Beautiful cards, not ugly QR codes"** -- удар по QiFi и голым генераторам
4. **"One file. Works offline. Print in 5 seconds."** -- техническая простота как преимущество

---

## Sources

- [QiFi -- pure JS WiFi QR Code Generator](https://qifi.org/)
- [WiQRCode -- Free WiFi QR Code Generator 2026](https://wiqrcode.com/)
- [MyToolster WiFi QR Code Generator](https://mytoolster.com/wifi-qrcode)
- [RouterQR -- WiFi QR Templates](https://routerqr.com/wi-fi-qr-templates/)
- [WiFiQRCode.com](https://wifiqrcode.com/locale/en/)
- [bndw/wifi-card on GitHub (7.1K stars)](https://github.com/bndw/wifi-card)
- [Canva WiFi QR Code Generator](https://www.canva.com/wifi-qr-code-generator/)
- [QR Tiger WiFi QR](https://www.qrcode-tiger.com/qr-code-generator/wifi)
- [QRCode Monkey](https://www.qrcode-monkey.com/)
- [Trustpilot: qr-code-generator.com (1.5/5)](https://www.trustpilot.com/review/www.qr-code-generator.com)
- [WiFi QR Code Generator iOS (Grima Hdez)](https://apps.apple.com/us/app/wifi-qr-code-generator/id1528594929)
- [QRFi iOS App](https://apps.apple.com/us/app/qrfi-wifi-qr-code-generator/id1535761355)
- [My Wi-Fi with QR Code iOS](https://apps.apple.com/us/app/my-wi-fi-with-qr-code/id1439754433)
- [Etsy WiFi QR Code Templates](https://www.etsy.com/market/airbnb_wifi_sign_qr_code)
- [WiFi Porter (NFC + QR)](https://tenonedesign.com/porter.php)
- [NFC WiFi Sharing -- Seritag](https://seritag.com/learn/using-nfc/how-to-share-wifi-with-nfc-tags)
- [Gizmodo: Share WiFi with NFC/QR](https://gizmodo.com/share-your-home-wi-fi-easily-using-an-nfc-tag-or-qr-cod-1624327128)

# QR Generator — Research Report
**Product #10 | OpenClaw Sandbox**
**Date:** 2026-03-29
**Author:** Саня (Product Manager)

---

## 1. Landscape: Top QR Code Generators

| Product | Pricing | Rating (Trustpilot) | Free Tier | WiFi QR |
|---------|---------|---------------------|-----------|---------|
| QR Code Generator (qr-code-generator.com) | $9.99/mo (billed annually) | **1.5/5** | Fake free (codes die) | Yes |
| QR Tiger | $7/mo | ~3.8/5 | 500-scan limit | Yes |
| Flowcode | $5-15/mo | ~3.5/5 | Ads in free tier | Limited |
| Beaconstac (Uniqode) | $5/mo (3 codes only) | ~3.2/5 | Almost useless | Yes |
| QR Code Monkey | Free | ~4.0/5 | Truly free | Yes |
| QiFi (qifi.org) | Free, open-source | N/A | Fully free | **Only WiFi** |

## 2. Top Complaints (1-2 Star Reviews)

### From Trustpilot (qr-code-generator.com — 1.5/5 stars):
1. **"Free" = ловушка** (12+ упоминаний) — QR-коды перестают работать после trial, а ты уже напечатал визитки
2. **Скрытая годовая подписка** (8 упоминаний) — показывают цену в месяц, списывают за год ($133+)
3. **Нет возврата** (6 упоминаний) — даже через 1 день после подписки
4. **Поддержка = чат-боты** (5 упоминаний) — невозможно связаться с человеком
5. **QR-коды блокируются** (4 упоминания) — 21 скан и стоп

### Общие боли:
- Dynamic QR требует подписку ВЕЗДЕ
- Красивый QR = paywall
- Скачать в SVG/PNG = premium feature
- Нет офлайн-работы
- WiFi QR → нужно вводить данные на чужом сервере (безопасность!)

## 3. Конкурентный анализ: WiFi QR ниша

### QiFi (qifi.org) — главный бесплатный конкурент
- **Плюсы:** Open-source, работает локально, данные не отправляются на сервер
- **Минусы:**
  - Дизайн 2012 года (Bootstrap 2)
  - Не адаптирован под мобильные
  - Генерирует ГОЛЫЙ QR-код без контекста
  - Нет красивых шаблонов для печати
  - Нет предпросмотра карточки
  - Использует jQuery (устаревший стек)

### MyToolster WiFi QR
- Предлагает 3 стиля: Minimal, Cafe, Cyber
- Работает в браузере
- Но: ограниченная кастомизация, нет SVG-экспорта

### Etsy шаблоны WiFi QR ($3-10)
- Canva-шаблоны для Airbnb/кафе/отелей
- Люди ПЛАТЯТ за красивую карточку с WiFi QR!
- Но: нужен Canva, нужен отдельный QR-генератор, двухшаговый процесс

## 4. Выбор ниши: WiFi QR Card Generator

### Почему WiFi QR?

**Рынок:**
- 76% потребителей предпочитают бизнесы с бесплатным WiFi
- 62% бизнесов отмечают: гости проводят больше времени с WiFi
- Airbnb — 7.7M объявлений по миру, каждому нужна WiFi-карточка
- Etsy: сотни продавцов продают WiFi QR шаблоны по $3-10

**Боль:**
- Текущий процесс: (1) найти QR-генератор (2) ввести WiFi данные на чужом сервере (3) скачать QR (4) открыть Canva (5) вставить в шаблон (6) распечатать
- Наше решение: ВСЁ в одном шаге, офлайн, данные не покидают браузер

**Конкуренция:**
- QiFi: функциональный, но уродливый, нет шаблонов
- Платные генераторы: paywall за красивые QR
- Etsy: статичные шаблоны, нужна Canva, нет QR-генерации
- **НЕТ** красивого бесплатного генератора WiFi QR-карточек с шаблонами

### Persona
**Марина, 34 года** — хозяйка квартиры на Airbnb в Тбилиси. Сдаёт 2 квартиры. Хочет красивую карточку с WiFi для гостей. Не дизайнер, не программист. Пробовала Canva, но это слишком сложно ради одной карточки. На Etsy нашла шаблон за $5, но не смогла вставить свой QR-код.

### Изюм
Не просто QR-код, а ГОТОВАЯ КАРТОЧКА для печати. 6+ дизайнерских шаблонов (Minimal, Cafe, Hotel, Airbnb, Home, Tech). Ввёл SSID + пароль -> получил красивую карточку -> распечатал.

## 5. Технические находки

### Библиотеки для QR-генерации (vanilla JS, без npm):
1. **QRCode.js** (davidshimjs) — 0 зависимостей, Canvas + Table, ~34KB
2. **qr-creator** (nimiq) — 12.2KB min (4.75KB gzip), SVG + Canvas, градиенты, скругления
3. **EasyQRCodeJS** — Canvas/SVG/Table, кастомизация, но тяжелее

**Рекомендация:** qr-creator от Nimiq — самый лёгкий, поддерживает SVG, стильные QR.

### WiFi QR формат:
```
WIFI:T:WPA;S:NetworkName;P:Password;H:false;;
```
- T = тип шифрования (WPA, WEP, nopass)
- S = SSID
- P = пароль
- H = скрытая сеть (true/false)

---

## Sources
- [Trustpilot: qr-code-generator.com (1.5/5)](https://www.trustpilot.com/review/www.qr-code-generator.com)
- [QiFi — pure JS WiFi QR Code Generator](https://qifi.org/)
- [QR Tiger vs competitors](https://www.qrcode-tiger.com/qrtiger-vs-top-rated-qr-code-generators)
- [WiFi QR for restaurants — QRLynx](https://qrlynx.com/blog/wifi-qr-code-restaurant-cafe)
- [Etsy: WiFi QR templates](https://www.etsy.com/market/airbnb_wifi_sign_qr_code)
- [qr-creator library (Nimiq)](https://github.com/nimiq/qr-creator)
- [QRCode.js](https://github.com/davidshimjs/qrcodejs)
- [EasyQRCodeJS](https://github.com/ushelp/EasyQRCodeJS)

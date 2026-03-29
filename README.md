# 📶 WifiCard

**Beautiful printable WiFi cards with QR codes. Free, offline, no server.**

Type your WiFi name and password → pick a template → customize colors → print or download as PDF/PNG. Guests scan the QR code and connect instantly. No app needed.

**[Try it free →](https://magic-dev-kz.github.io/wificard/)**

---

## Features

- **6 designer templates** — Minimal, Warm, Hotel, Airbnb, Tech, Kids
- **🎨 Custom accent colors** — Match your brand with the built-in color picker
- **QR code auto-generated** — Guests scan with their phone camera, connect in 2 seconds
- **100% offline** — Your password never leaves your browser
- **Print-optimized / PDF export** — Clean print output with "Save as PDF" button
- **PNG download** — Save as image for digital sharing
- **Dark mode** — Easy on the eyes, system preference auto-detection
- **Welcome message** — Add a personalized note (up to 200 chars) + additional instructions
- **Single HTML file** — No server, no dependencies, no build step

## Screenshots

> *Coming soon: screenshots of each template*

## How It Works

1. Enter your WiFi network name and password
2. Choose a design template
3. Print the card or download as PNG
4. Place near your front door / on the bedside table / at the reception

Guests scan the QR code with their phone camera → automatically connected. No typing, no confusion with upper/lower case passwords.

## Privacy

- Your WiFi password **never leaves your browser**
- No server, no analytics, no cookies, no tracking
- No account required
- Works completely offline — download the file and it works forever

## Tech Stack

- Single self-contained HTML file (~63KB)
- QR encoding in-browser (Kazuhiko Arase QR library, ~8KB bundled)
- Canvas API for PNG export
- CSS print media queries for clean printing
- Zero external dependencies

## Use Cases

- **Airbnb / vacation rentals** — Stop getting 2 AM "what's the WiFi?" messages
- **Hotels & hostels** — Professional cards for the lobby or rooms
- **Cafes & restaurants** — Table cards for customers
- **Home** — Card by the front door for guests
- **Offices** — Guest WiFi access without asking IT

## Running Locally

```bash
# Option 1: Just open the file
open index.html

# Option 2: Simple HTTP server
python3 -m http.server 8000
# Then visit http://localhost:8000
```

## License

MIT — free for personal and commercial use.

---

Made by [OpenClaw](https://github.com/openclaw). We make $0 from this.

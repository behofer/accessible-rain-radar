# Accessible Rain Radar

A 100% screen-reader-accessible rain radar (rain nowcast) **with weather forecast** for Germany, built as a **single, dependency-free HTML file**. It turns the DWD radar composite into **plain language**, a **timeline** and a **compass view** — no map image required, fully usable with a screen reader and keyboard — and adds an hourly and 7-day **weather forecast** for the same location.

The interface language is detected automatically from the browser: **English** for `en-*` locales, **German** for everything else.

[Live data source: Bright Sky](https://brightsky.dev) · DWD RV radar composite

---

## Features

- **Plain-text nowcast** — a single spoken-friendly sentence such as
  _"Currently dry. Moderate rain is moving in from the west (15 km to the west) at around 15 km/h – expected here in about 70 minutes."_
- **Timeline table** — precipitation at your location in 10-minute steps, 2 hours ahead.
- **Surroundings table + compass** — radar echoes per compass direction up to 50 km, with an estimated direction of movement.
- **Weather forecast tab** — for the selected location: a "now" summary plus an **hourly (24 h)** and **5-day** view with weather, temperature, rain, wind and cloud cover. Switch between _Rain radar_ and _Weather forecast_ with an accessible tab widget (arrow-key navigable), and toggle **hourly / 5-day**.
- **Table or list view** — the forecast can be shown as a table or as a **plain list** (e.g. _"14:00: partly cloudy, 23 °C, 90 % chance of rain, wind 12 km/h from W, 40 % cloud"_), which many screen-reader users find easier to scan. Both toggles are WAI-ARIA radio groups.
- **Remembers your choices** — the last location and the table/list preference are stored locally in the browser (`localStorage`), so you don't have to re-enter them.
- **Accessibility first** — semantic HTML, ARIA live regions (`role="status"` / `role="alert"`), a skip link, proper table headers, 44px tap targets, visible focus styles, `prefers-color-scheme` (light/dark) and `prefers-reduced-motion` support.
- **Bilingual (de/en)** — automatic browser-language detection; all UI text, plain-text sentences, compass directions and intensity levels are localized.
- **Location input** — search by town, address or postcode (Photon / OpenStreetMap geocoding, with a pick-list when several places match) or use the device geolocation (reverse-geocoded to a readable place name).
- **Discoverable & private** — descriptive `<title>`/meta description, Open Graph and JSON-LD tags for search engines; no build step, no dependencies, no tracking. One static `.html` file.

## Data source

[Bright Sky](https://brightsky.dev) serves the **DWD RV radar composite**: 1 km² grid, 5-minute steps, 2-hour forecast. Coverage is **Germany and bordering regions**. Radar is an estimate of near-surface precipitation, not an exact measurement.

The weather forecast (hourly and daily) uses the [Open-Meteo](https://open-meteo.com) API.

Geocoding (place/address → coordinates, and reverse) uses [Photon](https://photon.komoot.io) (OpenStreetMap data).

## Run locally

It is a single static file. Open `index.html` directly in a browser, or — recommended, because **geolocation requires a secure context (HTTPS or `localhost`)** — serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

> Opening via `file://` works, but the "My location" button is blocked by the browser. "Search location" works either way.

## Deploy with Caddy

Caddy serves static files and provisions HTTPS automatically. Put `index.html` into a web root and point a Caddyfile at it.

```bash
# on the server
sudo mkdir -p /var/www/rain-radar
sudo cp index.html /var/www/rain-radar/index.html
```

**Caddyfile** (`/etc/caddy/Caddyfile`):

```caddy
radar.example.com {
    root * /var/www/rain-radar
    file_server
    encode gzip
}
```

Then:

```bash
sudo systemctl reload caddy
```

Caddy obtains and renews a Let's Encrypt certificate automatically (DNS for `radar.example.com` must point at the server, ports 80 and 443 open). HTTPS also means the geolocation button works in production.

For a quick local preview without DNS:

```bash
caddy file-server --root /var/www/rain-radar --listen :8080
```

## Browser language

Detection order: the first entry in `navigator.languages` that starts with `en` → English, that starts with `de` → German; otherwise German is the default. To force a language for testing, change your browser's preferred language and reload.

## License

**GNU General Public License v3.0** — see [`LICENSE`](LICENSE).

Data attribution: radar © Deutscher Wetterdienst (DWD) via [Bright Sky](https://brightsky.dev); weather forecast via [Open-Meteo](https://open-meteo.com); geocoding via [Photon](https://photon.komoot.io) / OpenStreetMap contributors.

# KC Nduaguba — Digital Business Card

A fast, single-page digital business card for NFC tags and QR codes. One tap saves my contact, books a call, or opens WhatsApp. No build step, no framework, just static files.

**Live:** `https://kc-card.vercel.app/` · **Built by:** [@nduagubakc](https://x.com/nduagubakc) · KCEMMA HUB (RC 9602233)

## Features

- **Save contact:** one-tap vCard download with photo, WhatsApp, booking and social links
- **Book a call:** Cal.com / Calendly button
- **Smart WhatsApp links:** each service opens a chat with a pre-filled message about that topic
- **Three languages:** English, French and Igbo, auto-detected and switchable
- **Dark and light themes:** remembered between visits
- **Installable (PWA):** works offline and can be added to the home screen
- **Share and QR:** native share sheet and an on-page QR code
- **Latest YouTube video tile** and a **Download my CV** button
- **Services, proof tiles and featured work** sections
- **Analytics and stats page:** tracks taps, saves and sources, with a dashboard at `/stats`
- **Link previews:** Open Graph and Twitter tags, plus a generated preview image

## Project structure

```
.
├── index.html              # The card (all config lives at the top of its script)
├── stats.html              # Private analytics dashboard
├── sw.js                   # Service worker (offline + install)
├── manifest.webmanifest    # PWA manifest
├── vercel.json             # Hosting headers and clean URLs
├── og-image.png            # Link-preview image (1200×630)
├── icons/                  # App icons (192, 512, maskable, Apple touch)
├── portrait.jpg            # Your photo (optional, falls back to monogram)
└── cv.pdf                  # Your CV (optional, button hides if cvUrl is blank)
```

## Quick start

No install needed. Serve the folder locally to test the install button and service worker:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open `http://localhost:3000` (or `:8000`).

## Configuration

Everything you'd normally edit is in two blocks inside `index.html`:

| Block | What it controls |
| --- | --- |
| `CONFIG` | Name, phone, WhatsApp, email, website, booking link, CV path, YouTube channel, socials, featured links, languages, tracking |
| `LANG` | All visible text per language (`en`, `fr`, `ig`): role, bio, services, buttons, messages |

Anything left blank (`""` or `[]`) is hidden automatically.

### Placeholders to replace before going live

- [ ] `YOUR-CARD-URL` in the `canonical`, `og:` and `twitter:` tags (6 places) with your live domain
- [ ] `bookingUrl` with your real Cal.com or Calendly link
- [ ] `email` and `website` once you have a custom domain
- [ ] `portrait.jpg` and `cv.pdf` (add the files)
- [ ] `trackUrl` and `plausibleDomain` if you want analytics
- [ ] Igbo text in `LANG.ig`, ideally reviewed by a native speaker

## Deploy (GitHub + Vercel)

1. Push this folder to a GitHub repo:
   ```bash
   git init && git add . && git commit -m "KC digital card"
   git branch -M main
   git remote add origin https://github.com/nduprincekc/kc-card.git
   git push -u origin main
   ```
2. On [vercel.com/new](https://vercel.com/new), import the repo, set **Framework Preset** to **Other**, leave build settings empty, and deploy.
3. Update the `YOUR-CARD-URL` placeholders with your Vercel (or custom) domain and push again.

Every `git push` redeploys automatically. To roll back, open **Deployments** in Vercel and promote an older one.

## Analytics and stats

The card can send an event on every tap to two places, both optional:

- **Plausible:** set `plausibleDomain` for privacy-friendly traffic stats.
- **Your own n8n webhook:** set `trackUrl`. Each event is a POST with:
  ```json
  { "event": "save", "src": "nfc-1", "lang": "en", "ts": "2026-10-09T10:15:00.000Z", "detail": "" }
  ```

To power `stats.html`:

1. **Logging workflow:** an n8n POST webhook that appends one row per event to Google Sheets (columns: `ts`, `event`, `src`, `lang`, `detail`).
2. **Reading workflow:** an n8n GET webhook that returns those rows as a JSON array. Add a secret key check.
3. Set `STATS.url` at the top of `stats.html` and open `/stats?key=YOUR_SECRET`.
4. Preview with sample data at `/stats?demo=1`.

Make sure the webhooks allow requests from your card's domain (CORS).

### Tracked events

`view`, `save`, `book`, `whatsapp`, `call`, `email`, `cv`, `video`, `share`, `qr`, `service`, `work`, `social`, `install_tap`, `installed`, `lang`, `theme`

### Telling your cards apart

Write each tag or print each QR with its own source so the stats show where visitors came from:

```
https://YOUR-CARD-URL/?src=nfc-1
https://YOUR-CARD-URL/?src=flyer
https://YOUR-CARD-URL/?src=event-lagos
```

Other URL options: `?lang=fr` forces a language.

## Browser notes

- The install button appears on Android and desktop Chrome/Edge. On iPhone it shows an "Add to Home Screen" hint.
- The service worker only runs over HTTPS (or `localhost`).
- The QR button needs an internet connection, because the QR library loads from cdnjs.
- The latest-video tile uses the free [rss2json](https://rss2json.com) service to read the channel feed and falls back to a plain channel link if it's unavailable.

## Credits

- QR code generator: [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) by Kazuhiko Arase (MIT)
- Typefaces: system fonts only (no external font requests)

## License

© KC Nduaguba. All rights reserved. Replace this line with a license (for example MIT) if you want others to reuse the template.

# GestureX — AI Image Dissector

A single-file web app that uses **Anthropic Claude (vision)** to identify every component of an uploaded image, then lets you **dissect and reassemble it with hand gestures** captured by your webcam (via MediaPipe Hands).

Drop an image → Claude analyzes it → pinch your hands apart → the image explodes into labeled, color-coded component cards in 3D space. Pinch in to reassemble. Open palm to pause. Point to inspect. Swipe to cycle.

It also has full keyboard / mouse / touch fallbacks so it works without a camera.

---

## Quick start

You only need `index.html`. Everything is bundled (CSS, JS, MediaPipe via CDN).

```bash
# Local
python3 -m http.server 8000
# then open http://localhost:8000
```

> A static server is recommended over `file://` so the camera and clipboard APIs work reliably.

On first load, click the gear icon (top right) and paste an Anthropic API key — get one at <https://console.anthropic.com/settings/keys>. The key is stored in your browser's `localStorage` only.

---

## Deploy

### Vercel
```bash
npx vercel deploy --prod
```
No build step. Vercel will serve `index.html` at the root.

### Netlify
```bash
npx netlify deploy --dir=. --prod
```

### GitHub Pages
1. Push to a repo.
2. Settings → Pages → Source: `main` branch, root.
3. Visit `https://<user>.github.io/<repo>/`.

### Cloudflare Pages
Drop the folder in the dashboard, or `wrangler pages deploy .`.

Anywhere that serves a static file works.

---

## Features

- **Image upload** — drag & drop, click, or paste from clipboard.
- **AI analysis** — Claude vision (default `claude-sonnet-4-5`, configurable in Settings) identifies every distinct part, returning structured JSON (id, name, description, category, position hint, color hint).
- **Hand-gesture controls** (MediaPipe Hands, runs locally in the browser):
  - Two-hand **pinch out** → dissect / explode
  - Two-hand **pinch in** → reassemble
  - **Open palm** → pause/freeze
  - **Index point** → highlight a part
  - **Swipe L/R** → cycle through parts
- **Fallback controls**: Space = explode/assemble, Arrow keys = cycle parts, on-screen buttons, two-finger pinch on touch.
- **Sci-fi UI**: dark glassmorphism, neon cyan/magenta accents, scanline overlay, particle bursts.
- **History** — last 5 dissections cached in `localStorage`.
- **Share** — exports a composed PNG of the current dissection state.
- **Sound effects** — optional, generated via Web Audio (no asset downloads).
- **Mobile responsive** — works on phones, tablets, and desktops.

---

## How API access works

Claude calls go directly from the browser to `api.anthropic.com` using the
`anthropic-dangerous-direct-browser-access: true` header. This means **the user's key
is sent from their device only** — there is no server in the middle.

If you want to host this for other people without exposing them to the API-key flow,
add a small proxy that injects the key server-side and adjust `Claude.endpoint` in
`index.html` to point at it.

---

## File structure

```
index.html   # the entire app
README.md    # this file
```

That's it. No build, no deps.

---

## Browser support

- Chrome / Edge / Brave / Arc — full support
- Firefox — full support
- Safari (iOS 16+, macOS 13+) — full support; tap "Camera" to grant access
- Older browsers without `getUserMedia` will still work via keyboard / button / touch fallbacks

---

## Troubleshooting

- **"Claude API 401 / invalid x-api-key"** — your Anthropic key is wrong; re-enter in Settings.
- **"Model … not found" / 404** — the model snapshot has been retired. Open Settings → Model and enter a current one (e.g. `claude-sonnet-4-5`, `claude-sonnet-4-6`, or `claude-opus-4-7`).
- **"MediaPipe Hands failed to load"** — your network blocked `cdn.jsdelivr.net`. Allow it or self-host the `@mediapipe/hands` package.
- **Camera not appearing** — browsers require HTTPS (or localhost) for `getUserMedia`. Deploy to any HTTPS host above.
- **Gestures feel jumpy** — improve lighting, keep both hands fully in frame, and avoid backlight.

---

## License

MIT.

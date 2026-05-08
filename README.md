# GestureX — Holographic Dissector

A WebGL holographic interface in the browser. Your live webcam is the backdrop, the uploaded image becomes a real 3D object floating in front of you, and your hands drive everything via MediaPipe.

```
FLAT  ──spread──▶  HOLOGRAM  ──spread──▶  EXPLODED
       ◀──close──             ◀──close──
```

- **FLAT** — your image lying as a flat plane, lit, with a holographic ring beneath it.
- **HOLOGRAM** — every component Claude identified lifts off the plane and floats at its own depth. Two-hand motion rotates the model in 3D.
- **EXPLODED** — components fly outward radially in 3D space. Pinch to grab one and drag it through space. Pinch-tap → details.

## Stack

- **Three.js** — WebGL scene, mesh per component, depth, parallax, particle field.
- **Live `<video>` background** — the same MediaStream is used by both MediaPipe and the AR backdrop, so you appear behind the hologram.
- **MediaPipe Hands** — two-hand tracking, runs locally in the browser.
- **Claude vision** — `claude-sonnet-4-5` by default, configurable. Identifies every distinct part with position, size, color, and category metadata. Each part is then cropped from the original image and used as the texture for its own 3D plane.

## Quick start

You only need `index.html`.

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

Open the gear icon (top-right), paste an Anthropic API key from <https://console.anthropic.com/settings/keys>. Stored in `localStorage` only.

## How it works

1. Drop an image, paste, or click **Upload**. (Or click **Scan Live**, hold an object up to your camera, count down 3-2-1 — Claude analyses what you held up.)
2. Claude returns the part list with positions and colors.
3. The full image becomes a textured plane in WebGL. Each part becomes its own cropped textured plane, with three positions: flat (lying on top of the base), holo (lifted to its own depth), and exploded (radial in 3D).
4. The state machine smoothly tweens between these positions as you spread your hands.
5. Two-hand horizontal/vertical motion drives `root.rotation`. Single-hand pinch raycasts to the nearest mesh and attaches it to your fingers.

## Gestures

| Gesture | Action |
|---|---|
| Two-hand **spread** | Advance state (Flat → Holo → Exploded) |
| Two-hand **close** | Retreat state |
| Two-hand **move** (sideways/vertical) | Rotate the whole model |
| One hand **pinch** over a part | Grab and drag in 3D |
| One hand **pinch + release** (no drag) | Open detail card |
| Open palm + slow drift | Slow Y rotation |

Keyboard fallbacks: **Space** advance · **Esc** retreat · **U** upload · **L** scan live · **C** camera · **S** settings · **←/→** cycle parts.

## Deploy

```bash
npx vercel deploy --prod        # Vercel
npx netlify deploy --dir=. --prod  # Netlify
# or push to a GitHub repo and enable Pages
```

`index.html` + `vercel.json` + `README.md`. No build, no deps.

## Browser support

Modern Chrome / Edge / Firefox / Safari (with WebGL 2 + `getUserMedia`). The site needs HTTPS or `localhost` for camera access.

## Troubleshooting

- **Three.js or MediaPipe failed to load** — check that `cdn.jsdelivr.net` isn't blocked.
- **404 on the model** — Anthropic retires older snapshots. Open Settings and try `claude-sonnet-4-5`, `claude-sonnet-4-6`, or `claude-opus-4-7`.
- **Gestures feel jumpy** — improve lighting, keep both hands fully in frame, avoid backlight.
- **Camera blocked** — grant the site camera permission in your browser site settings, then refresh.

## License

MIT.

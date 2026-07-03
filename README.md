# Cinematic Web Lab

Two single-file WebGL scroll experiences exploring **scroll cinematography** — treating a webpage as a film that the visitor plays by scrolling.

**Live site:** https://ab30-bot.github.io/cinematic-web-lab/

| Experiment | File | Live | Concept |
|---|---|---|---|
| 01 — AETHER | [`aether.html`](./aether.html) | [Open ↗](https://ab30-bot.github.io/cinematic-web-lab/aether.html) | Marketing site for a fictional AI-agent platform |
| 02 — MIND 001 | [`mind001.html`](./mind001.html) | [Open ↗](https://ab30-bot.github.io/cinematic-web-lab/mind001.html) | Unofficial concept film for the real **Nike Mind 001** neuroscience mule |
| Bonus | [`aether-nextjs.zip`](./aether-nextjs.zip) | — | AETHER rebuilt as a production Next.js 14 + TypeScript codebase |

> Academic context: built as a university portfolio project on creative front-end engineering.
> Every experience is **one HTML file** — open it, view source, and the entire system is readable.

---

## 1. Design goals

1. **Cinematic, not scrollable** — each page is structured as chapters with a persistent 3D protagonist, letterboxing, HUD chrome, and a preloader that behaves like a title sequence.
2. **Zero build step** — no bundler, no framework at runtime. Three.js, GSAP + ScrollTrigger, and Lenis are loaded from CDNs; everything else is hand-written.
3. **Procedural 3D** — no `.glb` downloads that can 404. The MIND 001 shoe and its 22 sensory nodes are generated from math at load time, so the model always exists.
4. **Graceful degradation** — `prefers-reduced-motion` support, no-JS `<noscript>` fallbacks, CDN-failure guards that reveal all content if GSAP never arrives.

## 2. Techniques worth studying

### 2.1 The scroll-film architecture (`mind001.html`)

A single proxy object drives the whole movie. ScrollTrigger scrubs tweens on it per chapter; the render loop just reads it:

```js
const FILM = { camX:0, camY:.25, camZ:6.6, rotY:.55, scatter:0, fire:.7, orb:.55 };
// chapter tweens, e.g. dive under the sole during CH.001:
seg('#sensation', { camZ:4.4, camY:-.55, rotY:1.15, fire:1.5 });
// render loop consumes FILM every frame → camera + shader uniforms
```

One source of truth means chapters can never fight each other over the camera.

### 2.2 Procedural point-cloud modelling

The mule is defined by three 2-D profiles (top line, sole line, width-along-length), smoothstep-interpolated, then filled with 8,200 rejection-sampled particles — biased to the shell so the upper reads as a surface, with the foot-opening carved out. The **22 nodes** (real product spec) are meshes placed on a plantar-pressure map: 5 heel, 4 midfoot, 8 forefoot, 5 toe.

### 2.3 GLSL scatter-morph

Every particle carries two positions — its place in the shoe and a random point on a sphere. One uniform (`uScatter`) mixes between them, so the shoe can dissolve into a starfield for the CALM chapter and reassemble for colorways:

```glsl
vec3 p = mix(position, aScatter, uScatter);
```

### 2.4 Colorways as live 3-D state

The colorway cards don't swap images — they tween the shader's `uColorA/uColorB` uniforms and the node materials, re-skinning the particle shoe in real time (Light Smoke Grey `HQ4307-003`, Triple Black `HQ4307-005`, Flyknit Blue `IR2175-400`).

### 2.5 Systems shared by both experiments

- Lenis smooth scroll synced to ScrollTrigger via `gsap.ticker`
- Custom cursors (magnetic ring / crosshair with live coordinates)
- Magnetic buttons using `gsap.quickTo` spring physics
- Word-by-word manifesto illumination (scrubbed stagger)
- Pinned horizontal sections with `invalidateOnRefresh` for resize safety
- Preloader → intro timeline handoff with scroll-locking

## 3. Running locally

No install. Clone and open any `.html` file in a browser (internet needed for the CDN libraries + fonts):

```bash
git clone https://github.com/AB30-bot/cinematic-web-lab.git
cd cinematic-web-lab && python -m http.server 8000   # or just double-click a file
```

The Next.js version: unzip `aether-nextjs.zip`, then `npm install && npm run dev`.

## 4. Repository map

```
index.html          landing hub linking both experiences
aether.html         experiment 01 — AI SaaS concept          (~1.6k lines)
mind001.html        experiment 02 — Nike Mind 001 film       (~1.2k lines)
aether-nextjs.zip   AETHER as Next.js 14 + TS + R3F codebase
LICENSE             MIT
```

## 5. Facts & sources (MIND 001)

Product facts used in experiment 02 — 22 foam nodes acting as pistons/gimbals, 10+ years of development, $95 retail, January 2026 release, pre/post-competition use — come from:

- [Nike Newsroom — Mind 001 & 002 debut](https://about.nike.com/en/newsroom/releases/nike-mind-001-mind-002-official-images)
- [Nike.com/Mind](https://www.nike.com/mind) · [Complex on-foot review](https://www.complex.com/sneakers/a/ben-felderstein/nike-mind-001-002-review)
- [WWD — Triple Black](https://wwd.com/footwear-news/sneaker-news/nike-mind-001-triple-black-release-date-hq4307-005-1238998552/) · [WWD — Flyknit](https://wwd.com/footwear-news/sneaker-news/nike-mind-001-flyknit-release-date-ir2175-400-1238977351/)
- [Sole Retriever release calendar](https://www.soleretriever.com/sneaker-release-dates/nike/mind) · [Sneaker Freaker drop history](https://www.sneakerfreaker.com/features/every-nike-mind-001-releasing-2026-price-buy)

**Disclaimer:** experiment 02 is an unofficial fan concept. Nike, Mind 001 and related marks are trademarks of Nike, Inc. No affiliation or endorsement implied. AETHER, its logos, testimonials and metrics are entirely fictional.

## 6. License

Code is [MIT](./LICENSE) © 2026 Adam B.

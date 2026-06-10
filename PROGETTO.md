# Customs24 — Landing Page · Documentazione Completa

> **Repository:** https://github.com/Redogano/customs24-landing  
> **File principale:** `index.html` (single-file app, tutto contenuto in un unico HTML)  
> **Sviluppato con:** Claude Sonnet 4.6 via Claude Code

---

## 1. Concetto & Design

Una landing page stile **Linktree premium** per **Customs24 — G. Schreur International GmbH**, con tagline *"Zoll.Einfach.Machen"*.

Principi di design:
- Tutto si vede **senza scroll esterno** — `height: 100dvh; overflow: hidden`
- Area servizi con **scroll interno silenzioso** (nessuna scrollbar visibile)
- Estetica **glassmorphism** dark — vetro traslucido, blur, glow teal
- **Ultra high-definition rendering** — DPR-aware canvas, font-smoothing, GPU compositing
- Palette: `--teal: #00BCD4`, `--blue`, `--green`, `--amber`, `--red`, `--purple`

---

## 2. Struttura Layout

```
.page  (100dvh, overflow hidden)
└── .card  (max-width 400px, flex column, will-change: transform)
    ├── .logo-wrap          ← logo animato + tagline
    ├── .services           ← bottoni (flex:1, overflow-y auto, scroll interno)
    │   ├── link-btn × 8
    │   ├── .locs-wrap      ← griglia sedi 2×2
    │   └── .marquee-wrap   ← ticker scorrevole
    ├── .divider
    ├── .contact-row        ← flex row: [lang-toggle] + [contact-center]
    │   ├── .lang-toggle    ← toggle DE|EN (inline, non più fixed)
    │   └── .contact-center ← flex:1, centrato
    │       ├── .sec-label  ← "Kontakt & Social"
    │       └── .socials    ← WhatsApp · LinkedIn · Instagram
    └── .footer
```

---

## 3. Sfondo Video Doppio (Seamless Crossfade)

Due video in loop con crossfade automatico — **nessuno stacco visibile**, pensato per sessioni di diversi minuti.

**File:** `203404-921381913_medium.mp4` (video A) + `23008-332257750_medium.mp4` (video B)

**Parametri engine:**
```js
const RATE = 0.52;          // rallentati al 52% per atmosfera
const SWITCH_BEFORE = 5.0;  // inizia crossfade 5s reali prima della fine
const XFADE_SECS = 3.2;     // durata dissolvenza
```

**Mobile:** `#vidA` e `#vidB` nascosti via CSS su dispositivi touch (`display: none`). Al loro posto un gradiente dark navy statico sul `body`. Risparmio ~50MB RAM.

---

## 4. Video Hover per Paese/Servizio

Al passaggio del mouse su un bottone specifico, il video relativo appare come sfondo con `transition: opacity 0.55s ease`. Al mouseout svanisce e si mette in pausa.

| Bottone | Video | Classe CSS |
|---|---|---|
| Türkei | `TR.mp4` | `.tr-on` |
| Italien | `IT.mp4` | `.it-on` |
| Fiskalverzollung + Import | `DE.mp4` | `.de-on` |
| CBAM | `EU.mp4` | `.eu-on` |
| Import UK | `GREAT BRITAIN.mp4` | `.gb-on` |
| Export | `Export.mp4` | `.export-on` |

**Nota Türkei:** `object-position: calc(50% - 130px) center` sposta il video 130px a sinistra così l'emblema turco non viene coperto dalla card.

**DE condiviso** tra due bottoni: contatore `deCount` impedisce che il video si spenga quando si passa da Fiskalverzollung a Import.

**Guard mobile:** tutti i video hover sono wrappati in `if (hasHover)` — su touch device non vengono mai avviati. `preload="none"` su tutti i video hover.

---

## 5. Logo CUSTOMS24 — Animazione Disney

### Layer struttura
```html
<div class="logo-scan-wrap">
  <div class="logo-mark logo-gray">   ← base trasparente (solo per layout height)
  <div class="logo-mark logo-color">  ← colori animati con mask
    <span class="mark-customs">CUSTOMS</span>
    <span class="mark-2">2</span>     ← gradiente teal
    <span class="mark-4">4</span>     ← gradiente gold→fire
```

### Colori
- **CUSTOMS:** `color: white`
- **2:** `linear-gradient(160deg, #4DD0E1 → #00ACC1 → #0097A7)` (teal)
- **4:** `linear-gradient(135deg, #AFC900 → #FFD200 → #FF9100 → #EF3B2C)` (gold/fire)

### Sequenza animazione (5s, one-shot, `forwards`)

| Tempo | Evento |
|---|---|
| 0s → 1.9s | CUSTOMS sweep in dal nulla (mask da -14% → 73%) |
| 1.9s → 2.75s | Pausa — CUSTOMS acceso, "24" buio |
| 2.75s | "24" appare come **unità** (`step-start`, salto istantaneo a 114%) |
| 5s+ | Tutto rimane fisso per sempre |

### Effetto Disney — 4 layer simultanei

1. **Wand corona** — gradiente mask con aureola luminosa 22% davanti al sweep
2. **`logoBloom`** — brightness 4× al termine di CUSTOMS, 2.8× per "24" + drop-shadow multipli
3. **`auraLifetime`** — alone teal esplode in sincrono con i due flash, poi si sedimenta
4. **`wordmarkBounce`** — micro-bounce 4.5% quando "24" scatta

**Nota tecnica:** `filter` rimosso da `.mark-2`/`.mark-4` perché `brightness()` alto sul parent + `background-clip: text` sui figli crea artifact rettangolare nel compositing layer del browser.

---

## 6. Tagline — Zoll · Einfach · Machen

Le tre parole appaiono in sequenza **dopo** che CUSTOMS24 è completamente rivelato:

```css
.tw-1 { animation: wordAppear 0.9s ease-out 5.2s both; }  /* Zoll */
.tw-2 { animation: wordAppear 0.9s ease-out 5.9s both; }  /* Einfach */
.tw-3 { animation: wordAppear 0.9s ease-out 6.6s both; }  /* Machen */
```

`@keyframes wordAppear`: da dim → bloom bianco/teal → si stabilizza in glow permanente. Usa `animation-fill-mode: both` — start dim, end glow per sempre.

---

## 7. Bottoni Servizio

**8 bottoni** con glassmorphism, shimmer sweep, ripple al click, 3D tilt, staggered entry.

| Classe | Servizio | Accento | Email |
|---|---|---|---|
| `.btn-verzollung` | Fiskalverzollung | amber | neuhaus@customs24.eu, gumruk@customs24.eu |
| `.btn-import` | Import | green | jt@customs24.eu, mg@customs24.eu |
| `.btn-export` | Export | blue | export@customs24.eu |
| `.btn-importuk` | Import UK | purple | uk@customs24.eu, jt@customs24.eu |
| `.btn-cbam` | CBAM | #84CC16 | mg@customs24.eu, hvdg@customs24.eu |
| `.btn-turkey` | Türkei | red | fatma, neuhaus, gumruk, nazar, as01 @customs24.eu |
| `.btn-italy` | Italien | #009246 | glf@customs24.eu |
| `.btn-ceo` | CEO kontaktieren | teal | yf@customs24.eu |

Tutti i `href` usano `mailto:` con subject pre-compilato in tedesco (es. `?subject=Anfrage%20Fiskalverzollung`).

### CEO Button
Foto circolare di **Fritzsche** (`grafik(1).png`) con ring teal, glow, scale hover:
```css
.btn-icon--ceo { border-radius: 50%; box-shadow: teal ring + glow; }
.ceo-photo { object-fit: cover; object-position: center 15%; }
```

### Icone paese (inline SVG)
- **Türkei:** mezzaluna + stella (posizione originale)
- **Italien:** tricolore verde/bianco/rosso
- **Grecia/altri:** FontAwesome

---

## 8. Sedi (Unsere Standorte)

Griglia 2×2 dentro `.services`, glassmorphism chip con flag SVG inline. Al click si apre Google Maps.

| Flag | Azienda | Città | Google Maps |
|---|---|---|---|
| 🇩🇪 | G. Schreur Int'l | Emmerich am Rhein | Groendahlscher Weg 87, 46446 |
| 🇩🇪 | AS-Sped | Neuhaus am Inn | Hartham 13, 94152 |
| 🇦🇹 | NAZAR-Sped | Suben · AT | Autobahngrenze 14, 4975 |
| 🇵🇱 | Customs office Frankfurt TTOC | Świecko · PL | TTOC Swiecko 37, 69-106 |

Hover: bordo teal, `translateY(-1px)`, icona pin `fa-location-dot` appare.

---

## 9. Social & Contatti

| Piattaforma | Link/Numero |
|---|---|
| **WhatsApp** | +49 162 3014407 → `https://wa.me/491623014407` · logo custom `whatsapp.jpeg` |
| **LinkedIn** | https://www.linkedin.com/company/customs24-g-schreur-international-gmbh/posts/ |
| **Instagram** | https://instagram.com/customs24 |

---

## 10. Toggle Lingua DE/EN

Il toggle è inline nella riga `.contact-row`, a sinistra dei social (non più `position: fixed`).  
Ogni elemento con testo ha `data-de` e `data-en`:
```html
<span data-de="Fiskalverzollung" data-en="Customs Clearance">Fiskalverzollung</span>
```
JS aggiorna tutti gli elementi al click.

**Struttura contact-row:**
```css
.contact-row   { display: flex; align-items: center; gap: 12px; }
.contact-center { flex: 1; display: flex; flex-direction: column; align-items: center; }
```

---

## 11. Effetti Interattivi

### Parallax Mouse/Gyro
```js
// Card si sposta 0.30x nella direzione del mouse
// Logo si sposta 1.05x in direzione opposta (effetto profondità)
plx += (ptx - plx) * 0.055;  // lerp smoothing
cardEl.style.transform = `translate(${plx*0.30}px, ${ply*0.20}px)`;
logoWrapEl.style.transform = `translate(${-plx*1.05}px, ${-ply*0.68}px)`;
```
Supporta `DeviceOrientationEvent` per mobile (giroscopio).

### Shimmer Sweep
`@keyframes btnShimmer` — stripe luminosa scorre su ogni bottone ogni 8s, con delay scalato da 0s a 7s (cascata).

### Click Ripple
`pointerdown` crea un `<span class="btn-ripple">` con `radial-gradient(var(--accent))`, scala da 0 → 1 in 0.72s poi si auto-rimuove.

### Custom Cursor
Cursore personalizzato con dot + ring, si ingrandisce su hover di bottoni. Nascosto su touch device.

---

## 12. Marquee Ticker

Scorre orizzontalmente in loop continuo: Fiskalverzollung · Import · Export · Import UK · CBAM · Türkei · Italien · Customs24 · Zoll

**Fix clipping:** `padding: 4px 0` su `.marquee-wrap` + `padding-bottom: 20px` su `.services` + fade mask a `calc(100% - 24px)` per evitare che il ticker finisca nella zona trasparente del gradiente.

---

## 13. Canvas Particelle

Particelle fluttuanti in background:
- DPR-aware: `ctx.setTransform(dpr, 0, 0, dpr, 0, 0)` per retina
- **Mobile:** intero sistema (classe `P` + loop rAF) wrappato in `if (hasHover)` — zero CPU/GPU su touch device
- `#particles` nascosto via CSS su mobile

---

## 14. Ottimizzazioni Performance Mobile

| Ottimizzazione | Dettaglio |
|---|---|
| Video sfondo disabilitati | `#vidA, #vidB { display: none }` su touch device |
| Sfondo statico | Gradiente dark navy `#010c1e → #031630` sul `body` |
| Canvas particelle off | Loop rAF dentro `if (hasHover)` |
| Video hover skip | JS guard `if (hasHover)` — nessun `play()` su mobile |
| `preload="none"` | Tutti i video hover — zero download anticipato |
| Backdrop-filter ridotto | Da 22px a 6px su `.link-btn`, `.loc-chip`, `.lang-toggle` |
| Animazioni pesanti off | `.logo-dots { display: none }`, `.logo-aura { animation: none }` |

---

## 15. Responsive Breakpoints

| Breakpoint | Larghezza | Note |
|---|---|---|
| XS | ≤ 374px | iPhone SE 1st gen — logo 38-50px |
| SM | 375–479px | Telefoni standard — logo 38-48px, padding compatto |
| SM height | max-height 750px + max-width 479px | Nasconde `.btn-sub` per recuperare spazio |
| MD | 480–767px | Phablet |
| LG | 768px+ | Tablet |
| XL | 1024px+ | Desktop |
| 2XL | 1440px+ | Large desktop / 4K |
| Landscape | max-height 500px, landscape | Layout compatto orizzontale |

---

## 16. File nel Progetto

| File | Descrizione |
|---|---|
| `index.html` | Intera app (HTML + CSS + JS) |
| `203404-921381913_medium.mp4` | Video sfondo principale A |
| `23008-332257750_medium.mp4` | Video sfondo principale B |
| `TR.mp4` | Hover Türkei |
| `IT.mp4` | Hover Italien |
| `DE.mp4` | Hover Fiskalverzollung + Import |
| `EU.mp4` | Hover CBAM |
| `GREAT BRITAIN.mp4` | Hover Import UK |
| `Export.mp4` | Hover Export |
| `grafik(1).png` | Foto CEO Fritzsche |
| `whatsapp.jpeg` | Logo WhatsApp custom |
| `LOGO CUSTOMS 24 .png` | Logo aziendale |
| `24.png` | Asset logo |
| `Template 1-4.png` | Template di riferimento |

---

## 17. GitHub

```bash
# Repository
https://github.com/Redogano/customs24-landing

# Aggiornare dopo modifiche
git add index.html
git commit -m "descrizione modifica"
git push
```

---

*Ultimo aggiornamento: 2026-06-11 — Claude Sonnet 4.6*

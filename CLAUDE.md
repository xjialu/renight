# RENight 2026: show backdrop

Fullscreen, animated programme backdrop for **RENight 2026**, the graduation dinner of NTU's Renaissance Engineering Programme (REP), **Batch 12** (R12). It runs on **13 Nov 2026** at Amara Hotel, Singapore, on a laptop driving a projector or screen. An operator moves through it with arrow keys or a presentation clicker.

The owner is **Jialu Xu**, the batch representative, who also gives the Graduation Speech. The show was built in a Claude chat session and handed over here.

## Files

| File | What it is |
|---|---|
| `programme.html` | **Source of truth.** All the code (CSS, HTML and JS) in one file, with no `<!doctype>`, `<head>` or `<body>` wrapper. Edit this file. |
| `RENight-2026.html` | The show file to run on the night. It is `programme.html` wrapped in a page skeleton. Rebuild it after every edit (see below). |
| `rsigma.svg` | The REP "R–Σ" logo. The same paths are inlined in the code as `#mark`. |
| `README.md` | Operator notes and keys. |

To rebuild the show file from the source:

```bash
python3 -c "b=open('programme.html').read();open('RENight-2026.html','w').write('<!doctype html><html lang=\"en\"><head><meta charset=\"utf-8\"><meta name=\"viewport\" content=\"width=device-width,initial-scale=1\"><title>RENight 2026</title></head><body>'+b+'</body></html>')"
```

The show has no build step, no dependencies and no framework: plain ES5-style JavaScript in a single `<script>`. Keep it that way, because the show must open with a double-click and work offline.

## Design rules (from the owner; keep to them)

- **Classy, modern, techy, elite.** Black, ivory and gold only. Tokens are CSS vars such as `--ivory`, `--ivory-muted`, `--gold`, `--ash` and `--rule`.
- **Flat.** No glows, shadows or gradients-as-decoration. Motion comes from **dither** (Bayer 8×8 on a cell grid) and **split-flap** tiles.
- **Must not look AI-generated or "vibecoded".** The owner has rejected all of these:
  - Small-caps "eyebrow" labels above headings.
  - Middle-dot (·) separators as decoration.
  - Staggered fade-up entrances.
  - Filler lines like "One name, drawn from 39".
  - Operator or instruction text visible on stage.
  - Cheesy copy.
- Text on the stage must be readable from the back of a ballroom: labels at least 16–18px on the 1920×1080 stage.
- The logo stays top right at all times, with a subtle, small dither shine (an SMIL mask, 11s cycle).
- Default dither cell size is **2px**.

## Architecture (all in `programme.html`)

- **Stage:** a fixed 1920×1080 `#stage` scaled to the viewport (`fit()`). The left/right margin is `M=120`.
- **Mono engine** (`Mono`): canvas dithered type and images on a cell grid.
  - `Mono.add(W(text,{fit,x,y}),{enter,dur,tex})` adds a layer.
  - Enter modes: `resolve`, `plot`, `rain`, `rise`, `burst`.
  - Exit modes: `Mono.clear(mode,ms)`, where mode is one of `drain`, `dissolve`, `lift`, `cut`.
  - Textures: `flow`, `eq` (audio-reactive), `sweep`, `still`, `image`.
  - Other calls: `Mono.painter(fn)` (full-screen painter), `Mono.ambient(on)`, `Mono.addImage`, `Mono.iris`.
  - Colours are in `Mono.COL`.
- **Flaps:** split-flap tiles.
  - `new Flaps(el,n)`, then `.set(text,{drum,stagger,step,delay,align})` and `.lit(on)`.
  - The character set is `DRUM`. A character not in `DRUM` won't flip.
- **Board strip** (`#strip`, `setStrip(show,sc,over)`): a departure-board bar along the bottom showing NOW, status, NEXT and LOCAL time. `lastStrip` caches the last call.
- **Data:**
  - `PROG`: the run-of-show times and names.
  - `SCENES`: the ordered scene list, each with a `kind` and a `rail` index into `PROG`.
  - `steps(sc)`: the number of sub-steps per scene.
  - `STATUS`: the strip status text per kind.
  - `SPEAK`: the speakers.
  - `DEF`: the defaults, which are:
    - `start`: `19:05`
    - `grads` / `names`: the 39-name `CLASS` list
    - `prizes`: `"Item | $value"` per line, prize 1 = grand prize
    - `set1` / `set2`: `"Song | Artist"` per line
- **Scene renderers** are in `SCN[kind]`. `show(pos,dir)` handles navigation and transitions; `pos={s,k}` is the scene and step.
- **Scenes in order:**
  1. Countdown
  2. Title
  3. Opening Address (Prof Gan Chee Lip)
  4. Welcome Speech (Prof Lalit Goel)
  5. Dinner board
  6. R13 Band, set 1
  7. R12 Game One (15 min)
  8. Lucky Draw Round 1 (prizes 10→6)
  9. R12 Game Two (20 min)
  10. Montage (video)
  11. Lucky Draw Round 2 (prizes 5→1)
  12. Graduation Speech
  13. R13 Band Finale, set 2
  14. Final ("Last Call")
  15. End
- **The lucky draw** takes 3 steps per prize: sealed → reveal (`revealItem`) → draw (`runDraw`). The draw is a split-flap name spin. Picks use `crypto.getRandomValues`, and winners are excluded from later draws (`pool()`). A results board follows the last prize.
- **The closing scene** comes from `ENDS[TUNE.end]`; there are 10 treatments in `END_LIST`.
  - The chosen one is **`runway`**: flight **REP12** to **FUTURE**, `runGate` then `runwayPainter`.
  - The gate flips 22:00 BOARDING → FINAL CALL.
- **Band visualiser:** on a band scene, pressing B opens `vizOn()`. The chosen concept is the **oscilloscope** (`VIZ.scope`).
  - The audio source `Aud` is the live mic (`Mic`, getUserMedia) or a simulated 118 bpm band.
  - The mic works only in `RENight-2026.html` opened in Chrome. It does not work in the claude.ai artifact.
- **Running late:** `TUNE.late` (in minutes). Every on-screen time must pass through **`SH(t)`**, so wrap any new time display in it. `applyLate()` re-renders.
- **Sound:** Web Audio `clack`, `chime`, `chord` and `tone`. M toggles it.
- **Persistence:**
  - `localStorage['renight26']` holds `{S, W, sound, TUNE, pos}`.
  - IndexedDB `renight2026`, store `img`, holds prize photos as `p1`..`p10` and the montage video as `montage`.
  - Built-in prize photos live in `PHOTO` (WebP data URIs keyed by prize number, black-and-white product cutouts, vouchers drawn as cards with the logo cut out). An upload in IndexedDB overrides one (`UPL[n]`).
  - `migratePrizes()` / `PRIZE_SWAP` rename replaced prizes in an older saved prize list.
  - Storage is per browser and per file location. The claude.ai artifact and the local file don't share it.
- **Tuning:** `TUNE = {pair, cell, v, end, viz, rehearsal, late}`. `load()` migrates old saves. Rehearsal mode means draws re-run instead of keeping winners; it defaults to off.
- **Fonts:** `PAIRS` has 5 pairings, toggled in the D panel. The default is **Renaissance** (Cormorant Garamond + Jost). Fonts load from Google Fonts and are **not embedded**, so open the show online once to cache them, or embed them as base64 `@font-face` if you have network access.

## Operator keys

| Key | Action |
|---|---|
| → / PageDown / click | Next |
| ← / PageUp | Back |
| Space | Start/stop the game timer, or mark the next song as playing |
| F | Fullscreen |
| B | Blank the stage (on band scenes, opens the visualiser) |
| V | Mic on/off |
| M | Sound on/off |
| R | Redraw the current prize |
| ] / [ | Running late, plus or minus 5 minutes |
| G | Navigator: jump to any scene or step |
| S | Setup: pre-show check, start time, names, prizes, set lists, prize photos, montage video |
| D | Fonts and tuning: pairing, dither size, rehearsal, late slider, closing scene |
| H | Help |
| 0 0 | Restart at the countdown |
| 9 9 | Jump to the end |

The owner uses a Mac with no Home key. The dev panels (S, D, G, H) stay in by request.

## Testing

Tests ran in headless Chromium via Python Playwright. The pattern: load the file with `set_content` (wrapped), then jump to a scene and take a screenshot.

```python
await pg.evaluate("busy=false;pos={s:7,k:0};show(pos,1)")   # Lucky Draw Round 1
```

Also:
- Check JS syntax before shipping: `new Function(scriptBody)`.
- Watch `pageerror`.
- Timers in scenes must be registered with `onClean` / `runCleanups`, or guarded with a null check. Re-rendering a scene while its old timeouts are pending caused a null-element bug on the dinner board.

## State at handover (11 Nov 2026)

Done and approved:
- Monolith and departure-board direction
- Countdown that holds at 00:00 and then reads "Please take your seats."
- Running-late offset
- Bigger board strip
- Pre-show check in Setup
- Montage video saved in IndexedDB
- Prize photo slots
- Runway closing
- Oscilloscope visualiser
- Set lists
- Navigator

Known loose ends:
- Fonts aren't embedded; this is the biggest risk if the venue laptop is offline.
- Long names get truncated in the strip, e.g. "LUCKY DRAW, ROU…".
- The montage video must be added via Setup in the **local file**, on the show laptop, before the event. Prize photos are built in.
- Before going live, confirm rehearsal mode is off: the pre-show check shows it.

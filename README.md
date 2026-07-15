# Fury On · 狂怒觉醒

A **mobile-first 2.5D read-the-tell fighting game** — single-player Boss match.  
Built around hand-drawn animation frames, cinematic camera work, and a high-detail arena backdrop.

**Core loop:** see the wind-up → block / dodge → punish.

| | |
|--|--|
| English title | **Fury On** |
| Chinese title | **狂怒觉醒** |
| Entry file | [`v2.0.html`](./v2.0.html) |
| Languages | Chinese / English (remembered; first launch picker) |
| Modes | Solo Boss · first to 2 rounds |
| Difficulty | Easy / Normal / Nightmare |
| Challenges | Optional rules linked to achievements |

---

## Overview

Play as **Yi (宜)** against **Lilith (莉莉丝)**.  
**First to 2 rounds** wins the match (timed rounds). Each round can shift arena theme; Lilith escalates through up to **three phases**.

Gameplay pillars:

1. Read Boss wind-ups  
2. Block / dodge / punish  
3. Pressure with a 3-hit string, Iai, dash slash, and ultimate  

### What’s in this build

| Feature | Detail |
|---------|--------|
| **Boss phases** | Phase 2 (charm) · Phase 3 (void) + domain hazards |
| **Arenas** | Moon cliff · blood sanctum · void — rotate by round |
| **Difficulty** | Easy / Normal / Nightmare — BAL + AI (localStorage) |
| **Challenges** | Flawless, no hazard, time limits, no ult, sweep, combo, PF×5… |
| **Records** | **Per-difficulty** win rate, W–L, best clear time + global combo |
| **Achievements** | Local unlocks + toast queue + linked challenge clears |
| **Share card** | Canvas result card · save PNG / system share |
| **Audio** | WebAudio SFX · master volume + mute (persisted) |
| **Haptics** | Mobile `vibrate` on/off (persisted) |
| **Language** | Remembered (`furyon_lang`); title/result toggle |
| **Progressive load** | Critical → high → lazy; combat throttles bg load |
| **Scenes** | Title / result illustration overlays; result art lazy-loaded |
| **PWA** | Install to home screen + offline shell/assets |
| **Mobile layout** | Full-bleed battle canvas + floating 9-pad |
| **UI** | Compact glass setup card · segmented difficulty · rematch can re-pick settings |

- **Visuals**: design-locked hand-drawn sprites + true L/R art sets + concept backdrop + procedural 2.5D stage  
- **Controls**: 9-pad (mobile) / keyboard (PC)  
- **Deploy**: static site (GitHub Pages works) — host `assets/` next to the HTML  

No multiplayer (by design for this demo).

---

## Characters

| Role | Chinese | English | Design notes |
|------|---------|---------|--------------|
| Player | **宜** | **Yi** | Short blonde hair, white camisole, black shorts, single odachi |
| Boss | **莉莉丝** | **Lilith** | Succubus queen: horns, bat wings, sheer robe, heart-tipped whip |

Master design sheets:

- `assets/hero_design.png`  
- `assets/beast_design.png`  

Full frame list: [`assets/SPRITE_MANIFEST.md`](./assets/SPRITE_MANIFEST.md).

---

## Boss phases & arenas

### Phases (HP thresholds depend on difficulty)

| Phase | Default (Normal) | Behaviour |
|-------|------------------|-----------|
| **1** | Full HP | Base moveset — whip, grab, rock, leap, shell |
| **2 · Charm** | ≤ 50% HP | Faster AI, new tools (heart rain, bind, charge, nova…), **heartfire domain** |
| **3 · Void** | ≤ 25% HP | Void tools (void storm, throne crush, soul chain…), **void flame domain**, tighter arena pinch |

Lilith’s ultimate **Heart’s End (魅心终焉)** becomes available when her rage meter is full.

### Arenas (by round)

| Round | Theme | Notes |
|-------|--------|--------|
| 1 | **Moon cliff** `moon_cliff` | Default cool night grade |
| 2 | **Blood sanctum** `blood_sanctum` | Warm / crimson grade |
| 3+ | **Void** `void` | Purple void grade |

Themes are code tints + filters on the shared concept plate (`arena_bg.png`); no extra backdrop files required.

---

## Difficulty

Pick on the **title screen** or **result screen** (before rematch). Stored in `localStorage` (`furyon_diff_v1`).

| ID | UI | Player | Boss | AI feel |
|----|-----|--------|------|---------|
| `easy` | **简单 · Easy** | +HP | Lower HP / damage; slower wind-ups; later phases | Slower think, less dodge / ult |
| `normal` | **标准 · Normal** | Baseline | Baseline (`BAL` defaults) | Baseline |
| `hard` | **噩梦 · Nightmare** | −HP | Higher HP / damage; faster wind-ups; earlier phases | Aggressive; denser pressure (hazard ticks tuned) |

Implementation: `DIFF_PRESETS` → `applyDifficulty()` mutates runtime `BAL`. Monster AI reads multipliers each match.

---

## Challenges (optional)

Optional rules on the glass **setup card** (title + result). Stored in `furyon_challenge_v1`.  
Live HUD tracks progress; some rules **hard-fail** mid-match (e.g. took damage on Flawless).

| Challenge | Condition | Linked achievement |
|-----------|-----------|-------------------|
| Free | No extra rules | — |
| Flawless | 0 combat damage taken | 无伤通关 |
| No hazard | 0 heartfire / void flame damage | 心焰零伤 |
| 60s / 45s limit | Clear under time | 疾风斩 / 瞬杀 |
| No ult | Ultimate banned | 无剑通关 |
| Clean sweep | Win 2–0 | 三局完封 |
| PF×5 / Combo 10 | Thresholds | 完美闪达人 / 连击达人 |

Clearing **3 different challenges** unlocks **挑战达人**. Challenge clears are stored under `challengeClears` in the records blob.

---

## Local records & achievements

Key: `furyon_records_v1` (browser only).

### Records by difficulty

| Column | Per Easy / Normal / Hard |
|--------|--------------------------|
| Win rate | Yes |
| Best clear time | Yes |
| W–L | Yes |

Global footer: total matches · total W–L · max combo.  
Legacy saves without `byDiff` migrate totals into **Normal**.

### Achievements (sample)

First win · flawless · no hazard · clean sweep · void hunter · speed 60/45 · combo 10/15 · perfect dodge 5 · ten wins · no ult · challenge trio  

On unlock: **toast queue** + result banner. Title page shows a compact chip board.

### Share card

Result screen → **Share**: 900×1200 canvas card (portrait, rank, stats, difficulty, challenge, new achievements).  
Save PNG and/or Web Share API when available.

---

## Settings & persistence

| Key | Content |
|-----|---------|
| `furyon_lang` | `zh` \| `en` |
| `furyon_diff_v1` | Difficulty id |
| `furyon_challenge_v1` | Challenge id |
| `furyon_records_v1` | Records, achievements, `byDiff`, `challengeClears` |
| `furyon_audio_v1` | `{ muted, volume }` master gain |
| `furyon_vibrate_v1` | `{ enabled }` mobile haptics |

**Language priority:** `?lang=zh|en` → localStorage → first-launch picker.  
Title/result: language toggle, volume/mute, vibrate.  
Combat HUD: quick mute + vibrate. Pause: volume + vibrate.

---

## Quick start

### Local play

1. Serve the project root with a static server (recommended; some browsers restrict `file://`):

```bash
# either works
npx --yes serve .
python -m http.server 8080
```

2. Open the printed URL and go to **`v2.0.html`**.  
3. Language (if first visit) → pick difficulty / challenge → wait for load bar → start.

You can also double-click `v2.0.html` (if assets fail to load, use a static server).

### GitHub Pages

Publish at least:

- `v2.0.html`  
- `assets/` (all PNGs — especially `arena_bg.png` and character frames)  
- `manifest.webmanifest` · `sw.js` · `icons/` (PWA)

After updating art, hard-refresh or bump the asset query stamp (`?v=...`) in the HTML.

### PWA (Add to Home Screen · offline)

| File | Role |
|------|------|
| [`manifest.webmanifest`](./manifest.webmanifest) | App name, icons, standalone display |
| [`sw.js`](./sw.js) | Caches shell + `assets/*` for offline play |
| [`icons/`](./icons/) | 192 / 512 / Apple touch icons |

**Requirements:** serve over **HTTPS** (or `localhost`). `file://` cannot register a service worker.

**Install (mobile):** open the site → browser menu → **Add to Home Screen** / **Install app**.  
**Offline:** open once online so shell + played sprites are cached; later visits work without network (new uncached frames still need a first fetch).

After shipping a new SW, bump `CACHE_VER` inside `sw.js` so clients drop old caches.

---

## Controls

### Mobile · 9-pad

```
 1 Iai      2 Jump     3 Ultimate
 4 Left     5 Slash    6 Right
 7 Dodge L  8 Block    9 Dodge R
```

Layout: battle canvas is full-bleed; the pad floats over the lower area so the fight viewport stays large.

### PC

| Key | Action |
|-----|--------|
| **W A S D** | Jump / Left / Block / Right |
| **J** | Slash (primary attack) |
| **K / L** | Dodge left / right |
| **U** | Iai (hold to parry projectiles) |
| **I** | Ultimate · Fury Blades |
| **S + J** | Throw (breaks shell / turtling) |
| **Esc** | Pause / resume |

Numpad / top-row **1–9** also map to the pad.

### Yi’s toolkit

| Move | Input | Notes |
|------|-------|--------|
| 3-hit string | Mash **J** | Fast / mid / heavy; cancel windows between hits |
| Dimension slash | Direction + **J** | Dash attack; costs SP, has CD |
| Sword Qi | Hold **J** on ground, release | Projectile |
| Iai | **U** / pad 1 | Omni slash; charged Iai can parry ranged orbs |
| Ultimate | **I** / pad 3 | Spend full meter for the blade storm |
| Double jump | **W** in air | Extra air mobility |
| Air dodge | **K/L** while airborne | Air invuln dodge |

**Cancel priority (feel):** dodge > skill > ult > jump > dash slash > light chain > charge  

Boss wind-ups show on-screen hints. **Grab-style moves cannot be blocked** — dodge or jump. Domain zones (heartfire / void flame): jump out or leave the red/purple ground.

### Combat readability

- HUD pills: **i-frames** (player) · **armor** / **rage full** (Boss)  
- Perfect dodge: confirm window + chase hint + pad suggest  
- Hitstop / camera scaled by move weight (`HIT_FEEL`)

---

## Loading pipeline

Sprites load in tiers so the match can start before every PNG is ready:

| Tier | Role |
|------|------|
| **CRITICAL** | Idle / core attacks / arena — gates “Start fight” |
| **HIGH** | Common combat frames — background after critical |
| **LAZY** | Large result / title art — only when a scene needs them |

During combat, background loading concurrency is reduced so frame time stays stable. Missing files fall back to `SPRITE_FALLBACK` / skeleton.

---

## Tech stack

| Area | Details |
|------|---------|
| Runtime | Single-page HTML + Canvas 2D — no build step, no framework |
| Sprites | Prefer `assets/*.png`; missing frames use `SPRITE_FALLBACK` / embedded pack |
| Facing | Separate `_L` / `_R` art (avoids mirrored sword / whip hands) |
| Backdrop | `assets/arena_bg.png` + procedural 2.5D stage / props + arena tints |
| Lighting | Offscreen character grading, contact shadows, bloom, post grade |
| Audio | Web Audio synthesis only; `masterGain` volume / mute |
| Haptics | `navigator.vibrate` gated by setting |
| Balance | `BAL` + `DIFF_PRESETS` + `CHALLENGE_PRESETS` in the HTML |
| Persistence | Keys listed under [Settings & persistence](#settings--persistence) |
| i18n | `tr()` lookup; Chinese is the source string key |
| PWA | Manifest + service worker |
| UI | Title/result glass cards, segmented difficulty, share modal |

Optional: built-in Spine-lite skeleton fallback (hand-drawn art is preferred).

---

## Project layout

```
fireman/
├── v2.0.html              # Game entry (UI + combat + AI + render)
├── manifest.webmanifest   # PWA manifest
├── sw.js                  # Service worker (offline assets)
├── icons/                 # PWA / home-screen icons
├── README.md
├── note.md                # Feel / art iteration notes (Chinese)
└── assets/
    ├── SPRITE_MANIFEST.md # Frame catalog
    ├── hero_design.png    # Yi master design
    ├── beast_design.png   # Lilith master design
    ├── arena_bg.png       # Concept backdrop
    ├── hero_*.png         # Yi frames (incl. L/R)
    ├── beast_*.png        # Lilith frames (incl. L/R)
    ├── result_*.png       # Result portraits
    └── _*.py              # Optional art pipeline scripts
```

### Useful pipeline scripts (`assets/`)

| Script | Purpose |
|--------|---------|
| `_install_design_frames.py` | Install design-locked key frames, cutout, scale, L/R, seed family |
| `_install_key_actions.py` | Key action frames (Iai / ult / grab / cast…) |
| `_install_loco_atk.py` | Locomotion + light-string frames |
| `_fix_walk_L_from_R.py` | Mirror walk L from R |
| `_fix_jump_L_from_R.py` | Mirror jump L from R |
| `_seed_all_sides.py` | Seed side variants from bases |
| `_pack_v2.py` | Pack / strip huge base64 pack for lighter HTML |

---

## Art guidelines

1. **Design lock**: all frames must match `hero_design` / `beast_design` — no mixed styles.  
2. **Specs**: Yi ~280×512; Lilith ~300×600; full-body, transparent BG; L/R sets in game (true facing for weapons).  
3. **Backdrop**: `arena_bg` owns sky/cliffs/ruins; code owns the walkable 2.5D stage, near props, and arena colour grades.  
4. **After swaps**: bump the `?v=` cache stamp in `v2.0.html`, or hard-refresh; bump `CACHE_VER` in `sw.js` if PWA clients stick on old files.

---

## Development notes

- Combat feel / balance history: [`note.md`](./note.md) (Chinese).  
- Dev console helpers: `dumpFightLog()` / `fightSnapshot()`.  
- Release tip: keep sprites as external `assets/` files; avoid packing huge base64 into the HTML.  
- Tune combat in `BAL`; difficulty in `DIFF_PRESETS`; challenges in `CHALLENGE_PRESETS`.  

### Possible next steps (not implemented)

- Skip-able tutorial / training mode  
- Quality tier (low-end particle / DPR)  
- BGM loops (still SFX-only today)  
- Second Boss or weekly challenges  
- Smaller first paint (split pack / externalize more art)  
- Gamepad mapping  

---

## License & assets

Personal / experimental fighting demo.  
Character and scene art are project-specific. Confirm rights before redistribution.

---

## Credits

Read-the-tell structure, hand-drawn frame pipeline, 2.5D staging, cinematic camera, multi-phase Boss / arenas, difficulty + challenges, per-difficulty records, achievements & share cards, progressive loading, audio/haptics settings, and PWA packaging define the current build.

Fork freely: retune `BAL` / `DIFF_PRESETS` / `CHALLENGE_PRESETS`, swap `assets`, or finish unique poses for every remaining frame.

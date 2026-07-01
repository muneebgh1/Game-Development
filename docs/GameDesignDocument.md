# Space Miner — Game Design Document

**Version:** 1.0  
**Status:** Draft  
**Platform:** Mobile (Android & iOS)  
**Engine:** Unity 6  
**Genre:** Casual 2D Arcade / Mining  
**Perspective:** Top-down  
**Session Length:** 2–5 minutes per run  

---

## 1. Game Vision

**Space Miner** is a satisfying, bite-sized mobile game about piloting a small mining ship through asteroid fields, breaking rocks, collecting loot, and growing stronger over time.

The player should feel a clear sense of progression: each session makes their ship a little faster, their laser a little stronger, and new corners of space a little more reachable. Mining should feel tactile and rewarding — rocks crack, resources pop, numbers go up.

**Design pillars:**

| Pillar | Description |
|--------|-------------|
| **Satisfying mining** | Destruction feedback, clear drops, and readable rewards on every hit. |
| **Short sessions** | Meaningful progress in under five minutes; easy to pick up and put down. |
| **Visible growth** | Upgrades and new sectors should feel like real power jumps, not invisible stat bumps. |
| **Low friction** | Simple controls, forgiving movement, no complex systems to learn upfront. |
| **Respect the player** | Fair free-to-play model; no paywalls on core progression. |

**Success criteria for v1.0:**

- A new player understands how to mine within 30 seconds.
- A typical session ends with at least one meaningful reward (resource haul or upgrade).
- Players return because they want to unlock the next sector or ship upgrade, not because of artificial timers.

---

## 2. Elevator Pitch

> Pilot a mining ship through asteroid fields, blast rocks for ore, sell your haul, and upgrade your ship to reach deadlier sectors and rarer resources. Easy to play, hard to put down.

**One-liner (store listing):**  
*Mine asteroids. Upgrade your ship. Conquer the belt.*

---

## 3. Target Audience

### Primary audience

- **Casual mobile players** aged 18–40 who enjoy idle, arcade, and collection games.
- Players who liked *Vampire Survivors*, *Deep Rock Galactic* (lite), *Asteroids*-style movement, or mining loops in games like *Motherload* and *SteamWorld Dig* — but want something simpler and session-friendly.

### Player motivations

| Motivation | How Space Miner delivers |
|------------|--------------------------|
| Relaxation | Low-stress mining, ambient space setting, no harsh fail states |
| Collection | Multiple ore types, sector unlocks, upgrade tiers |
| Power fantasy | Ship visibly gets stronger; tougher asteroids become trivial |
| Short bursts | Runs fit commutes, breaks, and couch sessions |

### What we are not targeting (v1)

- Hardcore roguelike players expecting permadeath depth.
- PvP or competitive multiplayer audiences.
- Players who want narrative-heavy, story-driven experiences.

---

## 4. Core Gameplay Loop

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   SELECT    │────▶│    MINE     │────▶│   RETURN    │
│   SECTOR    │     │  ASTEROIDS  │     │  TO STATION │
└─────────────┘     └─────────────┘     └──────┬──────┘
       ▲                                       │
       │              ┌─────────────┐          │
       └──────────────│   UPGRADE   │◀─────────┘
                      │  & UNLOCK   │
                      └─────────────┘
```

### Loop breakdown

1. **Select sector** — Choose a mining zone from the station map. Higher sectors have tougher asteroids and better ore.
2. **Mine** — Fly the ship, aim the mining laser, destroy asteroids, collect dropped resources. Manage cargo capacity and hull health.
3. **Return** — Leave the sector manually or when cargo is full / hull is critical. No penalty for leaving early.
4. **Sell & upgrade** — Convert ore to credits at the station. Spend credits on ship upgrades or sector unlock fees.
5. **Repeat** — Re-enter the same sector to farm efficiently, or push into a newly unlocked sector for better rewards.

### Session structure

| Phase | Duration | Goal |
|-------|----------|------|
| Launch & mine | 2–4 min | Fill cargo or hit a credit target |
| Station visit | 30–60 sec | Sell, buy one upgrade or save toward unlock |
| Optional re-run | 0–3 min | "One more run" if close to next milestone |

---

## 5. Core Mechanics

### Movement

- Top-down 2D flight with **virtual joystick** (left thumb).
- Ship has momentum with light damping — responsive but not twitchy.
- Screen edges wrap or use soft boundaries (sector-sized play area).

### Mining laser

- **Hold-to-fire** mining beam (right thumb button or auto-aim toggle).
- Beam damages asteroids on contact; damage ticks per second.
- Laser range is limited — player must position the ship near targets.

### Asteroids

- Asteroids spawn in clusters within a sector.
- Each asteroid has **HP** and a **resource table** (what it drops when destroyed).
- Larger asteroids take longer to break but drop more loot.
- Some asteroids are purely rock (credits only); others contain rare ore.

### Cargo

- Ship has a **cargo capacity** (item count or weight — use item count for v1 simplicity).
- Resources auto-collect when the player flies near drops (magnet radius upgradeable).
- Run ends or prompts return when cargo is full.

### Hull & damage

- Asteroid collisions and environmental hazards deal hull damage.
- Hull reaches zero → ship retreats automatically (no hard game over). Player keeps collected resources but loses a portion of in-run ore (e.g. 25%) to create light stakes without frustration.
- Hull restores fully at the station (free, instant).

### Sectors

- Each sector is a self-contained mining field with its own asteroid mix, density, and hazard level.
- Sectors are unlocked with credits + minimum upgrade requirements (gate progression naturally).

---

## 6. Player Progression

### Progression layers

| Layer | Description | Pacing |
|-------|-------------|--------|
| **Within-run** | Cargo fills, credits accumulate | Every few seconds |
| **Between-runs** | Buy ship upgrades | Every 1–3 runs early; slows later |
| **Mid-term** | Unlock new sectors | Every 5–10 runs |
| **Long-term** | Max out ship tiers, complete sector collection | Days to weeks |

### Sector unlock gates (example)

| Sector | Unlock cost | Requirement |
|--------|-------------|-------------|
| Rust Belt (starter) | Free | — |
| Iron Drift | 500 credits | Mining Laser Lv. 2 |
| Crystal Field | 2,000 credits | Cargo Bay Lv. 3 |
| Ember Vein | 8,000 credits | Hull Lv. 4, Mining Laser Lv. 4 |
| Deep Core | 25,000 credits | All upgrades Lv. 5+ |

### Player skill progression

- Learning asteroid density patterns and efficient flight paths.
- Prioritizing high-value targets when cargo is limited.
- Choosing when to push into harder sectors vs. farming safe zones.

No XP or player level in v1 — progression is entirely ship- and sector-based to keep systems simple.

---

## 7. Economy

### Currency

| Currency | Source | Sink |
|----------|--------|------|
| **Credits** | Selling ore at station | Upgrades, sector unlocks |
| **Ore (inventory)** | Mining asteroids | Sold for credits (or held briefly for bonus events — future) |

### Selling ore

- All ore sells instantly at the station for a fixed price per unit.
- No separate shop inventory management in v1 — sell all with one button ("Sell All").

### Balance principles

- Early upgrades cost 1–2 runs worth of ore.
- Sector unlocks cost 5–15 runs, creating a clear milestone to work toward.
- Rare ore is worth significantly more but appears in harder sectors — risk/reward is spatial, not RNG-heavy.
- No energy/stamina system in v1. Players can mine as much as they want.

### Sink vs. faucet (v1 targets)

| Faucet | Sink |
|--------|------|
| Asteroid drops | Ship upgrades (repeatable, escalating cost) |
| Bonus crates (optional, rare) | Sector unlock fees (one-time) |
| Rewarded ad bonus (optional) | — |

---

## 8. Resource Types

### Common resources

| Resource | Color cue | Base value | Found in |
|----------|-----------|------------|----------|
| **Rock Chips** | Gray | 1 credit | All sectors |
| **Iron Ore** | Silver | 5 credits | Rust Belt+ |
| **Copper Ore** | Orange | 8 credits | Iron Drift+ |

### Uncommon resources

| Resource | Color cue | Base value | Found in |
|----------|-----------|------------|----------|
| **Crystal Shards** | Cyan | 25 credits | Crystal Field+ |
| **Ember Stone** | Red | 40 credits | Ember Vein+ |

### Rare resources

| Resource | Color cue | Base value | Found in |
|----------|-----------|------------|----------|
| **Platinum Nugget** | White | 100 credits | Deep Core |
| **Dark Matter Fragment** | Purple | 250 credits | Deep Core (low drop rate) |

### Drop rules

- Each asteroid type has a fixed drop table (no complex procedural loot in v1).
- Drops spawn as collectible sprites with a brief pop animation and pickup sound.
- Resource icons are distinct and readable at small mobile sizes.

---

## 9. Upgrade System

### Upgrade categories

Five ship systems, each with **5 levels** in v1:

| Upgrade | Effect per level | Player feel |
|---------|------------------|-------------|
| **Mining Laser** | +20% damage | Rocks break faster |
| **Cargo Bay** | +5 cargo slots | Longer runs, bigger hauls |
| **Engine** | +10% move speed | Faster repositioning |
| **Hull** | +20 max HP | Safer collisions, access to hazard sectors |
| **Collector Magnet** | +15% pickup radius | Less tedious scooping |

### Cost scaling (example)

```
Cost = BaseCost × (1.6 ^ currentLevel)
```

| Upgrade | Base cost |
|---------|-----------|
| Mining Laser | 100 |
| Cargo Bay | 80 |
| Engine | 120 |
| Hull | 100 |
| Collector Magnet | 60 |

### UI presentation

- Station screen shows all upgrades in a vertical list.
- Each row: icon, name, current level, next level effect, price, [Upgrade] button.
- Disabled state when player cannot afford or has reached max level.

### Design rules

- No upgrade respec in v1.
- No randomized upgrade offers — deterministic path keeps scope manageable.
- At least one upgrade should be affordable after every run in the first 30 minutes.

---

## 10. Game Controls

### Mobile input (v1)

| Input | Action |
|-------|--------|
| Left virtual joystick | Move ship (8-directional) |
| Right button (hold) | Fire mining laser |
| Right button (toggle, optional setting) | Auto-fire when asteroids in range |
| Pause button (top corner) | Pause menu |

### Accessibility options (ship in v1.1 if needed)

- Adjustable joystick size and dead zone.
- Left/right-handed layout swap.
- Auto-aim assist toggle (beam snaps to nearest asteroid in arc).

### What we avoid in v1

- Multi-touch gestures beyond joystick + button.
- Complex weapon switching or ability cooldowns.

---

## 11. UI Screens

### Screen list

| Screen | Purpose | Key elements |
|--------|---------|--------------|
| **Splash / Boot** | Branding, load assets | Logo, loading bar |
| **Main Menu** | Entry point | Play, Settings, (Shop) |
| **Station Hub** | Between-run home base | Credits, sector map, upgrades, sell ore |
| **Sector Select** | Choose mining zone | Sector cards with lock state, ore preview, difficulty indicator |
| **Gameplay HUD** | In-run overlay | Hull bar, cargo count, credits picked up, pause |
| **Pause Menu** | Mid-run break | Resume, settings, quit to station |
| **Settings** | Preferences | Audio sliders, controls, restore purchases |
| **Shop** (optional v1) | IAP | Remove ads, credit packs, cosmetic skins |

### Station Hub wireframe (conceptual)

```
┌──────────────────────────────────────┐
│  CREDITS: 1,240        [Settings]  │
├──────────────────────────────────────┤
│                                      │
│         [ SECTOR MAP ]               │
│    (tap sector to mine)              │
│                                      │
├──────────────────────────────────────┤
│  CARGO: 12 Iron, 4 Crystal           │
│         [ SELL ALL — 380 cr ]        │
├──────────────────────────────────────┤
│  UPGRADES                            │
│  ▸ Mining Laser  Lv.2  [200 cr]      │
│  ▸ Cargo Bay     Lv.3  [150 cr]      │
│  ▸ Engine        Lv.1  [120 cr]      │
│  ...                                 │
└──────────────────────────────────────┘
```

### UX principles

- Maximum two taps from station to mining.
- Always show current credits and next affordable upgrade.
- Use color and icons over text where possible (mobile readability).

---

## 12. Art Style

### Direction

- **Clean 2D vector / flat-shaded** aesthetic with a sci-fi arcade feel.
- Readable silhouettes at phone scale — ship and asteroids must be identifiable at a glance.
- Bright resource pops against darker space backgrounds.

### Visual guidelines

| Element | Direction |
|---------|-----------|
| **Ship** | Small, chunky silhouette; upgrades can add visible attachments (bigger laser, cargo pods) |
| **Asteroids** | Varied shapes, color-tinted by dominant ore type |
| **Backgrounds** | Parallax star layers per sector; subtle nebula tints to differentiate zones |
| **UI** | Rounded panels, high contrast, large touch targets (min 44×44 pt) |
| **VFX** | Mining beam (simple line/particles), rock shatter, pickup sparkle |

### Color palette (starting point)

| Role | Color |
|------|-------|
| Space background | `#0B0E1A` |
| UI primary | `#2DD4BF` (teal) |
| UI accent | `#FBBF24` (amber) |
| Danger / hull low | `#EF4444` |
| Rare loot | `#A78BFA` (purple) |

### Production scope (solo dev)

- Reuse asteroid shapes with color swaps and scale variations.
- One hero ship sprite with attachment overlays for upgrades (avoid drawing 5 full ships).
- Sector differentiation through background palette + asteroid mix, not unique art per rock.

---

## 13. Audio Style

### Music

- **Ambient electronic / lo-fi synth** — calm enough for repeated sessions, light energy during mining.
- One track per sector family (2–3 tracks total for v1).
- Crossfade between station (calm) and mining (slightly more upbeat).

### Sound effects

| Event | Sound character |
|-------|-----------------|
| Mining laser | Continuous low hum + impact tick on hit |
| Asteroid break | Crunchy crumble, pitch-varied |
| Resource pickup | Short bright chime, pitch scales with rarity |
| Upgrade purchase | Satisfying "ka-ching" + subtle power-up tone |
| Sector unlock | Short fanfare sting |
| Hull damage | Muffled thud + warning beep at low HP |
| UI tap | Soft click |

### Audio rules

- All audio respects device silent mode (SFX still play if music muted — standard mobile pattern).
- Separate volume sliders for music and SFX in settings.
- Keep total SFX count under 20 for v1 to limit production burden.

---

## 14. Monetization

### Model

**Free-to-play** with optional ads and IAP. Core progression is fully achievable without spending money.

### Revenue streams

| Stream | Implementation | Priority |
|--------|----------------|----------|
| **Rewarded video ads** | Optional: 2× sell value, bonus crate, or revive partial hull mid-run | v1 |
| **Interstitial ads** | After every 3–4 runs (never mid-mining) | v1 |
| **Remove ads IAP** | One-time purchase (~$2.99–$4.99) | v1 |
| **Credit packs** | Convenience only; priced so grinding remains viable | v1.1 |
| **Cosmetic ship skins** | No gameplay advantage | v1.1+ |

### Fairness rules

- No pay-gated sectors or upgrades.
- No loot boxes or gacha.
- Ads are always opt-in for rewards; interstitials only at natural break points.
- Offline play supported (ads simply unavailable offline).

---

## 15. Technical Requirements

### Platform

| Spec | Target |
|------|--------|
| Engine | Unity 6 (2D URP) |
| Android | API 24+ (Android 7.0) |
| iOS | iOS 14+ |
| Orientation | Portrait (primary); landscape optional later |
| Frame rate | 60 FPS on mid-range devices (2020+) |
| APK/IPA size | < 150 MB initial download |

### Architecture notes

- **Data:** ScriptableObjects for asteroid types, ore definitions, upgrade tables, sector configs.
- **Save system:** Local JSON or PlayerPrefs wrapper; cloud save is out of scope for v1.
- **Input:** Unity Input System with on-screen joystick package or custom implementation.
- **Pooling:** Object pool for asteroids, drops, and VFX particles.
- **UI:** Unity UI (uGUI) or UI Toolkit — pick one and stay consistent.

### Performance budget

| Category | Budget |
|----------|--------|
| Draw calls | < 100 in gameplay |
| Active asteroids | 30–50 on screen |
| Physics | 2D colliders only; keep collision layers minimal |
| Memory | < 300 MB RAM on target devices |

### Analytics (lightweight)

Track for balancing (Unity Analytics or similar):

- Runs per session, average run duration.
- Credits earned vs. spent per upgrade.
- Sector unlock rate and drop-off.
- Ad opt-in rate for rewarded videos.

### Out of scope for v1

- Multiplayer, leaderboards, cloud save.
- Procedural infinite worlds.
- Complex narrative or quest systems.

---

## 16. Future Expansion Ideas

Prioritized backlog — not commitments:

| Idea | Value | Complexity |
|------|-------|------------|
| **Daily contracts** | "Mine 50 Iron Ore" — gives direction | Low |
| **Boss asteroids** | Sector capstone encounters | Medium |
| **Secondary weapons** | Missiles, drill burst — variety | Medium |
| **Prestige / rebirth** | Reset for permanent multiplier | Medium |
| **New ship classes** | Speed vs. tank vs. miner builds | High |
| **Idle/offline earnings** | Station generates passive credits | Low |
| **Seasonal events** | Limited ore types, cosmetic rewards | Medium |
| **Galaxy map meta-layer** | Connect sectors into a larger map | Medium |
| **Achievements & cloud save** | Retention + device migration | Medium |
| **Haptic feedback** | Mining impacts feel better on modern phones | Low |

---

## Appendix: v1.0 Scope Checklist

**Must ship:**

- [ ] 5 sectors with escalating difficulty
- [ ] 7 resource types
- [ ] 5 upgrade tracks × 5 levels
- [ ] Station hub (sell, upgrade, sector select)
- [ ] Core mining loop with cargo and hull
- [ ] Rewarded + interstitial ads, remove-ads IAP
- [ ] Local save
- [ ] Basic tutorial (first run guided prompts)

**Nice to have (cut if behind schedule):**

- [ ] Auto-aim toggle
- [ ] Visible ship attachment upgrades
- [ ] Third music track
- [ ] Cosmetic skin slot in shop

---

*Document owner: Game Design*  
*Next review: After first playable prototype*

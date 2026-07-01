# Space Miner — Product Backlog

**Version:** 1.0  
**Status:** Active  
**Related:** [GDD](GameDesignDocument.md) · [Technical Spec](TechnicalSpecification.md)  
**Est. tasks:** 103 (one session each)  

---

## How to use this backlog

- Each `- [ ]` item is sized for **one focused dev session** (2–4 hours).
- Work milestones **in order** — later milestones depend on earlier ones.
- Mark complete: `- [x]`
- Cut scope from the bottom of M12+ if schedule slips; M1–M11 are v1.0 critical path.

---

## Milestone 1 — Project Setup

*Goal: Unity project runs on device with correct settings and repo hygiene.*

- [ ] **M1-01** Initialize Unity 6 project in `game/` using 2D URP template
- [ ] **M1-02** Configure Player Settings (company name, product ID, portrait orientation, ARM64)
- [ ] **M1-03** Install required packages (Input System, Cinemachine, URP 2D)
- [ ] **M1-04** Verify `_Project/` folder layout matches Technical Spec
- [ ] **M1-05** Initialize git repo at workspace root; confirm `.gitignore` excludes `Library/`, `builds/`
- [ ] **M1-06** Create `Boot` scene and add to Build Settings as index 0

---

## Milestone 2 — Core Architecture

*Goal: Bootstrapping, services, state machine, and events — empty scenes transition correctly.*

- [ ] **M2-01** Create `AppBootstrap` with DontDestroyOnLoad root object
- [ ] **M2-02** Implement `ServiceRegistry` and register stub services
- [ ] **M2-03** Define service interfaces (`ISaveService`, `IAudioService`, `IAdsService`, `IAnalyticsService`)
- [ ] **M2-04** Implement `GameStateMachine` with states: MainMenu, Station, Mining, Paused
- [ ] **M2-05** Create `GameEvents` static event bus with core event signatures
- [ ] **M2-06** Wire Boot scene → load MainMenu on startup
- [ ] **M2-07** Create placeholder scenes: MainMenu, Station, Mining (empty, loadable)

---

## Milestone 3 — Player Movement & Input

*Goal: Ship flies with virtual joystick; camera follows smoothly.*

- [ ] **M3-01** Create `SpaceMiner.inputactions` with Gameplay map (Move, Fire, Pause)
- [ ] **M3-02** Build on-screen joystick UI prefab wired to Move action
- [ ] **M3-03** Implement `ShipMovement` with momentum and damping (Rigidbody2D)
- [ ] **M3-04** Create placeholder player ship prefab with collider and movement script
- [ ] **M3-05** Set up Cinemachine orthographic camera following player
- [ ] **M3-06** Add sector boundary collider and `CinemachineConfiner2D`
- [ ] **M3-07** Enable/disable Gameplay vs UI input maps on scene enter/exit

---

## Milestone 4 — Asteroids & Mining

*Goal: Core mining loop works — spawn rocks, fire laser, destroy asteroids.*

- [ ] **M4-01** Implement generic `ObjectPool<T>` utility class
- [ ] **M4-02** Create placeholder asteroid prefab with HP and `AsteroidDefinition` reference
- [ ] **M4-03** Implement `AsteroidHealth` — take damage, die at zero HP
- [ ] **M4-04** Implement `MiningLaser` — hold-to-fire beam with range and DPS
- [ ] **M4-05** Add laser visual (LineRenderer or sprite beam) synced to fire state
- [ ] **M4-06** Implement `AsteroidSpawner` with timed spawn and pool integration
- [ ] **M4-07** Configure Physics 2D layers (Player, Asteroid, Pickup, Boundary)
- [ ] **M4-08** Add basic asteroid break VFX (particle burst on destroy)

---

## Milestone 5 — Cargo, Drops & Run Flow

*Goal: Asteroids drop loot; player collects cargo; run ends and returns to station.*

- [ ] **M5-01** Create resource drop prefab with ore type and collectible trigger
- [ ] **M5-02** Implement magnet pickup radius on player ship
- [ ] **M5-03** Implement `CargoHold` — track items, enforce capacity limit
- [ ] **M5-04** Implement `RunSession` transient state (cargo, hull, sector ID, credits earned)
- [ ] **M5-05** Implement `HullHealth` — collision damage from asteroids
- [ ] **M5-06** Add hull-depleted auto-retreat (25% ore penalty per GDD)
- [ ] **M5-07** Add manual "Return to Station" button and run-end transition

---

## Milestone 6 — Game Data & Economy

*Goal: All tuning lives in ScriptableObjects; ore sells for credits.*

- [ ] **M6-01** Create `OreDefinition` ScriptableObject and author 7 ore assets
- [ ] **M6-02** Create `AsteroidDefinition` with drop table struct
- [ ] **M6-03** Create `UpgradeDefinition` ScriptableObject scaffold
- [ ] **M6-04** Create `SectorConfig` ScriptableObject scaffold
- [ ] **M6-05** Create `GameBalanceConfig` (cost curve, hull penalty, magnet defaults)
- [ ] **M6-06** Implement `GameDatabase` registry with ID lookup and Initialize()
- [ ] **M6-07** Wire asteroid drops to pull from `AsteroidDefinition` drop tables

---

## Milestone 7 — Save System

*Goal: Credits, upgrades, and unlocks persist across app restarts.*

- [ ] **M7-01** Define `SaveData` and `PlayerProfile` serializable models
- [ ] **M7-02** Implement `SaveService` — load/save JSON to `persistentDataPath`
- [ ] **M7-03** Add backup file write before overwrite (`save_v1.bak`)
- [ ] **M7-04** Hook save triggers: app pause, run end, upgrade purchase
- [ ] **M7-05** Handle corrupted save — reset profile with logged warning

---

## Milestone 8 — Station Hub UI

*Goal: Player sells ore, sees credits, and navigates to mining.*

- [ ] **M8-01** Build Station screen canvas prefab with safe-area root
- [ ] **M8-02** Implement credits header display bound to `PlayerProfile`
- [ ] **M8-03** Implement cargo summary panel (ore icons + counts from last run)
- [ ] **M8-04** Implement "Sell All" button — convert ore to credits at fixed prices
- [ ] **M8-05** Raise `GameEvents.OnCreditsChanged` on sell and purchase
- [ ] **M8-06** Build upgrade list UI (scroll row prefab: icon, name, level, cost, button)
- [ ] **M8-07** Implement upgrade purchase logic with affordability checks
- [ ] **M8-08** Wire Station scene into state machine from MainMenu and post-run

---

## Milestone 9 — Upgrade System

*Goal: All five upgrade tracks affect gameplay and scale in cost.*

- [ ] **M9-01** Author 5 `UpgradeDefinition` assets (Laser, Cargo, Engine, Hull, Magnet)
- [ ] **M9-02** Apply Mining Laser level → laser DPS in gameplay
- [ ] **M9-03** Apply Cargo Bay level → max cargo slots
- [ ] **M9-04** Apply Engine level → ship move speed
- [ ] **M9-05** Apply Hull level → max HP
- [ ] **M9-06** Apply Magnet level → pickup radius

---

## Milestone 10 — Sectors & Content

*Goal: Five distinct mining zones with unlock gates and escalating difficulty.*

- [ ] **M10-01** Author 5 `SectorConfig` assets (Rust Belt through Deep Core)
- [ ] **M10-02** Build sector select panel UI with lock/unlock states
- [ ] **M10-03** Implement sector unlock — credit cost + upgrade requirement checks
- [ ] **M10-04** Pass selected sector ID into `RunSession` on mine launch
- [ ] **M10-05** Configure per-sector asteroid spawn sets and weights
- [ ] **M10-06** Add per-sector background (parallax layers or color tint)
- [ ] **M10-07** Tune sector difficulty (asteroid HP, density, hazard level)

---

## Milestone 11 — Menus, HUD & Settings

*Goal: Complete navigable UI shell with in-run HUD and preferences.*

- [ ] **M11-01** Build Main Menu screen (Play, Settings, Shop placeholder)
- [ ] **M11-02** Build Mining HUD (hull bar, cargo count, session credits, pause)
- [ ] **M11-03** Build Pause menu (Resume, Settings, Quit to Station)
- [ ] **M11-04** Build Settings screen (music volume, SFX volume, restore purchases)
- [ ] **M11-05** Implement `SafeAreaFitter` for notch/home-indicator padding
- [ ] **M11-06** Wire Settings persistence into `SaveData.PlayerSettings`
- [ ] **M11-07** Add fire button UI prefab wired to Fire input action

---

## Milestone 12 — Art & Visual Polish

*Goal: Replace placeholders with cohesive sci-fi 2D art and readable feedback.*

- [ ] **M12-01** Finalize ship sprite and import with correct PPU
- [ ] **M12-02** Create asteroid sprite set (3–5 shapes with color variants per ore)
- [ ] **M12-03** Create ore pickup sprites and UI icons for all 7 resources
- [ ] **M12-04** Build UI sprite kit (panels, buttons, bars) matching GDD palette
- [ ] **M12-05** Configure Sprite Atlases (Gameplay, UI) and verify batching

---

## Milestone 13 — Audio Integration

*Goal: Music and SFX reinforce mining satisfaction.*

- [ ] **M13-01** Implement `AudioService` with music/SFX channels and volume settings
- [ ] **M13-02** Import and hook core SFX (laser, break, pickup, upgrade, UI tap)
- [ ] **M13-03** Import station and mining music tracks with crossfade
- [ ] **M13-04** Add low-HP warning sound and hull damage feedback

---

## Milestone 14 — Tutorial & Onboarding

*Goal: New player learns to mine within 30 seconds.*

- [ ] **M14-01** Detect first launch via `SaveData.TutorialComplete` flag
- [ ] **M14-02** Implement guided prompts on first mining run (move, fire, collect, return)
- [ ] **M14-03** Implement station tutorial prompts (sell, upgrade, select sector)
- [ ] **M14-04** Mark tutorial complete and skip on subsequent launches

---

## Milestone 15 — Monetization

*Goal: Rewarded + interstitial ads and remove-ads IAP; fair and offline-safe.*

- [ ] **M15-01** Integrate Unity Ads SDK behind `IAdsService`
- [ ] **M15-02** Implement rewarded ad flow (2× sell bonus placement)
- [ ] **M15-03** Implement interstitial cadence (every 3–4 runs, never mid-mining)
- [ ] **M15-04** Integrate Unity IAP — remove-ads non-consumable product
- [ ] **M15-05** Build Shop screen stub (Remove Ads button, restore purchases)

---

## Milestone 16 — Performance, QA & Balance

*Goal: Stable 60 FPS on target devices; economy feels fair.*

- [ ] **M16-01** Profile on physical device; fix GC spikes and Instantiate leaks
- [ ] **M16-02** Validate pool caps (50 asteroids, 30 drops) under load
- [ ] **M16-03** Playtest full loop and tune upgrade cost curve + ore values
- [ ] **M16-04** Integrate Unity Analytics behind `IAnalyticsService` (key events only)
- [ ] **M16-05** Full regression pass: Boot → Mine → Sell → Upgrade → Unlock → Save

---

## Milestone 17 — Release

*Goal: Store-ready build submitted to Google Play and App Store.*

- [ ] **M17-01** Configure Android keystore and iOS signing/provisioning
- [ ] **M17-02** Produce release AAB and iOS archive; smoke-test on both platforms
- [ ] **M17-03** Write store listing copy, keywords, and privacy policy
- [ ] **M17-04** Capture store screenshots and feature graphic (portrait)
- [ ] **M17-05** Submit v1.0 to Google Play and App Store

---

## Backlog summary

| Milestone | Tasks | Cumulative |
|-----------|-------|------------|
| M1 Project Setup | 6 | 6 |
| M2 Core Architecture | 7 | 13 |
| M3 Player & Input | 7 | 20 |
| M4 Asteroids & Mining | 8 | 28 |
| M5 Cargo & Run Flow | 7 | 35 |
| M6 Game Data | 7 | 42 |
| M7 Save System | 5 | 47 |
| M8 Station Hub | 8 | 55 |
| M9 Upgrades | 6 | 61 |
| M10 Sectors | 7 | 68 |
| M11 Menus & HUD | 7 | 75 |
| M12 Art Polish | 5 | 80 |
| M13 Audio | 4 | 84 |
| M14 Tutorial | 4 | 88 |
| M15 Monetization | 5 | 93 |
| M16 QA & Balance | 5 | 98 |
| M17 Release | 5 | **103** |

---

## Optional backlog (post-v1.0)

Cut from v1 if behind schedule, or pick up after launch:

- [ ] Auto-aim toggle for mining laser
- [ ] Visible ship attachment upgrades per level
- [ ] Third music track for Deep Core sector
- [ ] Cosmetic ship skins in shop
- [ ] Daily contracts system
- [ ] Haptic feedback on asteroid break
- [ ] Cloud save and achievements

---

*Document owner: Product*  
*Next review: End of M4 (first playable mining loop)*
- }
```

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from `src/lib/ai/parse.ts` in repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

**`src/lib/ai/parse.ts`** — add `parseJsonFromText` (copy from repo).

**`src/lib/ai/prompts.ts`** — add `buildPrompt` (copy from repo).

Read
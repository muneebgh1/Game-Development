# Space Miner — Technical Specification

**Version:** 1.0  
**Status:** Draft  
**Related:** [Game Design Document](GameDesignDocument.md)  
**Audience:** Solo developer / technical reference  

---

## 1. Engine

| Item | Choice |
|------|--------|
| **Engine** | Unity 6 (6000.x LTS when available; otherwise latest stable Unity 6) |
| **Render pipeline** | Universal Render Pipeline (URP) — 2D Renderer |
| **Target platforms** | Android (API 24+), iOS (14+) |
| **Orientation** | Portrait (locked for v1) |
| **Color space** | Linear |
| **API compatibility** | .NET Standard 2.1 (Unity default) |

### Project creation settings

When creating the Unity project in `game/`:

1. Template: **2D (URP)** or **2D Core** then add URP manually.
2. Enable **Input System** (prompt to replace or use Both during setup).
3. Add **Android** and **iOS** modules via Unity Hub.
4. Set company name and product name: `Space Miner`.

### Build configuration

| Setting | Value |
|---------|-------|
| Scripting backend (Android) | IL2CPP |
| Target architectures (Android) | ARM64 only (drop ARMv7 to reduce size) |
| Minimum iOS version | 14.0 |
| Graphics APIs (Android) | Vulkan preferred, OpenGLES3 fallback |
| Managed stripping level | Medium (re-test saves and IAP after changes) |

---

## 2. Programming Language

| Item | Choice |
|------|--------|
| **Language** | C# |
| **IDE** | Visual Studio / Rider / VS Code with C# Dev Kit |
| **Nullable reference types** | Disabled (Unity default) — avoid friction with serialized fields |
| **Async** | `async`/`await` only for non-Unity-main-thread work (file I/O, web). Gameplay stays coroutine- or frame-driven. |

### Assembly definitions

Split code into small asmdefs to keep compile times fast as the project grows:

| Assembly | Path | References |
|----------|------|------------|
| `SpaceMiner.Core` | `Scripts/Core/` | None (base) |
| `SpaceMiner.Gameplay` | `Scripts/Gameplay/` | Core |
| `SpaceMiner.UI` | `Scripts/UI/` | Core |
| `SpaceMiner.Services` | `Scripts/Services/` | Core |
| `SpaceMiner.Editor` | `Assets/Editor/` | All runtime assemblies (Editor only) |

Add asmdefs when the script count exceeds ~15–20 files, or from day one if you prefer clean boundaries.

---

## 3. Project Architecture

### Philosophy

Use a **lightweight layered architecture** — enough structure to stay maintainable, no enterprise patterns that slow a solo dev down.

- **No full ECS** for v1. The game scope (one ship, pooled asteroids, menu screens) fits classic OOP + components.
- **No heavy DI framework.** Use a small service registry initialized at boot.
- **Data-driven tuning** via ScriptableObjects; runtime state in plain C# models.
- **Scene flow** handled by a single `GameStateMachine` rather than scattered `SceneManager` calls.

### High-level diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        BOOT SCENE                           │
│  AppBootstrap → ServiceRegistry → SaveLoad → MainMenu       │
└─────────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
   │  Main Menu  │    │   Station   │    │  Mining Run │
   │   (UI)      │    │   (UI)      │    │ (Gameplay)  │
   └─────────────┘    └─────────────┘    └─────────────┘
          │                   │                   │
          └───────────────────┴───────────────────┘
                              │
                    GameStateMachine
                    EventBus (pub/sub)
                    SaveData (persisted)
                    GameDatabase (SO configs)
```

### Core systems

| System | Responsibility |
|--------|----------------|
| **AppBootstrap** | First scene entry; creates DontDestroyOnLoad root, registers services, loads save. |
| **GameStateMachine** | States: `MainMenu`, `Station`, `Mining`, `Paused`. Controls scene loads and input maps. |
| **ServiceRegistry** | Holds references to `ISaveService`, `IAudioService`, `IAdsService`, etc. |
| **GameDatabase** | Runtime lookup of ScriptableObject configs (ores, upgrades, sectors). |
| **RunSession** | Transient state for an active mining run (cargo, hull, sector ID). Discarded on return. |
| **PlayerProfile** | Persistent state (credits, upgrade levels, unlocked sectors). Serialized to disk. |
| **EventBus** | Decoupled notifications (cargo changed, credits changed, asteroid destroyed). |

### Scene plan

| Scene | Location | Loaded by |
|-------|----------|-----------|
| `Boot` | `Scenes/Boot/` | Build index 0 — always first |
| `MainMenu` | `Scenes/Menus/` | State machine |
| `Station` | `Scenes/Menus/` | State machine |
| `Mining` | `Scenes/Levels/` | State machine (sector ID passed via RunSession) |

Use **additive loading only if needed** (e.g. shared EventSystem). For v1, single-scene swaps are simpler.

### Gameplay object model

```
MiningScene
├── Systems (empty GameObjects)
│   ├── AsteroidSpawner
│   ├── DropCollector
│   └── RunTimer
├── PlayerShip (prefab)
│   ├── ShipMovement
│   ├── MiningLaser
│   ├── HullHealth
│   └── CargoHold
├── VirtualControls (UI canvas)
└── WorldBounds
```

Asteroids and resource drops come from **object pools**, not per-frame `Instantiate`/`Destroy`.

---

## 4. Folder Organization

Workspace and Unity project are separated at the repository root. See [README](../README.md) for the full tree.

### Unity scripts (convention)

Scripts mirror the asmdef layout under `Assets/_Project/Scripts/`:

```
Scripts/
├── Core/
│   ├── Bootstrap/
│   ├── StateMachine/
│   ├── Events/
│   ├── Pooling/
│   └── Utilities/
├── Gameplay/
│   ├── Player/
│   ├── Asteroids/
│   ├── Mining/
│   ├── Drops/
│   └── Sectors/
├── UI/
│   ├── Screens/
│   ├── HUD/
│   └── Widgets/
└── Services/
    ├── Save/
    ├── Audio/
    ├── Ads/
    └── Analytics/
```

### Naming conventions

| Asset type | Convention | Example |
|------------|------------|---------|
| Scenes | PascalCase | `Station.unity` |
| Prefabs | PascalCase + type suffix | `PlayerShip.prefab`, `Asteroid_Large.prefab` |
| ScriptableObjects | PascalCase + `Config`/`Definition` | `IronOreDefinition.asset`, `SectorConfig_RustBelt.asset` |
| Scripts | PascalCase class = file name | `ShipMovement.cs` |
| Sprites | snake_case or PascalCase (pick one, stay consistent) | `ore_iron.png` |
| Audio | snake_case | `sfx_asteroid_break.wav` |

### Data assets

All tunable game data lives in `Assets/_Project/Data/`:

```
Data/
├── Ores/
├── Asteroids/
├── Upgrades/
├── Sectors/
└── GameBalance.asset          # Global tuning (cost curves, hull penalty %)
```

Third-party SDKs and asset packs go in `Assets/ThirdParty/` — never mix with `_Project/`.

---

## 5. Coding Standards

### Style

- Follow [Microsoft C# conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) with Unity adaptations.
- **One public class per file.** File name matches class name.
- **Serialize fields** with `[SerializeField] private` instead of public fields.
- Use `readonly` for references set in `Awake`/`Construct` where possible.

### Unity-specific rules

```csharp
// Prefer serialized references over Find/search
[SerializeField] private Rigidbody2D _rigidbody;

// Cache component references in Awake
private void Awake() => _rigidbody = GetComponent<Rigidbody2D>();

// Avoid logic in Update when FixedUpdate or events suffice
private void FixedUpdate() => ApplyMovement();

// Use CompareTag instead of tag ==
if (other.CompareTag("Asteroid")) { ... }
```

### Architecture rules

| Rule | Rationale |
|------|-----------|
| Gameplay scripts do not call `SceneManager` directly | Route through `GameStateMachine` |
| UI scripts do not modify `PlayerProfile` directly | Call a service or presenter method |
| No `FindObjectOfType` in Update | Cache on init or inject via inspector |
| Pool pooled objects; don’t Instantiate gameplay entities in loops | Mobile GC pressure |
| Magic numbers live in ScriptableObjects or constants class | Tunable without code changes |

### Comments and documentation

- **XML docs** on public service interfaces only — not every method.
- Use `// TODO:` sparingly with your name/tag: `// TODO(munee): hook up ads callback`.
- Keep comments for *why*, not *what*.

### Error handling

- Validate inspector references in `OnValidate()` (Editor) and log clear errors at runtime if missing.
- Save system: fail safe — corrupted save resets to new profile with a one-time user notice.
- Ads/IAP: always handle unavailable/offline gracefully (GDD: offline play supported).

---

## 6. ScriptableObject Usage

ScriptableObjects are the **design-time source of truth** for static game data. Runtime progression lives in `PlayerProfile`.

### Asset types

| ScriptableObject | Purpose | Key fields |
|------------------|---------|------------|
| `OreDefinition` | Resource metadata | `id`, `displayName`, `icon`, `sellPrice`, `rarityColor` |
| `AsteroidDefinition` | Asteroid variant | `prefab`, `maxHp`, `dropTable[]`, `spawnWeight` |
| `DropTableEntry` | (serializable struct) | `ore`, `minCount`, `maxCount`, `chance` |
| `UpgradeDefinition` | Upgrade track | `id`, `displayName`, `maxLevel`, `effectPerLevel[]`, `baseCost`, `costMultiplier` |
| `SectorConfig` | Mining zone | `id`, `displayName`, `unlockCost`, `requiredUpgrades[]`, `asteroidSpawnSet`, `backgroundPrefab` |
| `GameBalanceConfig` | Global constants | `hullLossOrePenalty`, `defaultMagnetRadius`, `interstitialFrequency` |

### Registry pattern

A single `GameDatabase` ScriptableObject holds lists of all definitions and builds lookup dictionaries at init:

```csharp
[CreateAssetMenu(menuName = "SpaceMiner/Game Database")]
public class GameDatabase : ScriptableObject
{
    public List<OreDefinition> Ores;
    public List<UpgradeDefinition> Upgrades;
    public List<SectorConfig> Sectors;

    private Dictionary<string, OreDefinition> _oreLookup;

    public void Initialize()
    {
        _oreLookup = Ores.ToDictionary(o => o.Id);
    }

    public OreDefinition GetOre(string id) => _oreLookup[id];
}
```

Place one `GameDatabase` asset in `Data/` and reference it from `AppBootstrap`.

### Rules

| Do | Don’t |
|----|-------|
| Store definitions and tuning curves in SOs | Store player credits or upgrade levels in SOs |
| Use `CreateAssetMenu` for easy authoring | Mutate SO fields at runtime (breaks in-editor values in builds) |
| Reference SOs from prefabs and scenes | Use `Resources.Load` |
| Version breaking changes by adding new SO fields with defaults | Rename `id` strings after ship — breaks saves |

### Optional: ScriptableObject events

For inspector-friendly wiring (e.g. audio reacts to game events), use SO-based event channels. Prefer the code `EventBus` for most v1 systems to reduce asset sprawl.

---

## 7. Save System

### Requirements (from GDD)

- Local persistence only for v1 — no cloud save.
- Persist: credits, upgrade levels, unlocked sectors, settings, ads-remove IAP flag, tutorial completion.
- Do **not** persist: in-run cargo, hull, active sector session.

### Format

| Item | Choice |
|------|--------|
| **Serializer** | `JsonUtility` (sufficient for flat v1 data; upgrade to Newtonsoft if schema grows) |
| **File path** | `Application.persistentDataPath + "/save_v1.json"` |
| **Backup** | Copy to `save_v1.bak` before overwrite |

### Data schema

```csharp
[Serializable]
public class SaveData
{
    public int Version = 1;
    public long Credits;
    public List<UpgradeSaveEntry> Upgrades;
    public List<string> UnlockedSectorIds;
    public PlayerSettings Settings;
    public bool AdsRemoved;
    public bool TutorialComplete;
}

[Serializable]
public class UpgradeSaveEntry
{
    public string UpgradeId;
    public int Level;
}
```

### Service interface

```csharp
public interface ISaveService
{
    PlayerProfile Profile { get; }
    void Load();
    void Save();
    void ResetProfile();
}
```

### When to save

| Trigger | Action |
|---------|--------|
| Return to station from mining run | Save |
| Purchase upgrade or sector unlock | Save immediately |
| IAP completed (remove ads) | Save immediately |
| App pause / focus loss (mobile) | Save |
| Settings changed | Save |

### Migration

- `SaveData.Version` increments on breaking schema changes.
- `ISaveService` runs a small migration chain: `v1 → v2` etc.
- Unknown version: backup file, create fresh profile, log warning.

---

## 8. Input System

### Package

**Unity Input System** (`com.unity.inputsystem`) — not legacy `Input Manager`.

### Action maps

| Action Map | Used in | Actions |
|------------|---------|---------|
| `Gameplay` | Mining scene | `Move` (Vector2), `Fire` (Button), `Pause` (Button) |
| `UI` | Menus, station | Standard UI navigation (pointer, submit, cancel) |

### Mobile bindings

| Action | Binding |
|--------|---------|
| `Move` | On-screen joystick (`OnScreenStick` or custom UI joystick → `InputActionReference`) |
| `Fire` | UI button → `IsPressed` while held |
| `Pause` | UI button top-right |

Use **one** `InputActionAsset` stored in `Assets/_Project/Settings/Input/SpaceMiner.inputactions`.

### State machine integration

```csharp
// Enable/disable maps per state
private void EnterMining()
{
    _inputActions.UI.Disable();
    _inputActions.Gameplay.Enable();
}
```

### Design notes

- Joystick dead zone: 0.1–0.15 (tune in asset).
- `Move` feeds `ShipMovement` via `ReadValue<Vector2>()` in `FixedUpdate`.
- Auto-fire (nice-to-have): gameplay reads a bool from settings, not a separate action.
- Test with **Input System Device Simulator** and on-device early.

---

## 9. Camera System

### Approach

**Cinemachine 3** (`com.unity.cinemachine`) with a single **2D orthographic** virtual camera following the player ship.

| Setting | Value |
|---------|-------|
| Projection | Orthographic |
| Follow target | `PlayerShip` transform |
| Follow damping | Light (0.2–0.5s) — smooth but responsive |
| Look ahead | Optional small offset based on velocity |
| Confiner | `CinemachineConfiner2D` bound to sector play area |

### Camera rig hierarchy

```
Main Camera (CinemachineBrain)
└── CM PlayerFollow (CinemachineCamera)
    └── Confiner shape (sector bounds collider)
```

### Rules

- One active virtual camera per scene — no blending needed for v1.
- Station and menus use static cameras (no Cinemachine required).
- Pixel-perfect optional: add `Pixel Perfect Camera` component only if using pixel art with fixed PPU; skip for smooth vector art (GDD direction).

### Screen shake (optional v1.1)

Brief noise on asteroid destruction via Cinemachine Impulse — low priority, cut if behind schedule.

---

## 10. UI Framework

### Choice

**Unity UI (uGUI)** with **TextMeshPro** for all text.

| Reason | Detail |
|--------|--------|
| Ecosystem | Most mobile Unity tutorials and assets assume uGUI |
| Maturity | Battle-tested on touch devices |
| Solo scope | UI Toolkit has a steeper learning curve for custom mobile layouts |

### Canvas setup

| Canvas | Render mode | Purpose |
|--------|-------------|---------|
| `ScreenSpace_Overlay` | Overlay | Menus, station, HUD |
| Sort order | HUD above world-space damage numbers if any |

### Structure per screen

```
StationScreen (root)
├── Background
├── Header (credits, settings)
├── SectorPanel
├── CargoPanel
└── UpgradeList (scroll view)
```

Each major screen is a **prefab** in `Prefabs/UI/` with a single controller script (`StationScreen.cs`).

### Patterns

- **Screen controller** owns button wiring and calls services — not individual buttons scattered across scenes.
- Use **object pooling** for upgrade list rows if the list grows; for 5 upgrades, simple Instantiate is fine.
- Disable raycast on decorative images (`Raycast Target` off) to reduce touch overhead.
- Safe area: apply `Screen.safeArea` padding via a root `SafeAreaFitter` component on all full-screen canvases (notch/home indicator).

### Fonts

- One TMP font asset for body, one for headings (or font weight variants of the same family).
- Include fallback glyphs for currency symbols.

---

## 11. Event System

### Approach

Use a **typed static EventBus** for gameplay decoupling. Lightweight, no external package, easy to debug.

```csharp
public static class GameEvents
{
    public static event Action<int> OnCreditsChanged;
    public static event Action<string, int> OnCargoChanged;
    public static event Action OnRunEnded;

    public static void RaiseCreditsChanged(int total) => OnCreditsChanged?.Invoke(total);
}
```

### Subscription rules

| Rule | Reason |
|------|--------|
| Subscribe in `OnEnable`, unsubscribe in `OnDisable` | Prevents leaks on pooled/disabled objects |
| UI listens; services raise | Clear data flow |
| Don’t pass UnityEngine.Object through events if avoidable | Reduces coupling |

### When to use direct references instead

- Tight loops (mining laser hitting asteroid every frame) — call methods directly, not events.
- Parent → child on same prefab — direct reference.

### Unity EventSystem (UI)

The scene `EventSystem` + `InputSystemUIInputModule` handles pointer/touch for uGUI. This is separate from `GameEvents` — don’t confuse the two.

---

## 12. Performance Goals

Aligned with [GDD §15](GameDesignDocument.md#15-technical-requirements):

| Metric | Target |
|--------|--------|
| Frame rate | 60 FPS on mid-range 2020+ devices |
| Draw calls | < 100 during gameplay |
| Active asteroids | 30–50 max |
| RAM | < 300 MB |
| APK/AAB size | < 150 MB |
| Cold start | < 3 seconds to main menu on target hardware |

### Pooling requirements

| Pool | Initial size | Notes |
|------|--------------|-------|
| Asteroids | 20 | Expand on demand, cap at 50 |
| Resource drops | 30 | Recycle on pickup timeout |
| VFX particles | 10 per type | Or use single shared particle system |
| UI float text | 5 | Optional damage numbers |

### Physics

- **Physics 2D** only — `Rigidbody2D` on ship, kinematic or dynamic based on feel tests.
- Asteroids: static colliders until hit, or kinematic if moving.
- Layer matrix: `Player`, `Asteroid`, `Pickup`, `Boundary` — disable unnecessary layer pairs in Project Settings.

### Profiling cadence

- Use **Unity Profiler** on device build weekly once gameplay exists.
- Watch: GC.Alloc spikes, `Instantiate` calls, overdraw on fill-rate limited phones.

---

## 13. Mobile Optimization

### Rendering (URP 2D)

- **Sprite Atlas** per category (ship, asteroids, UI, VFX) — batch draw calls.
- One shared **Lit/Unlit 2D material** for most sprites unless shaders differ.
- Disable HDR and unused URP features in the 2D Renderer asset.
- Limit overdraw: avoid full-screen transparent UI panels stacking unnecessarily.

### CPU

- Movement and mining in `FixedUpdate` (50 Hz default).
- Spawner ticks on a timer (e.g. every 0.25s), not every frame.
- Avoid LINQ and string concatenation in hot paths.
- Use `Animator` sparingly — prefer simple code-driven transforms for asteroids.

### Memory

- Audio: compress music (Vorbis/MP3), short SFX (ADPCM/PCM based on length).
- Texture max size: 2048 for UI backgrounds, 512 for icons, 256 for small sprites.
- Unload unused scenes after transition (`Resources.UnloadUnusedAssets` optional on low-end).

### Battery and thermal

- Target 60 FPS but cap simulation if needed on thermal throttling (future: 30 FPS fallback setting).
- `Application.targetFrameRate = 60` in boot; use `OnDemandRendering` only if battery testing shows issues.

### Platform specifics

| Platform | Note |
|----------|------|
| Android | Publish AAB; test GLES3 and Vulkan devices |
| iOS | Respect safe area; test silent switch behavior for audio (GDD) |
| Both | `Application.runInBackground = false` to save battery |

---

## 14. Version Control Strategy

### Repository layout

The git root is the **workspace** (`SpaceMiner/`), not only `game/`.

```
SpaceMiner/          ← git root
├── game/            ← tracked (except Unity-generated)
├── docs/            ← tracked
├── prompts/         ← tracked
├── .cursor/rules/   ← tracked
├── marketing/       ← tracked (source files; large exports optional LFS)
├── builds/          ← gitignored
└── .gitignore
```

### Git ignore (already configured)

Ignore: `game/Library/`, `game/Temp/`, `game/Logs/`, `game/UserSettings/`, `builds/*` binaries, IDE `.csproj`/`.sln` (regenerated by Unity).

### Branching (solo)

| Branch | Use |
|--------|-----|
| `main` | Always shippable; tagged releases |
| `dev` | Daily work (optional — solo can work on `main` with discipline) |
| `feature/*` | Larger features (mining prototype, station UI) |

**Recommendation for solo:** Work on `main` or a single `dev` branch. Tag milestones: `v0.1.0-prototype`, `v0.5.0-beta`, `v1.0.0`.

### Commits

- Commit message format: `type: short description` — e.g. `feat: asteroid pooling`, `fix: save corruption on Android pause`.
- Commit `Packages/manifest.json` and `Packages/packages-lock.json`.
- Commit `ProjectSettings/` changes intentionally — review before commit.

### Git LFS (optional)

Enable LFS only if repo contains large PSD/WAV/FBX source files. For a lean Unity project with compressed game assets, regular git is usually enough. Keep marketing 4K videos out of git.

### Unity version

Commit `ProjectSettings/ProjectVersion.txt`. Pin editor version in `README` to avoid surprise upgrades.

---

## 15. Third-Party Packages

Keep dependencies minimal. Prefer Unity-maintained packages.

### Required (v1)

| Package | ID | Purpose |
|---------|-----|---------|
| Input System | `com.unity.inputsystem` | Touch + action maps |
| Cinemachine | `com.unity.cinemachine` | Camera follow |
| TextMeshPro | `com.unity.ugui` (TMP included) | UI text |
| 2D Sprite | `com.unity.2d.sprite` | Core 2D |
| 2D Animation | `com.unity.2d.animation` | Optional — only if using bone animation |
| URP | `com.unity.render-pipelines.universal` | Rendering |

### Monetization & services (add when implementing §14 GDD)

| Package | ID | Purpose |
|---------|-----|---------|
| Unity Ads | `com.unity.ads` | Rewarded + interstitial |
| Unity IAP | `com.unity.purchasing` | Remove-ads purchase |
| Unity Analytics | `com.unity.services.analytics` | Lightweight telemetry |

Wrap each in a `Services` interface (`IAdsService`, `IIapService`) so SDK calls are isolated and mockable in Editor.

### Optional (evaluate later)

| Package | Purpose | Verdict |
|---------|---------|---------|
| DOTween (Asset Store / OpenUPM) | UI tweens, juice | Nice polish — not required day one |
| Unity Localization | Multi-language | v1.1+ per GDD |
| Addressables | Remote content | Skip until content size demands it |
| Newtonsoft JSON | Complex save schema | Add only if `JsonUtility` becomes limiting |

### Packages to avoid for v1

- Heavy DI frameworks (Zenject, VContainer) — overkill for this scope.
- Entire UI frameworks on top of uGUI — unnecessary abstraction.
- ECS / DOTS — wrong fit for a casual 2D solo project at this scale.

---

## Appendix A: Boot sequence

```
1. Boot scene loads
2. AppBootstrap.Awake()
   ├── DontDestroyOnLoad(root)
   ├── Register services (Save, Audio, Ads stub)
   ├── GameDatabase.Initialize()
   └── SaveService.Load()
3. If first launch → Tutorial flag check
4. GameStateMachine → MainMenu
```

---

## Appendix B: Service interfaces (starter set)

```csharp
public interface ISaveService { /* see §7 */ }
public interface IAudioService
{
    void PlaySfx(string id);
    void PlayMusic(string id);
    void SetMusicVolume(float v);
    void SetSfxVolume(float v);
}
public interface IAdsService
{
    bool CanShowRewarded { get; }
    void ShowRewarded(string placement, Action<bool> onComplete);
    void ShowInterstitialIfReady();
}
public interface IAnalyticsService
{
    void LogEvent(string name, Dictionary<string, object> parameters = null);
}
```

Implement stubs in Editor that log instead of calling SDKs — enables play-testing without ad network setup.

---

## Appendix C: Definition of Done (technical prototype)

- [ ] Boot → MainMenu → Station → Mining → Station loop loads without errors
- [ ] One sector spawns pooled asteroids; laser mines; drops collect into cargo
- [ ] Return sells ore, credits persist after app restart
- [ ] One upgrade purchase persists and affects gameplay (laser damage)
- [ ] 60 FPS on target test device with 30+ asteroids
- [ ] Portrait safe area respected on notched phone

---

## Appendix D: Related documents

| Document | Location |
|----------|----------|
| Game Design Document | `docs/GameDesignDocument.md` |
| Architecture diagrams (future) | `docs/architecture/` |
| Architecture decisions | `docs/decisions/` |
| Cursor AI rules | `.cursor/rules/space-miner.mdc` |

---

*Document owner: Technical Architecture*  
*Next review: After technical prototype milestone*

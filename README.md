# Game Development

Repositories for game projects by [muneebgh1](https://github.com/muneebgh1).

## Projects

### Space Miner

Professional Unity 6 mobile game — casual 2D asteroid mining.

**Docs:** [Game Design Document](docs/GameDesignDocument.md) · [Technical Specification](docs/TechnicalSpecification.md) · [Product Backlog](docs/ProductBacklog.md)

#### Top-level layout

| Folder | Purpose |
|--------|---------|
| `game/` | Unity 6 project. Open this folder in Unity Hub. |
| `docs/` | All project documentation, kept separate from game assets. |
| `prompts/` | Reusable AI prompts for design, code, art, and marketing. |
| `.cursor/rules/` | Cursor IDE rules for consistent AI-assisted development. |
| `marketing/` | Store listings, screenshots, trailers, and social assets. |
| `builds/` | Exported APK/AAB/IPA builds (not committed to git). |

#### Documentation (`docs/`)

| Folder | Purpose |
|--------|---------|
| `architecture/` | System design, diagrams, tech stack, module boundaries. |
| `game-design/` | GDD, mechanics, progression, economy, level design. |
| `decisions/` | Architecture Decision Records (ADRs) — why choices were made. |
| `research/` | Competitor analysis, market data, feasibility studies. |
| `references/` | Mood boards, inspiration, external links, asset licenses. |
| `logs/` | Dev diaries, playtest notes, sprint retrospectives. |

#### Unity project (`game/`)

| Folder | Purpose |
|--------|---------|
| `Assets/` | All game content and code. |
| `Packages/` | Unity Package Manager manifest and lock file. |
| `ProjectSettings/` | Unity project configuration (input, graphics, build targets). |

##### Assets layout (`game/Assets/`)

| Folder | Purpose |
|--------|---------|
| `_Project/` | First-party game content. Underscore keeps it sorted to the top. |
| `Editor/` | Editor-only scripts (build pipelines, custom inspectors). |
| `Plugins/` | Native plugins and platform-specific binaries. |
| `ThirdParty/` | Imported asset packs and SDKs you did not author. |
| `StreamingAssets/` | Files read at runtime via `Application.streamingAssetsPath`. |

##### Project content (`game/Assets/_Project/`)

| Folder | Purpose |
|--------|---------|
| `Art/Animations` | Animation clips and controllers. |
| `Art/Materials` | Materials and material variants. |
| `Art/Models` | 3D meshes and rigged characters. |
| `Art/Shaders` | Custom shaders and Shader Graph assets. |
| `Art/Sprites` | 2D sprites and sprite atlases. |
| `Art/UI` | UI-specific art (icons, frames, backgrounds). |
| `Audio/Music` | Background music tracks. |
| `Audio/SFX` | Sound effects and UI sounds. |
| `Data/` | ScriptableObjects, configs, tuning tables. |
| `Prefabs/Characters` | Player, enemies, NPCs. |
| `Prefabs/Environment` | Tiles, props, hazards, interactables. |
| `Prefabs/UI` | HUD, menus, popups. |
| `Prefabs/VFX` | Particles, trails, screen effects. |
| `Scenes/Boot` | Initialization and loading scene(s). |
| `Scenes/Menus` | Main menu, settings, shop. |
| `Scenes/Levels` | Gameplay scenes. |
| `Scripts/Core` | Bootstrap, game loop, managers, shared utilities. |
| `Scripts/Gameplay` | Mining, movement, combat, progression logic. |
| `Scripts/UI` | View controllers, HUD, menu logic. |
| `Scripts/Services` | Save, ads, analytics, IAP, cloud backends. |
| `Settings/` | URP assets, Input System actions, quality presets. |

#### Getting started

1. Install **Unity 6** with Android and/or iOS build support.
2. In Unity Hub, add project from `game/`.
3. Create your first scene in `Scenes/Boot/` and add it to Build Settings.

# Feature Flags & Internal Codenames

Feature flags, internal codenames, Swift native APIs, configuration keys, and other internals found in the Claude.app binary.

**Source:** Static analysis of `index.js` extracted from `app.asar`
**Last updated:** 2026-03-19

---

## Table of Contents

1. [Feature Flags](#feature-flags)
2. [Internal Codenames](#internal-codenames)
3. [Swift Native APIs](#swift-native-apis)
4. [Configuration Keys](#configuration-keys)
5. [The Clod Avatar](#the-clod-avatar)
6. [Apple App References](#apple-app-references)

---

## Feature Flags

Boolean switches (default `false`) that gate unreleased or experimental functionality.

### Extensions & Developer Tools

| Flag | Default | Description |
|------|---------|-------------|
| `isDxtEnabled` | `false` | Load browser extensions in app. DXT = Desktop Extension, Anthropic's extension format for the desktop client. |
| `isDxtDirectoryEnabled` | `false` | Enables a browsable extensions directory UI. Separate from `isDxtEnabled`, suggesting a phased rollout (sideloading first, then directory). |
| `isLocalDevMcpEnabled` | `false` | Allows local development MCP servers to connect to the app. For developers building custom integrations. |
| `isUvSystemPythonEnabled` | `false` | Use the system-installed Python for UV runtime extensions instead of a managed installation. |

### Prototypes & Experiments

| Flag | Default | Description |
|------|---------|-------------|
| `isSparkleHedgehogEnabled` | `false` | Activates the sparkleHedgehog prototype. See [codenames](#sparklehedgehog) below. |
| `isMidnightOwlEnabled` | `false` | Activates the midnightOwl prototype. Has a corresponding `setEnabled()` Swift native API method, indicating macOS system-level integration beyond the web layer. |
| `isPhoenixRisingAgainEnabled` | `false` | Debug override for a new auto-updater system. The "Again" suffix indicates this replaces a previous updater. |
| `isChicagoEnabled` | `false` | Forces `chicago_config.enabled` and `teachModeEnabled` to `true` regardless of GrowthBook remote config. Requires restart. |

---

## Internal Codenames

Anthropic uses an adjective+animal naming convention for internal prototypes (with one geographic exception). Each codename has its own configuration keys in the app's settings store.

### sparkleHedgehog

| Key | Default | Type |
|-----|---------|------|
| `sparkleHedgehogAppearance` | `"default"` | string |
| `sparkleHedgehogScale` | `1` | number |

Has an `appearance` setting (implying multiple appearances) and a `scale` factor. The keys are consistent with a UI appearance/theme system or a visual element with configurable size and style.

### plushRaccoon

| Key | Default | Type |
|-----|---------|------|
| `plushRaccoonEnabled` | — | bool |
| `plushRaccoonOption1` | `"off"` | string |
| `plushRaccoonOption2` | `"off"` | string |
| `plushRaccoonOption3` | `"off"` | string |

Three independently togglable sub-options, all defaulting to `"off"` as strings (not booleans), suggesting they may have more states than just on/off. Purpose not determined from binary analysis alone.

### quietPenguin

| Key | Default | Type |
|-----|---------|------|
| `quietPenguinEnabled` | — | bool |

Single on/off toggle. Name suggests notification suppression or a do-not-disturb mode.

### louderPenguin

| Key | Default | Type |
|-----|---------|------|
| `louderPenguinEnabled` | — | bool |

Counterpart to quietPenguin. Same structure. Name suggests enhanced notifications or more aggressive alerting. These two form a notification spectrum: quiet ← normal → louder.

### chillingSloth

| Key | Default | Type |
|-----|---------|------|
| `chillingSlothLocation` | `"default"` | string |

A `location` setting with a `"default"` value. Consistent with an idle-state behavior or ambient UI element that can appear in different positions (toolbar, menu bar, floating overlay, system tray, etc.).

### midnightOwl

| Key | Type |
|-----|------|
| Feature flag: `isMidnightOwlEnabled` | bool |
| Swift API: `midnightOwl.setEnabled()` | method |

Has both a feature flag and a Swift native API `setEnabled()` method through `@ant/claude-swift`, meaning it hooks into macOS at the system level. The "midnight" prefix and native integration are consistent with scheduled background tasks or time-based automation. The existence of `coworkScheduledTasksEnabled` and `ccdScheduledTasksEnabled` config keys (both `false`) suggests scheduled task infrastructure exists in the app.

### floatingAtoll

| Key | Default | Type |
|-----|---------|------|
| `floatingAtollActive` | — | bool |
| *(dimensions)* | 320 × 54 px | hardcoded |
| *(offset)* | 12 px | hardcoded |

A floating UI element with hardcoded pixel dimensions: 320px wide, 54px tall, with a 12px offset. This is the quick-access floating bar — visible to users in Cowork sessions. The naming parallels Apple's Dynamic Island ("atoll" = ring-shaped island). At 320×54px it fits a slim toolbar or single-line input. Corresponds to the `quickAccess` Swift native API.

### chicago

| Key | Default | Type |
|-----|---------|------|
| `chicagoEnabled` | — | bool |
| `chicagoAutoUnhide` | — | bool |
| `chicagoUserDeniedBundleIds` | — | string[] |
| Feature flag: `isChicagoEnabled` | `false` | bool (overrides GrowthBook) |
| Related: `teachModeEnabled` | — | bool |

The most well-documented codename and the only one that breaks the animal naming convention. This is the interactive teach mode / guided walkthrough system (see [computer-use-tools.md](computer-use-tools.md#teach-mode-request_teach_access--teach_step) for the teach mode tools). `chicagoUserDeniedBundleIds` tracks which macOS applications the user has declined to grant Claude access to during walkthroughs.

---

## Swift Native APIs (`@ant/claude-swift`)

The `@ant/claude-swift` module is a native addon bridging Claude's Electron UI with macOS system capabilities.

| API Namespace | Purpose |
|---------------|---------|
| `quickAccess` | Floating quick-access panel. System-level window management for the always-available Claude input (floatingAtoll). |
| `notifications` | macOS native notification delivery via Notification Center. |
| `desktop` | Desktop environment integration — window positioning, display detection. |
| `api` | Direct API communication layer for authenticated requests to Anthropic's backend. |
| `midnightOwl` | Native hook for the midnightOwl prototype. Exposes `setEnabled()`. |
| `vm` | Virtual machine management — `isRunning`, `isGuestConnected`. Handles lifecycle for Cowork's Linux VMs. |
| `hotkey` | Global keyboard shortcut registration. Works even when the app isn't focused. |
| `permissionFixer` | macOS permission remediation. Guides users through granting accessibility, screen recording, or automation permissions. |
| `computerUse` | Computer use capability bindings — screen reading, mouse/keyboard control, GUI automation. Separate SPM product, `computer_use.node`, nest builds only. |

---

## Configuration Keys

Additional configuration keys found in the app's settings store.

### Security & Permissions

| Key | Default | Notes |
|-----|---------|-------|
| `secureVmFeaturesEnabled` | `true` | VM sandboxing — Claude's compute runs in isolation |
| `bypassPermissionsModeEnabled` | `false` | Skip permission checks. Dev-only. |
| `autoPermissionsModeEnabled` | `false` | Auto-grant permissions without user confirmation. Dev-only. |
| `allowAllBrowserActions` | `false` | Unrestricted browser automation |
| `dispatchTrustedCodeWorkspaces` | `[]` | Folders where code execution is trusted without confirmation |
| `localAgentModeTrustedFolders` | `[]` | Folders trusted for local agent operations |

### Launch & Session

| Key | Default | Notes |
|-----|---------|-------|
| `launchEnabled` | `true` | Whether the app is allowed to launch |
| `launchPreviewPersistSession` | `false` | Persist preview sessions across app restarts |

### Capabilities

| Key | Default | Notes |
|-----|---------|-------|
| `coworkWebSearchEnabled` | `true` | Web search in Cowork mode |
| `coworkScheduledTasksEnabled` | `false` | Scheduled/recurring tasks in Cowork |
| `ccdScheduledTasksEnabled` | `false` | Scheduled tasks in Claude Code Desktop |
| `keepAwakeEnabled` | `false` | Prevent system sleep while Claude is working |
| `dockBounceEnabled` | `false` | Bounce Claude's dock icon for attention |

---

## The Clod Avatar

`default.clod` is a zip archive found in the app binary containing assets for an alternative AI persona:

| File | Contents |
|------|---------|
| `personality.txt` | Persona definition ("You are Clod, a minimalist AI assistant...") |
| `cursor.png` | Custom mouse cursor graphic |
| `idling_0.png`, `idling_1.png`, `idling_2.png` | Idle animation frames |
| `working_0.png`, `working_1.png`, `working_2.png`, `working_3.png` | Working animation frames |
| `celebrating_0.png`, `celebrating_1.png` | Success animation frames |
| `error_0.png` | Error state |

"Clod" is a play on "Claude." The `.clod` format is a self-contained avatar package: personality definition plus sprite sheets for different states (idle, working, celebrating, error). The working animation has multiple frames for a looping animation during processing.

This may connect to sparkleHedgehog — the `appearance` and `scale` settings could control which `.clod` avatar is active and how large it renders. The `.clod` format being a zip archive means custom avatar packs could be created and distributed.

---

## Apple App References

String references found in `index.js` suggesting planned or gated native integrations:

Calendar, Reminders, Notes, Contacts, Photos, Health, Maps, Safari, Shortcuts, Music, Freeform, Focus

No tool registration patterns (`name` + `inputSchema`) were found for any of these in the current binary. These are string references only — they may represent planned integrations, configuration labels, or permission identifiers rather than functional tool implementations.

# Cracked Zombies Mod

A Minecraft Forge mod for Minecraft 1.20.1 that upgrades zombie pressure with fast, aggressive, configurable Cracked Zombies.

Current release: **1.0.0**.

This codebase now uses an event-driven runtime architecture (no custom zombie entity class).

## Features

- **Vanilla zombie conversion** — control whether vanilla zombies are allowed to remain vanilla or are converted to Cracked Zombies
- **Cluster spawner** — configurable min/max group size, spawn chance, and spawn distance around players
- **Day spawning option** — keep pressure during daytime by clearing fire on Cracked Zombies
- **High movement speed** — default 0.35 (vanilla zombie is 0.23)
- **Extended aggro & follow range** — detect and chase players from much further away
- **Poison on hit** — configurable duration and amplifier
- **Door busting** — optionally allow zombies to break down doors
- **Creative mode control** — optional spawning while players are in creative mode
- **In-game config screen** — accessible from the Mods menu

## Compatibility

- Minecraft: `1.20.1`
- Forge: `47.4.10+`
- Java: `17`

## Default Config

| Parameter | Default | Description |
|-----------|---------|-------------|
| `minSpawn` | 1 | Min zombies per spawn event |
| `maxSpawn` | 5 | Max zombies per spawn event |
| `zombieSpawnProb` | 10 | Spawn chance per spawn tick (1-100) |
| `minSpawnDistance` | 25 | Min distance from player for cluster anchor |
| `maxSpawnDistance` | 80 | Max distance from player for cluster anchor |
| `spawnInCreative` | true | Allow cluster spawns for creative players |
| `zombieSpawns` | false | Allow vanilla zombies to remain vanilla (false converts to Cracked Zombies) |
| `daySpawning` | true | Spawn during daytime |
| `spawnIntervalTicks` | 120 | Ticks between cluster-spawn checks |
| `maxNearbyCrackedZombies` | 24 | Nearby Cracked Zombie cap before new spawns are skipped |
| `nearbyZombieCapRange` | 48 | Range used for nearby zombie cap checks |
| `doorBusting` | false | Break down doors |
| `sickness` | true | Apply poison on hit |
| `poisonDuration` | 20 | Poison duration in ticks (1s) |
| `poisonAmplifier` | 0 | Poison level (0 = Poison I) |
| `moveSpeed` | 0.30226672535211263 | Movement speed |
| `aggroRange` | 32.19179137323944 | Detection range in blocks with line of sight |
| `hiddenAggroRange` | 10.0 | Detection range when line of sight is blocked |
| `followRange` | 39.843529929577464 | Chase range in blocks |
| `farNavigationRefreshDistance` | 32 | Distance where path refreshes begin slowing down |
| `farNavigationRefreshInterval` | 40 | Path refresh interval in ticks for far targets |

Config is stored at `<gameDir>/config/crackedzombies.json5`.

## Runtime Architecture

The mod flow is centered on Forge events:

- `CrackedZombiesMod` initializes config and registers the config screen + runtime handler
- `CrackedZombieHandler#onEntityJoinLevel` converts newly spawned zombies based on `zombieSpawns` and `zombieSpawnProb`
- `CrackedZombieHandler#onLevelTick` runs a periodic cluster spawner in the Overworld
- `CrackedZombieHandler#onLivingTick` applies chase/aggro behavior and daytime fire suppression for Cracked Zombies
- `CrackedZombieHandler#onLivingHurt` applies poison effects when Cracked Zombies hit players

Notes:

- Cluster spawns are custom `addFreshEntity` spawns and are not tied to vanilla natural-spawn mob caps
- Cluster placement uses surface + collision checks to avoid invalid placements

## Prerequisites

- macOS (based on development environment)
- Homebrew
- OpenJDK 17
- Minecraft Forge MDK 1.20.1-47.4.10

## Setup Instructions

### 1. Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Install OpenJDK 17

```bash
brew install openjdk@17
echo 'export PATH="/usr/local/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
sudo ln -sfn /usr/local/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

### 3. Build

```bash
./gradlew build
```

Output JAR: `build/libs/crackedzombies-1.0.0.jar` - copy to your mods folder.

## Running

### VS Code Tasks

- **Run Client** (default build task) — launches Minecraft with the mod
- **Build** — compiles the JAR
- **Gen VS Code Runs** — generates launch configurations

### Manual Commands

```bash
./gradlew runClient       # Launch Minecraft client
./gradlew build           # Build the mod JAR
./gradlew genEclipseRuns  # Eclipse run configs
./gradlew genIntellijRuns # IntelliJ run configs
./gradlew :genVSCodeRuns  # VS Code run configs
./gradlew clean           # Clean build outputs
```

## Project Structure

```
src/main/java/com/dividedby0/crackedzombies/
├── CrackedZombiesMod.java       # Mod entry point and registration
├── CrackedZombieHandler.java    # Runtime Forge event handlers (conversion, spawning, AI, poison)
├── SimpleConfigScreen.java      # In-game config UI
├── config/
│   ├── ConfigManager.java       # Config singleton
│   └── JSON5ConfigManager.java  # JSON5 file reader/writer
```

## Troubleshooting

- Java version errors: ensure OpenJDK 17 is on PATH
- Gradle issues: try `./gradlew --no-daemon clean build`
- Ensure Forge 47.4.10 for Minecraft 1.20.1
- If daytime clusters are not appearing, check `daySpawning` is `true`
- If no clusters are spawning, verify `zombieSpawnProb` and `spawnInCreative` settings

## Project Links

- Repository: https://github.com/0dividedby0/CrackedZombie
- Issues: https://github.com/0dividedby0/CrackedZombie/issues

## Release Notes (1.0.0)

- Stable Forge 1.20.1 release.
- Added runtime zombie conversion system.
- Added configurable cluster spawning and distance controls.
- Added daytime spawning toggle.
- Added poison-on-hit behavior.
- Added in-game configuration screen.
- Tuned default cluster spawning to be less overwhelming while still keeping pressure on players.
- Added line-of-sight aware aggro and multiplayer performance controls for nearby caps and distant target navigation.

## License

MIT License — see [LICENSE](LICENSE) for full text.

## Credits

Inspired by **CrackedZombie** by RCKR.
Original mod: https://www.curseforge.com/minecraft/mc-mods/crackedzombie

This mod is an independent, ground-up rewrite for Minecraft Forge 1.20.1 and shares no code or assets with the original.

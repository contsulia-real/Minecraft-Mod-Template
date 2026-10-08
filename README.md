# Minecraft Mod Template

A minimal **Stonecutter + Architectury** multi-version Minecraft mod starter, extracted from the build configuration of Buck. No Buck gameplay code, assets, recipes or mixins are included.

## Targets

| Minecraft | Fabric | Forge | NeoForge | Java |
|---|---|---|---|---|
| 1.20.1 | ✓ | ✓ | — | 17 |
| 1.21.1 | ✓ | — | ✓ | 21 |
| 26.3 | ✓ | — | ✓ | 25 |

All targets require Architectury API. Fabric also requires Fabric API. Loader and dependency versions are in `versions/<minecraft>/gradle.properties`.

## Make a new mod

1. Click **Use this template** on GitHub and choose a repository name.
2. Set `modId`, `modName`, `modVersion`, `modDescription` and `modLicense` in `gradle.properties`.
3. Rename `rootProject.name` in `settings.gradle.kts`.
4. Rename Java package `com.example.examplemod`, `ExampleMod` and the platform entrypoint classes under `src/`, `fabric/`, `forge/` and `neoforge/`. Update `ExampleMod.MOD_ID` and the Fabric `entrypoints.main` class path in `fabric/src/main/resources/fabric.mod.json`.
5. Replace `YourName` in the three loader metadata files. Implement shared logic in `ExampleMod.init()`, and add loader-specific code only when needed.

Keep the Gradle `modId`, common `MOD_ID`, Forge/NeoForge `@Mod` IDs and Fabric metadata ID aligned. No generator or copied Buck content to clean up.

## Build one target, not six by default

On Windows:

```powershell
.\gradlew.bat :fabric:1.21.1:build --no-daemon
.\gradlew.bat :forge:1.20.1:build --no-daemon
.\gradlew.bat :neoforge:26.3:build --no-daemon
```

On Linux/macOS use `./gradlew`.

Available Gradle tasks:

- `:fabric:1.20.1:build`, `:forge:1.20.1:build`
- `:fabric:1.21.1:build`, `:neoforge:1.21.1:build`
- `:fabric:26.3:build`, `:neoforge:26.3:build`

JARs appear under `fabric/versions/<version>/build/libs/`, `forge/versions/<version>/build/libs/` or `neoforge/versions/<version>/build/libs/`. Run `chiseledBuild` **only** to build all six targets.

GitHub Actions builds **Fabric 1.21.1 only** for normal pushes and pull requests. Its manual **Run workflow** action accepts any listed Gradle task (or `chiseledBuild`). Change the workflow's default task when your primary target changes.

## JDKs and reusable Minecraft cache

The repository disables Gradle's **automatic JDK downloads**. Install the JDK major versions you need. Configure installed JDK paths in your *user-level* `~/.gradle/gradle.properties` (not this repository), for example:

```properties
org.gradle.java.installations.auto-download=false
org.gradle.java.installations.paths=C:/path/to/jdk17,C:/path/to/jdk21,C:/path/to/jdk25
```

Gradle shares dependency caches under `~/.gradle/caches` automatically. Loom's mapped Minecraft JARs may still live under each project's `.gradle/loom-cache/minecraftMaven`. To share them on **Windows**, and only if a shared repository already exists, run the following in a fresh project **before building**:

```powershell
New-Item -ItemType Directory -Force .gradle/loom-cache | Out-Null
New-Item -ItemType Junction -Path .gradle/loom-cache/minecraftMaven -Target "$env:USERPROFILE\.gradle\caches\fabric-loom\minecraftMaven"
```

The destination must exist and the local `minecraftMaven` directory must not already exist. The junction is local, ignored by Git and is **not** distributed by GitHub templates. A different Loom version or mappings may still create more cached JARs.

## License

[MIT](LICENSE). Minecraft is the property of Mojang/Microsoft; this is an unofficial community template.

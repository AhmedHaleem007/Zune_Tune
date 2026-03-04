# VCM_ECU

Desktop ECU tuning/editor application built with Kotlin Multiplatform and Compose.

## What This Project Does

`VCM_ECU` provides a desktop UI for loading ECU BIN files, inspecting DTC tables, and generating patched files through common tuning operations.

Current primary workflow supports:
- `MEDG17.9.8` (Kia/Hyundai)

## Tech Stack

- Kotlin `1.9.22`
- Compose Multiplatform plugin `1.6.0`
- Ktor client
- kotlinx serialization
- kotlinx coroutines
- kotlinx datetime
- Gradle multi-module project:
  - `:composeApp` (desktop UI + desktop ECU logic)
  - `:shared` (common processors/network/auth code)

## Project Structure

- `composeApp/src/desktopMain/kotlin/main.kt`: Desktop app entry point.
- `composeApp/src/desktopMain/kotlin/ui/App.kt`: Main UI, file load flow, save/patch pipeline.
- `shared/src/commonMain/kotlin/ecu/Medg17Processor.kt`: MEDG17 DTC extraction.
- `shared/src/commonMain/kotlin/ecu/Medg17DtcOffApplier.kt`: Generic selected-offset DTC off writer.
- `composeApp/src/desktopMain/kotlin/ecu/common_stage1_logic.kt`: Stage map modifier.
- `composeApp/src/desktopMain/kotlin/ecu/ImmoOffLogic_kia_medg.kt`: IMMO patch logic.
- `composeApp/src/desktopMain/kotlin/ecu/pops_kia_medg.kt`: POPS patch logic.

## Build and Run

### Run Desktop App (Dev)

Windows:

```powershell
.\gradlew :composeApp:run
```

macOS/Linux:

```bash
./gradlew :composeApp:run
```

### Compile Desktop Sources

Windows:

```powershell
.\gradlew :composeApp:compileKotlinDesktop
```

macOS/Linux:

```bash
./gradlew :composeApp:compileKotlinDesktop
```

### Full Build

```bash
./gradlew build
```

## Windows Native Packaging

The project is configured for native distribution through Compose/jpackage:

- MSI installer
- EXE installer

Build Windows packages:

```powershell
.\gradlew :composeApp:packageReleaseMsi :composeApp:packageReleaseExe
```

Output is generated under:
- `composeApp/build/compose/binaries`

Note: Windows native packaging requires a Windows environment with required JDK/jpackage tooling.

### Installer Troubleshooting (Windows)

If installer window opens then closes immediately:

1. Run from command line with log:
```powershell
.\Zune Tune-2.0.2.exe /log install.log
```
2. Check `install.log` for MSI code `1638`:
   another version with the same app version is already installed.
   Fix by uninstalling old version first or installing a newer version.
3. Check for MSI `Error 1730` during upgrade:
   installer could not remove an older install without admin rights.
   Fix by uninstalling existing Zune Tune as Administrator, then install again.
4. If app installs but exits on launch, inspect startup log:
   `%LOCALAPPDATA%\Zune Tune\startup.log`
5. If needed, ensure VC++ runtime and Windows Defender are not blocking installation.

## How to Use (MEDG17.9.8 Flow)

1. Start app.
2. In sidebar, select `MEDG17.9.8`.
3. Choose a `.bin` file.
4. Optional:
   - Select DTC rows and click `DTC off`.
   - Open Stage panel and apply stage.
   - Toggle `IMMO OFF`.
   - Toggle `Pops` card.
5. Click `Save File` to write patched output.

Save pipeline for MEDG17.9.8:
- Apply pending DTC-off offsets.
- Apply IMMO patch if IMMO toggle is enabled.
- Apply Pops patch if Pops toggle is enabled.

## Known Limitations

- Pops patching currently relies on defined pattern replacements; tune patterns may need project-specific calibration updates.
- Some Gradle ecosystem deprecation messages can come from toolchain/plugins rather than project code.

## Future Plans

- Add ECU-specific patch profiles and safety checks.
- Add regression test fixtures for bin parsing and patching.

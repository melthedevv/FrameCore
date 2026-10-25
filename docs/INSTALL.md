# Installing FrameCore

FrameCore is an After Effects panel for After Effects 2024, 2025 and 2026 on
Windows and macOS.

The current build is unsigned and for testing. After Effects hides unsigned
panels until a one-time setting is turned on. A signed build, when published,
installs normally without these steps.

## 1. Which CEP version your After Effects uses

| After Effects | CEP | Debug key |
|---|---|---|
| 2024 (24.x) | 11 | `CSXS.11` |
| 2025 (25.x) | 12 | `CSXS.12` |
| 2026 (26.x) | 12 | `CSXS.12` |

If you run more than one version, set both keys. An unknown key does nothing.

## 2. Turn on debug mode

Windows, PowerShell (no admin needed):

```powershell
New-Item -Path "HKCU:\Software\Adobe\CSXS.12" -Force | Out-Null
Set-ItemProperty -Path "HKCU:\Software\Adobe\CSXS.12" -Name PlayerDebugMode -Value "1" -Type String
New-Item -Path "HKCU:\Software\Adobe\CSXS.11" -Force | Out-Null
Set-ItemProperty -Path "HKCU:\Software\Adobe\CSXS.11" -Name PlayerDebugMode -Value "1" -Type String
```

macOS:

```bash
defaults write com.adobe.CSXS.12 PlayerDebugMode 1
defaults write com.adobe.CSXS.11 PlayerDebugMode 1
killall cfprefsd
```

## 3. Put the panel where After Effects looks

Per-user folders (no admin):

- Windows: `%APPDATA%\Adobe\CEP\extensions`
- macOS: `~/Library/Application Support/Adobe/CEP/extensions`

Copy the FrameCore folder into that folder and name it `com.framecore.panel`.
The folder you place there must be the one that contains `CSXS/manifest.xml`.

Windows example, if your copy is at `C:\Path\To\FrameCore`:

```powershell
cmd /c mklink /J "%APPDATA%\Adobe\CEP\extensions\com.framecore.panel" "C:\Path\To\FrameCore"
```

A junction is optional. Copying the folder works too.

## 4. Open the panel

Restart After Effects, then choose `Window > Extensions > FrameCore`.

If FrameCore is not in that menu, the debug key or the folder is wrong. Check
that the folder contains `CSXS/manifest.xml`, then set both debug keys and
restart After Effects.

## 5. Where settings and keys live

FrameCore writes its own file, never the After Effects project:

- Windows: `%APPDATA%\FrameCore\settings.json`
- macOS: `~/Library/Application Support/FrameCore/settings.json`

Keys are in that file. Remove a key in Settings to delete it from disk; **Reset
all settings** clears every key and preference. The file is per-user; there is
no keychain integration.

Provider checks need internet. **Validate** calls the provider and reports
either the number of models the key can use or a sentence saying what to fix. If
Validate says it only works inside After Effects, the panel is not running
inside After Effects.

## If something is wrong

| Symptom | What to do |
|---|---|
| No FrameCore entry in `Window > Extensions` | Set both debug keys, confirm the folder contains `CSXS/manifest.xml`, restart After Effects. |
| The panel opens blank | Reinstall from a complete copy of the folder and restart After Effects. |
| “Could not start FrameCore's host script” | The copy is incomplete. Reinstall the whole folder. |
| Fonts or the logo are wrong | The copy is incomplete. Reinstall the whole folder. |
| A key will not validate | Check the key and the connection, then read the sentence under the field. |
| The panel says it must run in After Effects | Close the browser preview, if any, and open the panel from `Window > Extensions`. |

If a step still fails, note the After Effects version, the operating system and
the exact message.

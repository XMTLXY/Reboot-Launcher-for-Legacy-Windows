# Reboot-Launcher-for-Legacy-Windows

Project Reboot Launcher fixed to run on legacy Windows. Windows 7 is done, and Vista and XP are next.

The current release is **Reboot Launcher 10.0.9 for Windows 7** (with VxKex), for Windows 7 (64-bit, SP1).

- The launcher needs [VxKex](https://github.com/i486/VxKex) to run on Windows 7.
- Fortnite runs normally. It does **not** use VxKex.
- The built-in backend works on Windows 7 without VxKex.

On Windows 10 and 11 you don't need VxKex.

## Download

Get **Reboot Launcher 10.0.9 for Windows 7** (`reboot_launcher-10.0.9-windows-setup.exe`) from the [Releases](../../releases) page.

## Tested game build

**Fortnite 6.31** is tested and working with this release:

- Download: [windows-6.31.rar](https://builds.rebootfn.org/windows-6.31.rar)
- From: [bekka0668/FortniteArchive](https://github.com/bekka0668/FortniteArchive)

## How to use

1. Install [VxKex](https://github.com/i486/VxKex).
2. Run `reboot_launcher-10.0.9-windows-setup.exe`. At the end it tells you whether VxKex was found.
3. Go to the install folder (normally `C:\Program Files\Reboot Launcher`), right-click `reboot_launcher.exe`, then choose **Properties** and the **VxKex** tab.
4. Tick:
   - **Enable VxKex for this program**
   - **Report a different version of Windows**: **Windows 10**
   - **Disable VxKex for child processes**. This one is important: without it, Fortnite crashes.
5. Click **Apply**, then **OK**, and open the launcher. If it was already open, restart it.
6. In the launcher, keep the backend set to **Embedded** (the default).

## Multiplayer

The launcher's online server list doesn't work, so use [Radmin VPN](https://www.radmin-vpn.com/). It works on Windows 7.

1. Everyone installs Radmin VPN and joins the same network.
2. The host starts a game server in Reboot Launcher.
3. Everyone else types the host's Radmin IP (usually starting with `26.`) into the launcher's server IP box.

If someone can't connect, check that Windows Firewall isn't blocking Reboot Launcher or Fortnite.

## Problems

| Problem | Fix |
|---|---|
| "Entry point GetHostNameW could not be located" | VxKex isn't enabled for `reboot_launcher.exe` (step 3). |
| Fortnite crashes in the lobby after about 30 seconds | Tick **Disable VxKex for child processes**, then restart the launcher. |
| Can't log in to Fortnite | Set the backend to **Embedded**. |
| Antivirus blocks the launcher | Add the install folder to your antivirus exclusions. |

## Roadmap

- [x] **Windows 7**: done (Reboot Launcher 10.0.9 for Windows 7)
- [ ] **Windows Vista**: next, trying to get it working
- [ ] **Windows XP**: if Vista works
- [ ] **One launcher for all three**: a single version that runs on Windows 7, Vista and XP, instead of separate ones

## What I changed

Three things are different from the normal 10.0.9.

### 1. `flutter_windows.dll`: launcher crashing on startup

- **Problem:** the newer way this file opens files doesn't work under VxKex, so the launcher couldn't load its own code and crashed instantly.
- **Change:** 5 bytes at file offset `0x728823`, from `4C 8D 44 24 28` to `48 89 3E EB 39`. It now skips the broken step and uses the normal Windows file path.

### 2. `data\app.so`: Fortnite crashing when launched

- **Problem:** the launcher loads its DLLs into Fortnite using the address of a Windows function. Under VxKex, the launcher got VxKex's address instead of the real Windows one, and that address doesn't exist inside Fortnite.
- **Change:** the text `"KERNEL32"` (file offset `0x29F7B0`) became `"KERNEL32.DLL."`. Windows still treats it as the real `kernel32.dll`, but VxKex doesn't recognise it, so the launcher gets the real address.

### 3. Backend: Node.js 20 to Node.js 13

- **Problem:** the built-in backend (`data\flutter_assets\assets\backend\lawinserver.exe`) was LawinServer bundled with Node.js 20, which doesn't run on Windows 7. So Fortnite couldn't log in.
- **Change:** the same LawinServer code now runs on **Node.js 13.14.0**, the last version that supports Windows 7. In the backend folder:
  - `node\node.exe`: official Node.js 13.14.0.
  - `index.js`, `structure\`, `node_modules\`: LawinServer's code, taken out of the old exe unchanged.
  - `lawinserver.exe`: now a small starter that runs LawinServer on Node 13. It also stops Node when the launcher stops the backend. Source in [`tools/lawinserver-shim`](tools/lawinserver-shim).

### Installer

The installer is the original Reboot Launcher installer, with the same pages and options, built from their own script and containing these fixed files. On Windows 7 it adds one page at the end that checks for VxKex and shows the setup steps.

## What's in this repository

| Path | What it is |
|---|---|
| `reboot_launcher.exe`, `*.dll`, `data\` | The patched Reboot Launcher 10.0.9 |
| `installer\` | Installer script (`RebootLauncher-Win7.iss`), `build.cmd` and the Visual C++ runtime |
| `tools\lawinserver-shim\` | Source of the `lawinserver.exe` starter |

The unpatched files aren't included. You can get them from the [original Reboot Launcher](https://github.com/Auties00/reboot_launcher) 10.0.9 release.

## Building the installer

1. Install [Inno Setup 6.2](https://github.com/jrsoftware/issrc/releases/tag/is-6_2_2).
2. Run `installer\build.cmd`.
3. The installer appears in `installer\output\`.

To rebuild the `lawinserver.exe` starter: `%WINDIR%\Microsoft.NET\Framework64\v3.5\csc.exe /nologo /out:lawinserver.exe tools\lawinserver-shim\LawinServerShim.cs`

## Credits

- **Windows 7 port:** vibecoded by [XMTLXY](https://github.com/XMTLXY)
- **AI used:** [Claude Code](https://claude.com/claude-code) (Claude Opus 5.5)
- **Reboot Launcher:** [Auties00](https://github.com/Auties00/reboot_launcher), the original project
- **LawinServer:** the backend bundled with Reboot Launcher
- **Node.js:** [Node.js](https://nodejs.org/) 13.14.0, license in `data\flutter_assets\assets\backend\node\LICENSE`

This is an unofficial fork. Reboot Launcher and its files belong to their original authors.

---

## Original Reboot Launcher README

The original project's README, from [Auties00/reboot_launcher](https://github.com/Auties00/reboot_launcher):

![Banner](https://i.imgur.com/p0P4tcI.png)

![GitHub Downloads (all assets, all releases)](https://img.shields.io/github/downloads/Auties00/Reboot-Launcher/total)

GUI and CLI Launcher for [Project Reboot](https://github.com/Milxnor/Project-Reboot-3.0/)

Join our [Discord](https://discord.gg/rebootmp)

Install the launcher easily from the [releases](https://github.com/Auties00/Reboot-Launcher/releases/) section

### Modules

- COMMON: Shared business logic for CLI and GUI modules
  
- CLI: Work in progress command line interface to host a Fortnite Server on a Windows VPS easily, developed in Dart
  
- GUI: Stable graphical user interface to play and host Fortnite S0-14

### Preview 

- GUI
  
![Registrazione 2025-03-24 194421](https://github.com/user-attachments/assets/f3452969-76ba-49e7-b707-42754bacad70)

- CLI
  
Coming soon!

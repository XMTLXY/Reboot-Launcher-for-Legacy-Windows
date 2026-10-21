# Reboot-Launcher-for-Legacy-Windows

Project Reboot Launcher fixed to run on legacy Windows. Windows 7 and Vista are done, and XP is next.

The current release is **Reboot Launcher 10.0.9 for Windows 7 and Vista**, for **Windows 7 (64-bit, SP1)** and **Windows Vista (64-bit, SP2)**.

- **Runs natively. No VxKex needed.** A small extra file, `w7compat.dll`, provides the newer Windows functions the launcher uses.
- The built-in backend runs on **Node.js 22**, using a build modified to run on Windows 7 from [W7Revived](https://w7revived.chefkiss.dev/). It also runs on Vista with the Extended Kernel.
- **On Vista, the [Windows Vista Extended Kernel](https://win32subsystem.live/extended-kernel/) is needed** for Fortnite and for the Node.js 22 backend. The launcher itself runs without it.

## Roadmap

- [x] **Windows 7**: done. The first release (Reboot Launcher 10.0.9 for Windows 7) needed VxKex. The current release (Reboot Launcher 10.0.9 for Windows 7 and Vista) runs natively, with no VxKex needed.
- [x] **Windows Vista**: done, in the current release (Reboot Launcher 10.0.9 for Windows 7 and Vista). The launcher runs natively; Fortnite needs the Extended Kernel.
- [ ] **Hosting on Windows 7 and Vista**: likely needs a fixed `reboot.dll`
- [ ] **Windows XP**: next
- [ ] **One launcher for all three**: a single version that runs on Windows 7, Vista and XP, instead of separate ones

## Download

Get **Reboot Launcher 10.0.9 for Windows 7 and Vista** (`reboot_launcher-10.0.9-windows-setup.exe`) from the [Releases](../../releases) page.

## Tested

| Windows | Launcher | Backend | Fortnite |
|---|---|---|---|
| Windows 7 SP1 x64 | Works | Works | 6.10.2: loads into a match |
| Windows Vista SP2 x64 + Extended Kernel | Works | Works | 1.8: tested up to the lobby |

Hosting a game server doesn't work yet on Windows 7 or Vista. See [Known problems](#known-problems).

## Setup

### Windows 7

1. Run `reboot_launcher-10.0.9-windows-setup.exe` and follow the installer. It installs to `C:\Program Files\Reboot Launcher` by default and installs the Visual C++ runtime the launcher needs. Install to a local drive: from a slow or network drive the backend can start too slowly for the launcher.
2. Open Reboot Launcher.
3. In the launcher, keep the backend set to **Embedded** (the default).

You don't need to change your Fortnite builds on Windows 7. If a build comes with **DXVK** files, you can leave them there.

If you used the VxKex version before, VxKex isn't needed any more. Turn it off for the launcher: right-click `reboot_launcher.exe` in the install folder, choose **Properties**, open the **VxKex** tab and untick **Enable VxKex for this program**.

> **Don't update the launcher.** If Reboot Launcher offers an update, decline it, and don't install the official version over this one. Updates replace the fixed files and will break the launcher on Windows 7 and Vista. Wait for a new release here instead.

### Windows Vista

Do these steps in order, then the [Windows 7](#windows-7) steps above. Tested with Fortnite 1.8. On Vista, the installer shows at the end which of these are installed and which are missing.

#### 1. Install the Windows Vista Extended Kernel

Install the [Windows Vista Extended Kernel](https://win32subsystem.live/extended-kernel/) and follow its own instructions. It adds newer Windows functions to Vista, which Fortnite and the Node.js 22 backend need.

#### 2. Install the four Platform Update parts

The Platform Update for Vista (KB971644) can't be downloaded as one file any more. Its four parts are still on the [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/). For each one, search the KB number, pick the **x64** entry ("Update for Windows Vista for x64-based Systems"), download it and install it:

| Update | What it adds |
|---|---|
| [KB971512](https://www.catalog.update.microsoft.com/Search.aspx?q=KB971512) | DirectX 11 (Fortnite needs this one) |
| [KB971513](https://www.catalog.update.microsoft.com/Search.aspx?q=KB971513) | Windows Automation API |
| [KB971514](https://www.catalog.update.microsoft.com/Search.aspx?q=KB971514) | Windows Ribbon and Animation Manager |
| [KB960362](https://www.catalog.update.microsoft.com/Search.aspx?q=KB960362) | Windows Imaging Component |

The catalog site doesn't work in Vista's own browser (Internet Explorer). Download the updates with a newer browser that still runs on Vista, such as [Supermium](https://www.supermium.net/) (a Chrome-based browser for older Windows), or on another PC and copy the files over. Restart Vista after installing.

#### 3. Install the DirectX End-User Runtime (June 2010)

Install `directx_Jun2010_redist.exe` from Microsoft. Fortnite needs `XINPUT1_3.dll` and other DirectX parts from it. Without it you get **"XINPUT1_3.dll was not found"**.

#### 4. Remove DXVK from the game folder (Vista only)

Some Fortnite builds come with **DXVK**, which replaces DirectX with Vulkan. Vista has no Vulkan, so the game fails with **"vulkan-1.dll was not found"**. This step is only for Vista: on Windows 7, leave DXVK where it is.

Go to `FortniteGame\Binaries\Win64` in your Fortnite build. If these files are there, move them to another folder (keep them in case you use the build on Windows 7 or newer):

- `d3d9.dll`
- `d3d10.dll`
- `d3d10_1.dll`
- `d3d10core.dll`
- `d3d11.dll`
- `dxgi.dll`

Fortnite then uses Vista's own DirectX 11 from KB971512.

### Limits on Windows 7 and Vista

- **No pop-up notifications.** The launcher's Windows notifications ("toasts") need Windows 8 or newer. Everything else works without them.
- **Solid window background.** The launcher's see-through background (Mica) only exists on Windows 11. On Windows 7 and Vista, as on Windows 10, the background is a solid colour. This is normal.

## Known problems

- **Hosting a game server doesn't work on Windows 7 or Vista.** The server crashes when a player joins. This is suspected to be a bug in Project Reboot's `reboot.dll`, not in this port.

## Multiplayer

The launcher's online server list doesn't work, so use [Radmin VPN](https://www.radmin-vpn.com/). It works on Windows 7.

1. Everyone installs Radmin VPN and joins the same network.
2. The host starts a game server in Reboot Launcher.
3. Everyone else types the host's Radmin IP (usually starting with `26.`) into the launcher's server IP box.

If someone can't connect, check that Windows Firewall isn't blocking Reboot Launcher or Fortnite.

Hosting doesn't work on Windows 7 or Vista yet (see [Known problems](#known-problems)), so for now the host needs Windows 8 or newer. Players on Windows 7 can join.

**On Vista:** it's not known yet whether Radmin VPN works on Vista, and multiplayer on Vista hasn't been tested. There's likely a way (for example another VPN that still supports Vista), but it hasn't been tried.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Entry point ... could not be located" when opening the launcher | `w7compat.dll` is missing from the install folder. Run the installer again. |
| "XINPUT1_3.dll was not found" when launching Fortnite | Install the DirectX End-User Runtime (June 2010). See [Windows Vista](#windows-vista). |
| "vulkan-1.dll was not found" when launching Fortnite on Vista | Remove the DXVK files from the game folder (Vista only). See [Windows Vista](#windows-vista). |
| "d3d11.dll was not found" when launching Fortnite on Vista | Install KB971512. See [Windows Vista](#windows-vista). |
| An "entry point" error from Fortnite or the backend on Vista | Install the Windows Vista Extended Kernel. See [Windows Vista](#windows-vista). |
| Antivirus blocks the launcher | Add the install folder (normally `C:\Program Files\Reboot Launcher`) to your antivirus exclusions. |

## How it works

The launcher is a Flutter app built for Windows 10 and 11. It asks Windows for a handful of functions that only exist on newer versions, so Windows 7 and Vista refused to start it.

- **`w7compat.dll`**: a small DLL written for this port that provides those missing functions (file paths, networking, secure random numbers for HTTPS, and on Vista touch input, a thread lock and a few others). Wherever Windows already has the real function, it uses that, so nothing changes on Windows 8, 10 and 11.
- **Patched launcher files**: `flutter_windows.dll` and one plugin now point their missing functions at `w7compat.dll` instead of Windows.
- **Backend on Node.js 22**: the built-in backend originally used Node.js 20, which doesn't run on Windows 7. The first release for Windows 7 (the version that needed VxKex) used Node.js 13. The latest release, which runs natively on Windows 7 and Vista, uses [W7Revived](https://w7revived.chefkiss.dev/)'s Windows 7 build of Node.js 22, which also runs on Vista with the Extended Kernel.
- **Fortnite on Vista**: the game's own files aren't modified. Vista gets what Fortnite needs instead: the Extended Kernel, DirectX 11 (KB971512, one of the four Platform Update parts, each downloaded on its own), and the DirectX June 2010 runtime. Builds that come with DXVK files need those moved out, because Vista can't run DXVK.

## Credits

- **Windows 7 and Vista port:** vibecoded by [XMTLXY](https://github.com/XMTLXY)
- **AI used:** [Claude Code](https://claude.com/claude-code) (Claude Opus 5.5)
- **Reboot Launcher:** [Auties00](https://github.com/Auties00/reboot_launcher), the original project
- **Node.js:** [Node.js](https://nodejs.org/) 22.13.1, license in `data\flutter_assets\assets\backend\node\LICENSE`
- **W7Revived:** [W7Revived](https://w7revived.chefkiss.dev/), for the Windows 7 build of Node.js 22 (`node-v22.13.1-win-x64.7z`)
- **Tiny C Compiler:** used to build `w7compat.dll`

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

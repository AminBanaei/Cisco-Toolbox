# Cisco Toolbox

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Windows-0078D6)](#requirements)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue)](#requirements)

**A PuTTY-style Windows desktop app for configuring Cisco switches and routers — without memorizing a single command.**

Connect over **SSH** or **Serial (COM port)**, then configure your device by clicking buttons instead of typing raw IOS commands from memory. Every button sends a real command over a real, live session — nothing is simulated.

*by Amin Banaei — [a.banaeei@gmail.com](mailto:a.banaeei@gmail.com)*

---

## Table of contents

- [Why this exists](#why-this-exists)
- [Features](#features)
- [Tabs at a glance](#tabs-at-a-glance)
- [Screenshots](#screenshots)
- [Requirements](#requirements)
- [Getting started (development mode)](#getting-started-development-mode)
- [Building a standalone .exe](#building-a-standalone-exe)
- [Making a proper Setup.exe installer](#making-a-proper-setupexe-installer)
- [Project structure](#project-structure)
- [Testing](#testing)
- [A note on safety](#a-note-on-safety)
- [Contributing](#contributing)
- [Contact / Support](#contact--support)
- [License](#license)
- [Credits](#credits)

## Why this exists

Built while setting up a Catalyst 2960-X switch, out of wanting a faster way to get common configuration tasks done without re-typing (or re-Googling) the same IOS commands every time. See the in-app **About** dialog for the full story.

## Features

### Connectivity
- **SSH** (via [PuTTY](https://www.putty.org/)'s `plink.exe`, run as a subprocess) and **Serial/COM port** (via `pyserial`), in one interface. SSH login (host-key confirmation, username, password) is handled interactively by `plink` itself, exactly like typing into a real PuTTY window.
- Live terminal input/output, just like PuTTY — type directly, or click buttons.
- **Enter Switch** button to safely wake a console session that's stuck on boot/log messages.

### 23 configuration and utility tabs
Every tab is built around real Cisco IOS syntax, ordered beginner → advanced: day-0 basics, then L2 foundations, L2 security & protocols, remote access & AAA, discovery & monitoring, operational utilities, and finally personal-productivity tools. See the full [tab-by-tab reference](#tabs-at-a-glance) below.

A **Useful Commands** bar (Enable, Configure Terminal, End, Yes, No, Exit, Write) stays visible above every tab. Every row is either a **fixed command** (one click, one command) or a **variable command** (fill in a field — hostname, VLAN ID, IP, etc. — then Apply). Each tab also has a **❓** badge with a bilingual (English/Persian) tooltip explaining what it's for.

### Input validation
IP/subnet/gateway addresses, RADIUS/TACACS/NTP/syslog hosts, MAC addresses, interface ids, and hostnames are validated as you type. A malformed value is flagged with a red border and the Apply/Pin button refuses to send it — so a typo can't reach a live device's CLI.

### Pin Commands
- Pin any command from anywhere in the app with one click (its own always-visible shortcut button sits in the header) — build your own personal shortlist.
- Add custom commands directly, remove what you don't need.
- Export/import your pinned list as a file; it also persists automatically across restarts.

### Switch Models
- A **📋 Switch Models** button sits right next to Pin Commands in the header.
- One click opens a bundled Cisco switch models/specs/lifecycle spreadsheet in whatever program is associated with `.xlsx` on your machine (normally Excel) — just the file, nothing else.

### Import / Export
- Export a device's running-config or startup-config straight to a local file (handles pagination automatically).
- Import a saved config file: pastes it into the device line-by-line in global config mode, automatically skipping comments and `show run` boilerplate. A fresh safety backup of the device's *current* config is taken first.

### Backup / Diff, safer by default
- One-click, timestamped snapshots of the running-config, saved automatically — no save dialog.
- **WRITE MEMORY** and **COPY RUNNING-CONFIG STARTUP-CONFIG** (in Save & Verify) automatically take a backup right before committing — no extra click needed.
- A unified diff between any two saved backups, so you can see exactly what changed before/after a maintenance window.
- One-click **Restore** on any saved backup pushes it back onto the device — after first taking a fresh safety backup of the device's current state, so the restore itself can be undone.

### Black Box — encrypted credential notebook
- A private, password-gated place to save each switch's hostname, username, secret, and enable secret (with an optional description).
- First use asks you to set a username/password; every entry is encrypted on disk with a key derived from that password (nothing is ever stored in plain text).
- Export/import your saved accounts as a separately password-protected file.

### Macros
- Save your own multi-command sequences and replay them in one click.
- Saved macros persist across restarts.

### Everything else
- **Cross-tab search** — one search box finds matching commands across *every* tab at once, grouped by section, not just the tab you happen to have open.
- Adjustable command-output text size.
- A proper About dialog and a startup splash screen with the app icon and version.
- Roboto (Google Fonts) used throughout the UI — the command output/terminal keep a monospace font on purpose, so CLI output columns still line up.

## Tabs at a glance

The exact order and wording below matches the in-app **❓** tooltips, tab by tab, left to right:

| # | Tab | What it does |
|---|---|---|
| 1 | **Basic** | Core device setup: hostname, banner, enable secret, and other core switch configuration commands. |
| 2 | **VLAN** | Create, name, and manage VLANs on the switch. |
| 3 | **Interface** | Configure switch interfaces: mode, description, speed/duplex, and VLAN assignment. |
| 4 | **Trunk** | Configure trunk ports and the VLANs allowed between switches. |
| 5 | **EtherChannel / LACP** | Bundle multiple physical ports into one logical link with EtherChannel (LACP/PAgP). |
| 6 | **Spanning Tree** | Configure Spanning Tree mode, priority, PortFast, and BPDU guard. |
| 7 | **Port Security** | Configure port security: max MAC addresses, violation action, and sticky MAC. |
| 8 | **DHCP Snooping** | Configure DHCP snooping to protect the network against rogue DHCP servers. |
| 9 | **ACL** | Create and apply standard/extended access control lists. |
| 10 | **Line VTY / Access** | Configure console/VTY lines and remote access restrictions (Telnet/SSH access-class). |
| 11 | **SSH** | Enable and configure SSH access: domain name, RSA key generation, and SSH version. |
| 12 | **AAA / RADIUS / TACACS+** | Centralized authentication: AAA method lists plus RADIUS/TACACS+ server configuration. |
| 13 | **CDP / LLDP** | View and configure neighbor discovery: CDP (Cisco) and LLDP (standard) — useful for troubleshooting. |
| 14 | **NTP / Syslog** | Configure NTP time synchronization and syslog logging destinations. |
| 15 | **Management** | Configure the management VLAN/IP, SNMP, and other device management settings. |
| 16 | **Save / Verify** | Save the running configuration and verify current device settings — WRITE MEMORY / COPY RUNNING-CONFIG STARTUP-CONFIG here auto-backup first. |
| 17 | **Import / Export** | Import or export configuration files and app settings. |
| 18 | **Backup / Diff** | Take timestamped configuration backups, compare (diff) them, and restore any of them back to the device. |
| 19 | **Reset** | Factory reset or erase the switch's configuration — behind an explicit warning. |
| 20 | **ROMMON** | ROMMON (boot-level) recovery commands for password recovery and boot issues. |
| 21 | **🗄 Black Box** | Encrypted credential notebook to securely store device usernames/passwords. |
| 22 | **📌 Pin Commands** | Save your frequently used commands as pins for one-click access. |
| 23 | **▶ Macros** | Create and run multi-command sequences (macros) with one click. |

Two more things always live in the header, outside the tab strip: the **📌 Pin Commands** shortcut button (jumps straight there) and the **📋 Switch Models** button (opens the bundled specs spreadsheet).

## Screenshots

_Add a few screenshots or a short GIF here once you publish the repo — the connection panel, a configuration tab, and the Black Box vault are good ones to show._

## Requirements

- **Windows** (the app is built and packaged for Windows; SSH depends on PuTTY's `plink.exe`).
- **Python 3.10+** for development mode — get it from [python.org](https://www.python.org/) (check "Add python.exe to PATH" during install). Not required to run a pre-built `.exe`.
- **[PuTTY](https://www.putty.org/)** (free) for SSH — installs `plink.exe`, which the app looks for on `PATH`, in PuTTY's default install locations, and right next to the app itself. Serial doesn't need this.

## Getting started (development mode)

```bat
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python main.py
```

## Building a standalone .exe

No Python installation is required on the machine you hand the .exe to — everything is bundled. There are a few build options depending on what you care about most:

| Script | What you get | Best for |
|---|---|---|
| `setup_and_build.bat` / `build_exe.bat` | A single `.exe` file (`--onefile`) | Simplicity — one file to share |
| `setup_and_build_onedir.bat` / `build_exe_onedir.bat` | A folder (`--onedir`), with the support files hidden and locked from casual browsing | Much faster startup than onefile |
| `build_exe_onedir_compiled.bat` | A folder, but compiled to native machine code with [Nuitka](https://nuitka.net/) instead of packaged Python bytecode | Strongest protection against casual tampering; fully offline after Nuitka's one-time C-compiler setup |

The `setup_and_build*` scripts create a virtual environment and install everything from scratch; the `build_exe*` scripts assume you already have one activated. Each script prints its own next steps and caveats when it finishes — read the on-screen notes, especially for the Nuitka build.

Every build script bundles the whole `assets/` folder as-is, so the app icon, logo, splash screen, fonts, and the Switch Models spreadsheet are all included automatically — no extra flags needed when you add or change files under `assets/`.

## Making a proper Setup.exe installer

For a real download-and-install experience (Program Files, Start Menu shortcut, optional Desktop shortcut, and an uninstaller listed in "Add or remove programs"), build the app with an onedir script first, then run `build_installer.bat`. It uses [Inno Setup](https://jrsoftware.org/isdl.php) (free — install it once) to turn `dist\CiscoToolbox\` into a single `installer_output\CiscoToolboxSetup.exe` — that one file is what you upload for people to download.

## Project structure

```
CiscoToolbox/
  main.py                    -> entry point, splash screen, font/icon setup
  core/
    connection_manager.py    -> SSH (plink.exe subprocess) / Serial (pyserial) session handling
    blackbox_crypto.py       -> key derivation + stream cipher for the Black Box vault
    validators.py            -> IP/MAC/interface/hostname format checks (Qt-free, unit-tested)
    config_utils.py          -> config-text cleaning + diffing (Qt-free, unit-tested)
  ui/
    main_window.py           -> main window: header, search, connection panel, command box, status bar
    widgets.py                -> reusable row types (fixed / variable commands), validated input fields, shared styles
    about_dialog.py            -> About dialog
    splash_screen.py           -> startup splash screen
    config_states_bar.py       -> the always-visible Useful Commands bar
    tabs/
      basic_tab.py, vlan_tab.py, interface_tab.py, trunk_tab.py, etherchannel_tab.py,
      stp_tab.py, port_security_tab.py, dhcp_snooping_tab.py, acl_tab.py,
      line_vty_tab.py, ssh_tab.py, aaa_tab.py, cdp_lldp_tab.py, ntp_syslog_tab.py,
      management_tab.py, save_verify_tab.py, import_export_tab.py, backup_diff_tab.py,
      reset_tab.py, rommon_tab.py                -> one tab per configuration area, in on-screen order
      blackbox_tab.py               -> Black Box
      pin_tab.py                    -> Pin Commands
      macro_tab.py                  -> Macros
  tests/                      -> pytest unit tests for the Qt-free logic in core/
  assets/
    cisco_logo.svg, cisco_logo.ico, splash_screen.png, switch_models.xlsx, fonts/
                                -> app icon, logo, splash image, the Switch Models spreadsheet, and bundled Roboto/Vazirmatn fonts
```

## Testing

Every command tab was built to match real Cisco IOS syntax, but please test against a lab device before relying on this for production changes — like any tool that touches live network config, mistakes are easy to make quickly.

A small automated test suite covers the Qt-free logic that everything else builds on: input validation (`core/validators.py`), config-text cleaning/diffing (`core/config_utils.py`), and the Black Box crypto primitives (`core/blackbox_crypto.py`).

```bat
pip install -r requirements-dev.txt
pytest
```

## A note on safety

This app sends real commands to real devices over a real live session. A few things it does to make that safer:

- Input fields for IPs, MAC addresses, interface ids, and hostnames are validated before anything is sent.
- WRITE MEMORY, COPY RUNNING-CONFIG STARTUP-CONFIG, Import Config, and Restore Backup all take an automatic safety snapshot of the running-config immediately before they touch the device.
- Destructive actions (Factory Reset) are behind an explicit warning.

None of that is a substitute for testing on a lab device first and knowing what a command does before you click Apply.

## Contributing

Found a bug, or have an idea for a feature? Contributions are welcome:

1. Open an issue describing the bug or feature request.
2. For code changes, fork the repo, make your change, run the test suite (`pytest`), and open a pull request.
3. Keep new logic that doesn't need Qt (validation, parsing, crypto, etc.) in `core/` so it stays easy to unit-test — see `tests/` for the existing pattern.
4. If you add a new tab, register it with `_add_tab(...)` in `ui/main_window.py` and add its bilingual entry to the `TAB_HELP` dict so the in-app ❓ tooltip (and this README's [tab table](#tabs-at-a-glance)) stay accurate.

## Contact / Support

Questions, bug reports, or feature ideas — reach out directly:

📧 **Email:** [a.banaeei@gmail.com](mailto:a.banaeei@gmail.com)

You can also open an issue on this repository, or use the **Email Me** button in the app's About dialog.

## License

MIT — see [LICENSE](LICENSE).

## Credits

Thanks to Eng. Aghaei, Eng. Mohebbi, Eng. Mehdi Banaei, Eng. Mohseni, Eng. Valukian, Eng. Abdollahpour, Eng. Sharifi, and Eng. Milad Mohammadi for their feedback, encouragement, and support throughout the process of building this app.

---

نسخه‌ی فارسی این راهنما: [README.fa.md](README.fa.md)

![Fuxion logo](https://github.com/FluxionNetwork/fluxion/raw/master/logos/logo.jpg)

# Fluxion is the future of MITM WPA attacks

Fluxion is a security auditing and social-engineering research tool. It is a remake of linset by vk496 with (hopefully) fewer bugs and more functionality. The script attempts to retrieve the WPA/WPA2 key from a target access point by means of a social engineering (phishing) attack. It's compatible with the latest release of Kali (rolling). Fluxion's attacks' setup is mostly manual, but experimental auto-mode handles some of the attacks' setup parameters. Read the [FAQ](https://github.com/FluxionNetwork/fluxion/wiki/FAQ) before requesting issues.

If you need quick help, you can talk with us on [Discord](https://discord.gg/G43gptk).

---

## 🛠️ Bug Fixes & Improvements by [Ajay Kandhare](https://github.com/Ajay120503)

> **Maintainer:** Ajay Kandhare
> **GitHub:** [@Ajay120503](https://github.com/Ajay120503)
> **Email:** [ajaykandhare12@gmail.com](mailto:ajaykandhare12@gmail.com)

The following bugs were identified and fixed in this fork:

### `lib/InterfaceUtils.sh`
- **Critical Fix:** `default)` → `*)`  in `interface_hardware()` and `interface_chipset()` case statements.
  In bash, `default` is a literal string match, **not** a catch-all — only `*` works. Unknown hardware bus types silently fell through leaving chip/bus variables unset.
- **Fix:** Re-enabled the double-source guard (`InterfaceUtilsVersion`) that was accidentally commented out, preventing the file from being loaded multiple times.

### `fluxion.sh`
- **Critical Fix:** `options+=` → `choices+=` in `fluxion_hash_verify()`. The Back button was appended to an undefined array `options` instead of the declared `choices` array — Back navigation was completely broken in the hash verification menu.
- **Critical Fix:** Language file check used `find ... | while read` (pipe/subshell), so `return 1` inside only exited the subshell and the English fallback never triggered. Switched to process substitution `< <(find ...)` with a `_langMissing` flag.
- **Fix:** Replaced `more $FLUXIONPreferencesFile | grep -q` — `more` is an interactive pager whose exit code is lost through a pipe. Now uses `grep -q "..." "$file"` directly.
- **Fix:** `let counter++` returns exit code 1 when counter was 0, breaking the do-sequence loop on the first iteration. Changed to `(( counter++ )) || true`.
- **Fix:** `kill -s SIGKILL $authService` — `$authService` is not guaranteed to be set, causing fatal errors. Guarded with `[ -n "${authService:-}" ]`.

### `lib/WindowUtils.sh`
- **Fix:** `fluxion_window_close()` in tmux mode only killed the PID but left orphaned tmux windows open. Now also finds and kills the matching tmux window by pane PID.

### ✨ New Features Added
- **6GHz / Wi-Fi 6E band support** — If the adapter supports 6GHz, scan options for "6GHz (Wi-Fi 6E)" and "All Bands" now appear in the interactive menu.
- **6GHz channel labeling** — Channels ≥ 178 are marked with a `₆` suffix in the target list (similar to `!` for DFS channels).
- **Correct 6GHz band inference** — When scanning a specific channel, channels ≥ 178 now correctly map to band `ax` instead of being misidentified as 5GHz.

---

## Installation

Read [here](https://github.com/FluxionNetwork/fluxion/wiki/Generate-ssh-keys) before you do the following steps.

**Download the latest revision**
```bash
git clone git@github.com:FluxionNetwork/fluxion.git

# Or if you prefer https

git clone https://www.github.com/FluxionNetwork/fluxion.git
```

**Switch to tool's directory**
```bash
cd fluxion
```

**Run fluxion (it will check dependencies and prompt to install any that are missing)**
```bash
./fluxion.sh
```

**To install/check dependencies only without running attacks**
```bash
./fluxion.sh -i
```

**Fluxion is also available in Arch Linux**
```bash
cd bin/arch
makepkg
```

Or using the BlackArch repo:
```bash
pacman -S fluxion
```

---

## :scroll: Changelog

Fluxion is actively maintained with new features, improvements, and bugfixes.
Be sure to check out the [changelog here](https://github.com/FluxionNetwork/fluxion/commits/master).

---

## ![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png "Octocat") How to Contribute

All contributions are welcome! Code, documentation, graphics, or even design suggestions are welcome; use GitHub to its fullest. Submit pull requests, contribute tutorials or other wiki content — whatever you have to offer, it'll be appreciated, but please follow the [style guide](https://github.com/FluxionNetwork/fluxion/wiki/Code-style-guide).

---

## :book: How It Works

* Scan for a target wireless network.
* Launch the `Handshake Snooper` attack.
* Capture a handshake (necessary for password verification).
* Launch `Captive Portal` attack.
* Spawns a rogue (fake) AP, imitating the original access point.
* Spawns a DNS server, redirecting all requests to the attacker's host running the captive portal.
* Spawns a web server, serving the captive portal which prompts users for their WPA/WPA2 key.
* Spawns a jammer, deauthenticating all clients from original AP and luring them to the rogue AP.
* All authentication attempts at the captive portal are checked against the handshake file captured earlier.
* The attack will automatically terminate once a correct key has been submitted.
* The key will be logged and clients will be allowed to reconnect to the target access point.

For a guide to the `Captive Portal` attack, read the [Captive Portal attack guide](https://github.com/FluxionNetwork/fluxion/wiki/Captive-Portal-Attack).

---

## :heavy_exclamation_mark: Requirements

A Linux-based operating system. We recommend **Kali Linux 2025.4**. An external WiFi card is recommended.

---

## ![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png "Octocat") Credits

| # | Contributor | Role |
|---|-------------|------|
| 1 | **[Ajay Kandhare](https://github.com/Ajay120503)** | **Bug Fixes & Feature Updates** |
| 2 | l3op | Contributor |
| 3 | dlinkproto | Contributor |
| 4 | vk496 | Developer of linset |
| 5 | Derv82 | @Wifite/2 |
| 6 | Princeofguilty | @webpages and @bruteforce |
| 7 | Ons Ali | @wallpaper |
| 8 | PappleTec | @sites |
| 9 | MPX4132 | Fluxion V3 |
| 10 | usama7628674 | Contributor |
| 11 | cjb900 | Moderator |

---

## Disclaimer

* Authors do not own the logos under the `/attacks/Captive Portal/sites/` directory. Copyright Disclaimer Under Section 107 of the Copyright Act 1976, allowance is made for "fair use" for purposes such as criticism, comment, news reporting, teaching, scholarship, and research.

* The usage of Fluxion for attacking infrastructures without prior mutual consent could be considered an illegal activity and is **highly discouraged** by its authors/developers. It is the end user's responsibility to obey all applicable local, state, and federal laws. Authors assume no liability and are not responsible for any misuse or damage caused by this program.

---

## ⚠️ Note

* Beware of sites pretending to be related with the Fluxion Project. These may be delivering malware.
* For WN722n V2/V3 visit — https://github.com/aircrack-ng/rtl8188eus
* Fluxion **DOES NOT WORK** on Windows Subsystem for Linux (WSL/WSL2), because the subsystem doesn't allow access to wireless network interfaces. Any issues regarding WSL will be **Closed Immediately**.

---

## Links

| Resource | URL |
|----------|-----|
| 🌐 Fluxion Website | https://fluxionnetwork.github.io/fluxion/ |
| 💬 Discord | https://discord.gg/G43gptk |
| 🐛 Bug Fixes by | [Ajay Kandhare](https://github.com/Ajay120503) |
| 📧 Contact | [ajaykandhare12@gmail.com](mailto:ajaykandhare12@gmail.com) |

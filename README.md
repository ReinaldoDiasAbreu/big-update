# 🚀 BigUpdate Stack

<p align="right">
  🇧🇷 <a href="./README.pt-BR.md">Português Brasileiro</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/OS-BigLinux%20%7C%20Arch%20%7C%20Manjaro-blue?style=for-the-badge&logo=linux" alt="Supported OS">
  <img src="https://img.shields.io/badge/Shell-Bash%205+-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white" alt="Bash">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">
</p>

<p align="center">
  <img src="./assets/demo.gif" alt="BigUpdate Demo">
</p>

BigUpdate Stack is a Bash automation script developed for **BigLinux**, **Manjaro**, and Arch Linux-based systems using **BTRFS + Timeshift**.

It automates the entire system maintenance workflow:

* official package updates
* AUR package updates
* Flatpak updates
* orphan package cleanup
* snapshot verification
* execution logging

---

## 💡 Why does this project exist?

I've always enjoyed updating my systems directly from the terminal, especially in the Arch/BigLinux ecosystem. However, before every system upgrade, I found myself manually repeating almost the same maintenance ritual:

* checking for available updates
* verifying Timeshift snapshots
* searching for orphan packages and removing them
* updating official repositories
* updating AUR packages
* updating Flatpaks
* cleaning unused dependencies

After repeating this process for a long time, I decided to automate everything into a single command.

The real reason?

Laziness.

So I gradually improved this script until it became mature enough to share publicly.

---

## ✨ Main Features

### 🔒 Strict Safety (`Strict Mode`)

Uses:

```bash
set -euo pipefail
```

to immediately stop execution if any intermediate command fails, avoiding partial upgrades or inconsistent system states.

---

### 📝 Smart Logging

Automatically creates an execution log at:

```bash
~/bigupdate.log
```

Recording:

* pending updates
* updated packages
* execution timestamps
* complete system output

Useful for troubleshooting if something fails or closes unexpectedly.

---

### 📸 Timeshift + BTRFS Integration

Since BigLinux uses the BTRFS filesystem by default together with Timeshift snapshots, the script automatically checks for snapshots created on the current day before updating the system and optionally creates a preventive snapshot.

This allows BigLinux users to revert all system modifications by selecting a previous snapshot from the boot menu.

---

### 🧹 Orphan Package Management

Detects and safely offers removal of orphan dependencies using:

```bash
pacman -Qtdq
```

Over time, I encountered several issues caused by orphan packages without maintenance or support, especially in the Arch/Manjaro ecosystem.

Since these packages are usually no longer needed, I started removing them before updates to reduce the chance of conflicts, broken dependencies, or upgrade failures.

For this reason, the script performs orphan verification before the system update and offers safe cleanup of detected orphan packages.

Personally, I prefer not to remove orphan packages immediately after updates because some recently updated software may still indirectly depend on them. Since keeping them temporarily installed usually causes no issues, they remain in the system until the next execution of the script.

---

### 🔄 Complete Ecosystem Update

Updates:

* pacman packages
* yay/AUR packages
* Flatpaks

---

### 🧼 Flatpak Auto Cleanup

Automatically removes unused Flatpak runtimes and dependencies after updates.

---

### 🔔 Desktop Notifications

Sends desktop notifications via `notify-send` when updates are installed, recommending a system reboot.

---

## 🛠️ System Requirements

The script automatically checks for required dependencies.

Make sure you have installed:

* `yay`
* `flatpak`
* `timeshift`
* `libnotify`

Recommended installation:

```bash
sudo pacman -S flatpak timeshift libnotify
```

---

## 📦 Installation

### 1. Clone the repository and enter the project directory

```bash
git clone https://github.com/ReinaldoDiasAbreu/big-update.git
cd big-update
```

---

### 2. Install

```bash
makepkg -si
```

---

### 3. Run

After installation is complete, you can launch the project from anywhere in the terminal by running:

```bash
big-update
```

> [!NOTE]
> After installation finishes, you can safely remove the cloned `big-update` directory. However, keeping it is recommended. This way, whenever new updates are released, you only need to enter the same directory, run `git pull` to download the latest changes, and execute `makepkg -si` again to update the package.

---

## 🔧 Optionally Updating Mirrors

The script includes optional support for automatically updating the mirror list using:

```bash
./big-update --upmirrors
```

This option executes:

```bash
sudo pacman-mirrors -f
```

before synchronizing repositories.

Although useful in certain situations, updating mirrors on every execution is usually unnecessary and increases update time. In most cases, the currently configured mirrors continue working correctly for long periods. For this reason, this functionality is optional, allowing mirrors to be refreshed only when necessary.

Examples of errors where updating mirrors may help because the system starts using more up-to-date and stable servers:

```text
error: failed retrieving file
error: database is not valid
error: failed to synchronize all databases
error: target not found
error: signature is invalid
error: corrupted database
```

This happens because mirrors are distributed replicas of official repositories and are not always perfectly synchronized with each other. Depending on the mirror status, situations such as the following may occur:

* the database has already been updated, but the package has not yet been replicated
* a mirror is temporarily offline
* the server has high latency
* files were partially synchronized
* a mirror became outdated compared to others
* cache/CDN synchronization issues occurred during downloads

---

## 📄 Log

The complete execution log is saved at:

```bash
~/bigupdate.log
```

The file is automatically recreated on each execution to prevent excessive growth. It stores the script output along with additional information about updated packages and their versions.

---

## 📜 License

MIT License

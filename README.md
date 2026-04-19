Lean Fedora Atomic Sway nightly image, built with [BlueBuild](https://blue-build.org/) and delivered via GitHub Actions.

Based on `quay.io/fedora-ostree-desktops/sway-atomic` with a curated set of packages, fonts, Flatpaks, and system configuration baked in.

## What's included

**Packages (rpm-ostree):**
- wireguard-tools, fail2ban, rclone, smartmontools, iotop
- gh (GitHub CLI), syncthing, neovim
- zathura + PDF support (mupdf)
- mako (notifications), tesseract + English OCR
- Inter + JetBrains Mono fonts
- zram-generator-defaults

**Removed from base:** firefox, firefox-langpacks, dunst

**Flatpaks (auto-installed on first boot):**
- Loupe (image viewer), Gnumeric, Fragments (torrent), Celluloid (video), Bitwarden
- Brave (browser), Obsidian

**Removed Flatpaks:** LibreOffice, Transmission, VLC

## Installation

### Fresh install (recommended)

1. Download the [Fedora Sway Atomic ISO](https://fedoraproject.org/atomic-desktops/sway/) and write it to a USB drive
2. Install stock Fedora Sway Atomic on the target machine
3. Boot into the fresh install and connect to the network
4. Rebase to this image:

   rpm-ostree rebase ostree-unverified-registry:ghcr.io/lars010101/fedora-sway:latest
   systemctl reboot

5. After reboot, optionally switch to signed pulls:

   rpm-ostree rebase ostree-image-signed:docker://ghcr.io/lars010101/fedora-sway:latest
   systemctl reboot

### Updates

The image rebuilds nightly via GitHub Actions, pulling in the latest Fedora Atomic base and packages. To update:

   rpm-ostree upgrade
   systemctl reboot

Or enable automatic updates with rpm-ostreed-automatic.timer.

## Migrating to a new machine

If you need to move this setup to a new device, there are three layers of state to handle:

### 1. Home directory

Your data, dotfiles, and app config all live in ~/. Back up with rsync, Syncthing, or any tool you prefer:

   rsync -aAXv --exclude='.cache' ~/ /mnt/backup/home/

### 2. Local /etc changes

On Atomic systems, /etc/ is a writable overlay. Any manual edits you've made outside of this image need to be captured:

   sudo ostree admin config-diff

Review the output. Anything worth keeping should be added to the files/etc/ directory in this repo so it becomes part of the image. Push the changes and let the nightly build pick them up.

### 3. Layered packages

Check for any packages you've manually layered on top of the image:

   rpm-ostree status

If there are layered packages, add them to the install: list in recipes/recipe.yml so they're baked into future builds.

### Migration summary

1. Back up your home directory
2. Audit /etc changes (ostree admin config-diff) → commit to repo
3. Audit layered packages (rpm-ostree status) → add to recipe
4. Push changes, wait for nightly build
5. Install stock Fedora Sway Atomic on the new machine
6. Rebase to this image
7. Restore home directory
8. Done

Once everything is captured in the repo, future machine swaps reduce to: install base → rebase → restore home.

## Repo structure

   recipes/recipe.yml    # BlueBuild recipe (packages, flatpaks, modules)
   files/etc/            # System config overlay (baked into image)
   cosign.pub            # Image signature verification key
   .github/workflows/    # CI/CD pipeline

## License

Apache 2.0

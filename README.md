# arya-repo
# MPTFA — My Plymouth Theme For All

SPDX-License-Identifier: BSD-2-Clause

Overview
--------
MPTFA is a simple, static (single-frame) Plymouth theme intended for Archcraft and other Linux distributions. It displays one image for boot and a different image for shutdown. The included example images are 1366×768; the script centers the image and can be extended to scale automatically.

Repository layout (recommended)
- LICENSE
- README.md
- mptfa.plymouth
- theme/
  - mptfa.script
  - boot.png
  - shutdown.png
- screenshots/
  - boot-1366x768.png
  - shutdown-1366x768.png
- packaging/ (optional: PKGBUILD, debian/)

Files
-----
- `mptfa.plymouth` — Plymouth metadata (ImageDir, ScriptFile).
- `theme/mptfa.script` — script engine file (selects boot/shutdown images and draws them).
- `theme/boot.png`, `theme/shutdown.png` — image assets (must be present before installation).
- `LICENSE` — BSD 2‑clause; Copyright (c) 2026 Arya.

Installation (Arch / Archcraft)
------------------------------
1. Install plymouth:
   sudo pacman -Syu plymouth

2. Copy theme files:
   sudo mkdir -p /usr/share/plymouth/themes/mptfa
   sudo cp -r theme/* /usr/share/plymouth/themes/mptfa/
   sudo cp mptfa.plymouth /usr/share/plymouth/themes/mptfa/

3. Ensure the `plymouth` hook is in `/etc/mkinitcpio.conf` (before `filesystems`), e.g.:
   HOOKS=(base udev plymouth filesystems ...)

4. Rebuild initramfs:
   sudo mkinitcpio -P

5. Set the theme (if plymouth-set-default-theme is present):
   sudo plymouth-set-default-theme mptfa
   sudo mkinitcpio -P

Installation (Debian / Ubuntu)
------------------------------
1. Install plymouth:
   sudo apt update
   sudo apt install plymouth plymouth-themes

2. Copy theme files:
   sudo mkdir -p /usr/share/plymouth/themes/mptfa
   sudo cp -r theme/* /usr/share/plymouth/themes/mptfa/
   sudo cp mptfa.plymouth /usr/share/plymouth/themes/mptfa/

3. Set theme and update initramfs:
   sudo plymouth-set-default-theme mptfa
   sudo update-initramfs -u

Notes on images
--------------
- Name images exactly `boot.png` and `shutdown.png` and place them in the `theme/` directory.
- The example images are 1366×768. On other resolutions the script centers them. If you want scaling for different screen sizes, see the "Script behavior" section below.
- Legal: the supplied images resemble Windows BSoD. If you did not create them from scratch, consider modifying them to avoid potential trademark/copyright issues. If you own the images, state that in README and license them under BSD as well.

Script behavior (mptfa.script)
------------------------------
- Default script selects `boot.png` when Plymouth mode is `boot`, otherwise uses `shutdown.png`.
- Current basic script centers the image on screen.
- To support multiple resolutions, extend the script to calculate a scaling ratio (preserve aspect ratio) and call sprite/image SetSize or Scale if the engine supports it. Example autoscaling logic is available in the repo (or I can add it on request).

Packaging
---------
Tarball (recommended for quick sharing)
- Prepare directory `MPTFA-1.0/` with files and `theme/` containing both images.
- Create tar.gz:
  tar -czvf MPTFA-1.0.tar.gz MPTFA-1.0

Arch package (PKGBUILD)
- Create PKGBUILD that installs files to `/usr/share/plymouth/themes/mptfa`.
- Optionally include a `.install` with a `post_install()` that runs `mkinitcpio -P`.

Debian package (minimal)
- Provide `debian/control`, and an executable `debian/postinst` that runs `update-initramfs -u` on configure.
- Use `dpkg-buildpackage` or debhelper tooling to build.

Permissions
-----------
After installing to /usr/share/plymouth/themes/mptfa, ensure files are owned by root and readable:
sudo chown -R root:root /usr/share/plymouth/themes/mptfa
sudo chmod -R 644 /usr/share/plymouth/themes/mptfa/*
sudo chmod 755 /usr/share/plymouth/themes/mptfa/*.script || true

Contributing
------------
- Add screenshots under `/screenshots`.
- For multi-resolution support, include alternate images (e.g., `boot-1920x1080.png`) and update `mptfa.script` to select based on `Window.GetWidth()`/`GetHeight()`.
- Open issues or PRs for bug fixes and improvements.

Troubleshooting
---------------
- If theme doesn’t appear: confirm files are in `/usr/share/plymouth/themes/mptfa` and `mptfa.plymouth` is present.
- If plymouth-set-default-theme not found: use the mkinitcpio method and ensure the plymouth hook is present.
- If images don’t scale: check your plymouth script engine version for supported sprite/image methods (SetSize, Scale).

License
-------
This project is licensed under the BSD 2-clause license. See the `LICENSE` file.

Author
------
Arya

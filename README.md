# arya-repo
# MPTFA — My Plymouth Theme For All

SPDX-License-Identifier: BSD-2-Clause

Overview
--------
MPTFA is a simple Plymouth theme that displays a single image for boot and another for shutdown. This release adds automatic scaling so the theme adapts to different screen resolutions while preserving aspect ratio.

Repository layout (recommended)
- LICENSE
- README.md
- mptfa/
  - mptfa.plymouth
  - mptfa.script      ← autoscaling script (updated)
  - boot.png
  - shutdown.png
- screenshots/ (optional)

Files
-----
- `mptfa/mptfa.plymouth` — theme metadata (ImageDir, ScriptFile).
- `mptfa/mptfa.script` — the script engine file. This version:
  - chooses `boot.png` for Plymouth mode `boot`, otherwise `shutdown.png`,
  - computes a scale factor to fit the image on screen while preserving aspect ratio,
  - optionally prevents upscaling (configurable in the script),
  - centers the image.
- `mptfa/boot.png`, `mptfa/shutdown.png` — image assets (include before packaging).
- `LICENSE` — BSD 2‑clause; Copyright (c) 2026 Arya.

Script behavior (autoscaling)
-----------------------------
- The updated script calculates a fit scale (min(screen_w / img_w, screen_h / img_h)) so the image will never be distorted.
- Configuration at top of script:
  - `allow_upscale = true` — set to `false` if you prefer images not to be enlarged on larger screens.
- The script tries several common plymouth script API calls to resize (Sprite.SetSize, Image.Scale, Sprite.SetTransform). If your plymouth version does not support resizing, the script will still center the original image.
- If you need exact API compatibility for your distribution's plymouth version, tell me the plymouth package version and I’ll adapt the script.

Installation (same as before)
-----------------------------
1. Copy the `mptfa` folder and `mptfa.plymouth` to `/usr/share/plymouth/themes/mptfa`:
   ```bash
   sudo mkdir -p /usr/share/plymouth/themes/mptfa
   sudo cp -r mptfa/* /usr/share/plymouth/themes/mptfa/
   sudo cp mptfa.plymouth /usr/share/plymouth/themes/mptfa/
   ```
2. Ensure `plymouth` appears in your initramfs hooks (e.g., `/etc/mkinitcpio.conf` for Arch):
   ```
   HOOKS=(base udev plymouth filesystems ...)
   ```
3. Rebuild initramfs:
   - Arch:
     ```bash
     sudo mkinitcpio -P
     ```
   - Debian/Ubuntu:
     ```bash
     sudo update-initramfs -u
     ```
4. Set default theme (if available):
   ```bash
   sudo plymouth-set-default-theme mptfa
   sudo mkinitcpio -P   # Arch; run update-initramfs on Debian if needed
   ```

Permissions & executable bit
----------------------------
Make the script executable and set ownership:
```bash
sudo chown -R root:root /usr/share/plymouth/themes/mptfa
sudo chmod -R 644 /usr/share/plymouth/themes/mptfa/*
sudo chmod 755 /usr/share/plymouth/themes/mptfa/*.script || true
```

Testing tips
------------
- Test in a VM first. Install plymouth on a test VM, copy the theme, set it and rebuild initramfs, then reboot the VM to see the splash.
- If the image is not scaled and remains too large or too small, check your plymouth version and logs (dmesg or journalctl) and let me know the plymouth package version so I can adjust method calls.

Image & licensing note
----------------------
- Name your images `boot.png` and `shutdown.png`.
- If you made the images yourself, license them under the project BSD license (note asset licensing in README if desired).
- If images are derived from Windows BSoD artwork, consider altering design to avoid potential trademark/copyright issues.

Author
------
Arya

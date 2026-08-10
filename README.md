# content-snap-core22

**A content snap providing a shared library and media stack for `core22` snaps — FFmpeg, mpv, VA-API hardware video acceleration, GTK themes and fonts.**

Shipping FFmpeg, mpv and a full theme stack inside every app snap makes each one huge. This snap carries them once; your app plugs into it and ships only its own code.

Unlike **[pyqt5-runtime-core22](https://github.com/rishabh3354/pyqt5-runtime-core22)**, this one does **not** include PyQt5 — use it when your app brings its own Python/Qt or isn't a Qt app at all.

[![Snap Store](https://img.shields.io/badge/Snap%20Store-content--snap--core22-E95420?style=flat-square&logo=snapcraft&logoColor=white)](https://snapcraft.io/content-snap-core22)

## What's inside

| | |
|---|---|
| **Media** | `ffmpeg`, `mpv` |
| **Hardware video acceleration** | `mesa-va-drivers` (AMD + generic), `intel-media-va-driver` (Intel Gen9+), `i965-va-driver` (older Intel), `vainfo` |
| **Qt integration** | `qt5-gtk-platformtheme`, `qt5-image-formats-plugins` |
| **Themes & icons** | `adwaita-icon-theme`, `light-themes`, `gnome-themes-standard`, `dmz-cursor-theme` |
| **Fonts & locales** | `fonts-ubuntu`, `locales-all` |
| **Core libs** | `glib`, `gdk-pixbuf`, `libglu1-mesa`, `libxkbcommon0`, `samba-libs`, `libdb5.3`, `libslang2` |
| **Desktop integration** | `gsettings-desktop-schemas`, `shared-mime-info`, `xdg-user-dirs` |

Base `core22` (Ubuntu 22.04), `amd64`, strict confinement.

Don't need FFmpeg and mpv? Use **[content-snap-core22-lite](https://github.com/rishabh3354/content-snap-core22-lite)**.

## Using it in your app

Add the plug to your app's `snap/snapcraft.yaml`:

```yaml
plugs:
  content-snap-core22:
    interface: content
    target: $SNAP/content-snap-core22
    default-provider: content-snap-core22
```

Point your runtime and the VA-API driver path at the mounted content:

```yaml
environment:
  SNAP_DESKTOP_RUNTIME: $SNAP/content-snap-core22

apps:
  yourapp:
    environment:
      LIBVA_DRIVERS_PATH: $SNAP/content-snap-core22/usr/lib/x86_64-linux-gnu/dri:/var/lib/snapd/lib/gl/dri:/usr/lib/x86_64-linux-gnu/dri
```

That `LIBVA_DRIVERS_PATH` line is what makes GPU-accelerated decoding actually work inside strict confinement — without it, libva falls back to software and users see slow playback with no obvious cause.

`default-provider` means snapd installs this snap automatically alongside your app.

## Building it yourself

```bash
git clone https://github.com/rishabh3354/content-snap-core22.git
cd content-snap-core22
snapcraft
```

## Related

- **[content-snap-core22-lite](https://github.com/rishabh3354/content-snap-core22-lite)** — same idea, without FFmpeg and mpv, with Noto fonts
- **[pyqt5-runtime-core22](https://github.com/rishabh3354/pyqt5-runtime-core22)** — adds PyQt5 and Qt 5.15
- **[pyqt5-runtime-core22-lite](https://github.com/rishabh3354/pyqt5-runtime-core22-lite)** — PyQt5 without the media stack

Used in production by [FormatLab](https://github.com/rishabh3354/FORMAT_LAB), [4KTUBE](https://github.com/rishabh3354/4KTUBE), [4KWALL](https://github.com/rishabh3354/4KWALL) and [DL-YouTube](https://github.com/rishabh3354/YOUTUBE-DL-PRO).

---

<details>
<summary><strong>Publishing notes</strong> — building and releasing a snap to the Snap Store</summary>

**1. Install Snapcraft**

```bash
sudo snap install snapcraft --classic
```

**2. Build**

```bash
snapcraft
```

Produces a `.snap` file, e.g. `content-snap-core22_22.04_amd64.snap`.

**3. Log in**

```bash
snapcraft login
```

**4. Register the name** (first time only)

```bash
snapcraft register <snap-name>
```

**5. Upload to a test channel**

```bash
snapcraft upload --release=edge <snap-name>_<version>_amd64.snap
```

Channels: `edge`, `beta`, `candidate`, `stable`.

**6. Test**

```bash
sudo snap install <snap-name> --edge
```

**7. Promote to stable**

```bash
snapcraft list-revisions <snap-name>
snapcraft release <snap-name> <revision> stable
```

Status and metrics live at the [Snapcraft dashboard](https://snapcraft.io/dashboard). Use `snapcraft --debug` for verbose build output.

</details>

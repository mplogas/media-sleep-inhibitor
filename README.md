# media-sleep-inhibitor

media-sleep-inhibitor is a small helper that prevents KDE Plasma from suspending or locking the screen while media players are actively playing audio via MPRIS. This is useful when listening to music (for example with Spotify) without disabling power management globally.

## Features

- Monitors all MPRIS-compatible players on the session bus.
- Inhibits KDE’s screensaver and sleep while at least one player is in `Playing` state.
- Automatically releases the inhibition when all players are paused or stopped.
- Works with common players like Spotify, VLC, Elisa, Strawberry, and other MPRIS-capable players.


## Requirements

- KDE Plasma desktop session.
- Python 3.
- D-Bus session bus.
- Python packages:
    - `dbus-python` (often `python3-dbus`)
    - `PyGObject` / `gi` (often `python3-gobject`)

Package names may differ across distributions; the examples below assume Fedora-like naming.

## Installation

1. Install dependencies (example for Fedora):

```bash
sudo dnf install python3-dbus python3-gobject
```

2. Clone this repository and install the script in your local bin:

```bash
git clone https://github.com/<your-user>/kde-media-sleep-inhibitor.git
cd kde-media-sleep-inhibitor
mkdir -p ~/.local/bin
cp media-inhibit.py ~/.local/bin/kde-media-sleep-inhibitor
chmod +x ~/.local/bin/kde-media-sleep-inhibitor
```


## Manual usage

Run the daemon in a terminal inside your KDE Plasma session:

```bash
~/.local/bin/kde-media-sleep-inhibitor
```

Then start your media player and begin playback. As long as at least one MPRIS player reports `Playing`, the script will keep the session from sleeping or locking. Stop with `Ctrl+C` in the terminal.

## Autostart via KDE

To start the script automatically on login using KDE Autostart:

1. Open **System Settings → Workspace → Autostart**.
2. Add a new **Login Script**.
3. Select `~/.local/bin/kde-media-sleep-inhibitor`.

The script will now run whenever you log into Plasma.

## Autostart via systemd (user service)

Alternatively, you can manage it as a user systemd service for better control and logging.

1. Create a user service unit:

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/kde-media-sleep-inhibitor.service << 'EOF'
[Unit]
Description=Inhibit KDE sleep while MPRIS media is playing

[Service]
ExecStart=%h/.local/bin/kde-media-sleep-inhibitor
Restart=on-failure

[Install]
WantedBy=default.target
EOF
```

2. Reload and enable the service:

```bash
systemctl --user daemon-reload
systemctl --user enable --now kde-media-sleep-inhibitor.service
```

3. Check status and logs if needed:

```bash
systemctl --user status kde-media-sleep-inhibitor.service
journalctl --user -u kde-media-sleep-inhibitor.service -f
```


With this setup, the script will run as part of your user session and automatically ensure that audio playback via MPRIS prevents KDE from going to sleep or locking the screen.
<span style="display:none">[^1][^2][^3]</span>

<div align="center">⁂</div>

[^1]: interests.linux_desktop_customization

[^2]: preferences.media_control

[^3]: location.distro


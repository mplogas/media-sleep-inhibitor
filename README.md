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
git clone https://github.com/<your-user>/media-sleep-inhibitor.git
cd media-sleep-inhibitor
mkdir -p ~/.local/bin
cp media-sleep-inhibitor.py ~/.local/bin/media-sleep-inhibitor.py
chmod +x ~/.local/bin/media-sleep-inhibitor.py
```


## Manual usage

Run the daemon in a terminal inside your KDE Plasma session:

```bash
~/.local/bin/media-sleep-inhibitor
```

Then start your media player and begin playback. As long as at least one MPRIS player reports `Playing`, the script will keep the session from sleeping or locking. Stop with `Ctrl+C` in the terminal.

## Autostart via KDE

To start the script automatically on login using KDE Autostart:

1. Open **System Settings → Workspace → Autostart**.
2. Add a new **Login Script**.
3. Select `~/.local/bin/media-sleep-inhibitor.py`.

The script will now run whenever you log into Plasma.

## Autostart via systemd (user service)

*recommended approach*

Alternatively, you can manage it as a user systemd service for better control and logging.

1. Create a user service unit:

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/media-sleep-inhibitor.service << 'EOF'
[Unit]
Description=Inhibit KDE sleep while MPRIS media is playing

[Service]
ExecStart=%h/.local/bin/media-sleep-inhibitor.py
Restart=on-failure

[Install]
WantedBy=default.target
EOF
```

2. Reload and enable the service:

```bash
systemctl --user daemon-reload
systemctl --user enable --now media-sleep-inhibitor.service
```

## Troubeshooting

If you run into anything odd (e.g., no inhibit entry when Spotify plays), check the logs while starting playback.

```bash
systemctl --user status media-sleep-inhibitor.service
journalctl --user -u media-sleep-inhibitor.service -f
```
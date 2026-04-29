# Caelestia Shell on niri Notes

This repository keeps Caelestia shell as an optional replacement for Waybar, but the upstream shell is designed around Hyprland. The local `caelestia/shell.json` is intentionally conservative so the shell can be tested under niri without replacing working Waybar behavior or relying on Hyprland-only IPC.

## Enabled Items

- `bar.entries`: keeps `logo`, `spacer`, `tray`, `clock`, `statusIcons`, and `power` enabled. These are the closest equivalents to the current Waybar status surface and are less tied to workspace/window IPC than Caelestia's workspace and active-window modules.
- `bar.status.showAudio`, `showMicrophone`, `showNetwork`, `showWifi`, `showBluetooth`, and `showBattery`: enabled because they use general desktop services such as PipeWire, NetworkManager, Bluetooth, and UPower rather than niri-specific workspace APIs.
- `bar.popouts.tray` and `bar.popouts.statusIcons`: enabled to preserve useful tray/status interactions during preview.
- `bar.scrollActions.volume` and `brightness`: enabled because they map to audio and brightness services rather than workspace dispatch. They still depend on the relevant runtime tools being installed.
- `session.enabled`: enabled so the power/session surface remains available. Its logout command is changed to `niri msg action quit`, which matches this niri setup instead of Hyprland-oriented logout commands.
- `general.apps.terminal`, `audio`, and `explorer`: set to local, common commands (`kitty`, `pavucontrol`, `xdg-open`) so Caelestia actions do not assume the upstream defaults.

## Hyprland Dependencies

- `bar.entries.workspaces`: disabled because Caelestia's workspace widget reads `Quickshell.Hyprland`, `Hyprland.workspaces`, and dispatches Hyprland workspace commands. niri exposes a different model and message API.
- `bar.entries.activeWindow` and `bar.popouts.activeWindow`: disabled because the active-window widget and preview read Hyprland toplevel metadata and Hyprland screencopy sources.
- `bar.scrollActions.workspaces`: disabled because scrolling workspaces dispatches Hyprland workspace commands.
- `bar.status.showKbLayout` and `showLockStatus`: disabled because these rely on Caelestia's Hyprland keyboard/device service for layout, Caps Lock, and Num Lock state.
- `bar.workspaces.*`: kept minimal and non-interactive for the same Hyprland workspace reason.

## niri Incompatibilities

- `general.idle.timeouts`: set to an empty list because the upstream default uses Hyprland-style `dpms off` and `dpms on` dispatch strings. This niri config already starts a separate swayidle script, so duplicating idle behavior would be risky.
- `session.commands.logout`: changed from `loginctl terminate-user` to `niri msg action quit`, matching the existing Waybar logout action and avoiding a broader session termination command.
- Hyprland global shortcuts are not copied. niri binds call local scripts directly instead of using Hyprland global shortcut registration.

## Temporarily Conservative Disables

- `background.enabled`, `wallpaperEnabled`, `desktopClock`, and `visualiser`: disabled for the first niri preview to avoid conflicts with the existing wallpaper/background setup and to keep Caelestia focused on bar replacement.
- `dashboard.enabled`, `launcher.enabled`, `sidebar.enabled`, `utilities.enabled`, and `osd.enabled`: disabled because these panels involve more Caelestia services, shortcuts, focus grabs, and hover behavior. They can be enabled one at a time after the bar path is stable.
- `utilities.toasts.configLoaded`, `capsLockChanged`, `numLockChanged`, and `kbLayoutChanged`: disabled to avoid startup noise and Hyprland-keyboard-derived notifications while testing under niri.

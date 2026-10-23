# appimage-install

A small Bash script that installs an AppImage properly. It gives the AppImage a permanent home, pulls out its bundled icon, and creates a `.desktop` launcher entry, so the app shows up **with its own icon** in rofi (`drun` mode), wofi, fuzzel and other desktop-entry launchers.

## What it does

1. Copies the AppImage to `~/Applications/<name>.AppImage` and makes it executable.
2. Extracts the AppImage to a temp dir, then copies its icon to `~/.local/share/icons/<name>.png` (or `.svg`). It tries `.DirIcon` first, then the `Icon=` from the bundled `.desktop` file, then the largest png/svg it can find.
3. Writes `~/.local/share/applications/<name>.desktop` with absolute paths. `Name`, `Categories` and `StartupWMClass` are taken from the AppImage's own `.desktop` file.
4. Runs `update-desktop-database`.

## Install

```sh
mkdir -p ~/.local/bin
curl -fsSL https://raw.githubusercontent.com/aucapricornus/appimage-install/main/appimage-install -o ~/.local/bin/appimage-install
chmod +x ~/.local/bin/appimage-install
```

Make sure `~/.local/bin` is on your `PATH`.

**Requirements:** bash, coreutils, findutils, `fuse2` (most AppImages need it to run: `sudo pacman -S fuse2` / `sudo apt install libfuse2`). `update-desktop-database` (desktop-file-utils) is optional.

## Usage

```sh
appimage-install ~/Downloads/Something-1.2.3-x86_64.AppImage        # short name derived: "something"
appimage-install ~/Downloads/Something.AppImage mycustomname         # choose the short name
```

The source file is copied, not moved. To **update** an app, run the script again with the new AppImage and the same short name.

### Uninstall

```sh
rm ~/Applications/<name>.AppImage ~/.local/share/icons/<name>.* ~/.local/share/applications/<name>.desktop
```

## Tips

- **rofi:** only `drun` mode lists `.desktop` apps. Icons need `-show-icons` or `show-icons: true;` in `config.rasi`:
  ```sh
  rofi -show drun -show-icons
  ```
- **Check an entry:** `desktop-file-validate ~/.local/share/applications/<name>.desktop`. Some AppImages ship invalid categories, e.g. `Audio;` without `AudioVideo;`.
- **Blurry Electron apps on Wayland:** add `--ozone-platform-hint=auto` before `%U` in the entry's `Exec=` line.

## License

MIT

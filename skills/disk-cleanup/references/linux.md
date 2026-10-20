# Linux commands

Start read-only. Some commands need root. Ask before elevating.

## Volume

```sh
df -h
lsblk -f
```

## Top-level sizes

```sh
du -x -h -d 1 /
du -x -h -d 1 ~
```

## Large files

```sh
find / -xdev -type f -size +1G -print 2>/dev/null
```

## Processes and open files

```sh
ps aux | grep <name>
lsof +D <path>
```

## Caches

- `~/.cache`
- npm: `~/.npm`
- pnpm: `pnpm store path`
- yarn: `~/.cache/yarn`
- bun: `~/.bun/install/cache`
- pip: `~/.cache/pip`
- uv: `~/.cache/uv`
- Cargo: `~/.cargo/registry/cache` and `src`
- Gradle: `~/.gradle/caches`
- Maven: `~/.m2/repository`
- NuGet: `~/.nuget/packages`
- Journal: `journalctl --disk-usage`, then `journalctl --vacuum-size=500M` only with approval

Check active project pins before removing Gradle wrapper distributions, Rust toolchains, or SDK components.

## Browser caches

Delete only cache directories. Keep the profile root.

- Chrome: `~/.cache/google-chrome`
- Edge: `~/.cache/microsoft-edge`
- Firefox: `~/.cache/mozilla/firefox`

Keep cookies, history, sessions, and profile data under `~/.config` or `~/.mozilla`.

## Trash

`~/.local/share/Trash/files`

Emptying it is a separate, explicit step.

## Snapshots

Check for Btrfs, Snapper, or Timeshift snapshots:

```sh
btrfs subvolume list /
snapper list
timeshift --list
```

Delete only with explicit approval. A snapshot may be the only restore point.

## Package cleanup

```sh
snap list --all
flatpak uninstall --unused
```

Remove old snap revisions only with approval.

## Size check

`du` reports allocated blocks. `ls -l` reports apparent size. GNU `du` counts hardlinked files once by default. Compare `df -h` before and after to confirm what a delete actually freed.

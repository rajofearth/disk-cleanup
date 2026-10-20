# macOS commands

Start read-only. Some commands need sudo. Ask before elevating.

## Volume

```sh
df -h
diskutil list
diskutil info /
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

- `~/Library/Caches`
- `~/Library/Logs`
- `~/Library/Application Support/<app>/Cache`
- Xcode DerivedData: `~/Library/Developer/Xcode/DerivedData`
- Xcode simulator caches: `~/Library/Developer/CoreSimulator/Caches`
- Xcode archives are user data. Ask before touching them.
- Homebrew: `brew cleanup --prune=all`

Package manager locations:

```sh
npm config get cache
pnpm store path
yarn cache dir
bun pm cache
pip cache dir
uv cache dir
```

Common paths:

- Cargo: `~/.cargo/registry/cache` and `src`
- Gradle: `~/.gradle/caches`
- Maven: `~/.m2/repository`
- NuGet: `~/.nuget/packages`

Check active project pins before removing Gradle wrapper distributions, Rust toolchains, or SDK components.

## Browser caches

Delete only cache directories. Keep the profile root.

- Chrome: `~/Library/Caches/Google/Chrome`
- Edge: `~/Library/Caches/com.microsoft.edgemac`
- Firefox: `~/Library/Caches/Firefox/Profiles`

Keep cookies, history, sessions, and profile data under `~/Library/Application Support`.

## Trash

`~/.Trash`

Emptying it is a separate, explicit step.

## Snapshots

```sh
tmutil listlocalsnapshots /
diskutil apfs listSnapshots /
tmutil deletelocalsnapshots <date>
```

Delete only with explicit approval. A local snapshot may be the only restore point.

## Size check

`du` reports allocated blocks. `ls -l` reports apparent size. Compare `df -h` free space before and after to confirm what a delete actually freed.

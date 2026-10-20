# Windows commands

Start read-only. Some commands need admin. Ask before elevating.

## Volume

```powershell
Get-Volume | Format-Table DriveLetter,FileSystemLabel,FileSystem,DriveType,Size,SizeRemaining
Get-PhysicalDisk | Format-Table FriendlyName,MediaType,Size
fsutil volume diskfree C:
```

## Shadow copies

```powershell
vssadmin list shadowstorage /for=C:
vssadmin list shadows /for=C:
vssadmin delete shadows /for=C: /all
```

Delete shadow copies only with explicit approval. They may be the only restore point. Run `vssadmin` from an elevated prompt.

## Top-level sizes

```powershell
Get-ChildItem 'C:\' -Directory -Force | ForEach-Object {
  $s = (Get-ChildItem $_.FullName -Recurse -Force -File -ErrorAction SilentlyContinue |
    Measure-Object Length -Sum).Sum
  [pscustomobject]@{ Name = $_.Name; GB = [math]::Round($s / 1GB, 2) }
} | Sort-Object GB -Descending | Format-Table -AutoSize
```

`Get-ChildItem` sums logical length and counts each hardlink. Use the free-space delta to confirm what a delete actually frees.

## Large files

```powershell
Get-ChildItem 'C:\' -Recurse -Force -File -ErrorAction SilentlyContinue |
  Where-Object Length -ge 1GB |
  Sort-Object Length -Descending |
  Select-Object -First 50 @{ n = 'GB'; e = { [math]::Round($_.Length / 1GB, 2) } }, FullName
```

## Processes and open handles

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.CommandLine -match 'C:\\path' } |
  Select-Object ProcessId, Name, CommandLine
```

`handle.exe <path>` from Sysinternals, or Resource Monitor, shows open handles.

## Package caches and stores

```powershell
npm config get cache
npm cache clean --force
pnpm store path
pnpm store prune
yarn cache dir
yarn cache clean
bun pm cache
bun pm cache rm
pip cache dir
pip cache purge
uv cache dir
uv cache clean
```

Common paths:

- Cargo: `%USERPROFILE%\.cargo\registry\cache` and `src`
- Gradle: `%USERPROFILE%\.gradle\caches`
- Maven: `%USERPROFILE%\.m2\repository`
- NuGet: `%USERPROFILE%\.nuget\packages`

Check active project pins before removing Gradle wrapper distributions, Rust toolchains, or SDK components.

## Browser caches

Delete only cache directories. Keep the profile root.

- Chrome: `%LOCALAPPDATA%\Google\Chrome\User Data\<profile>\Cache`, `Code Cache`, `GPUCache`, `ShaderCache`, `GrShaderCache`
- Edge: `%LOCALAPPDATA%\Microsoft\Edge\User Data\<profile>\...`
- Brave: `%LOCALAPPDATA%\BraveSoftware\Brave-Browser\User Data\<profile>\...`
- Firefox: `%APPDATA%\Mozilla\Firefox\Profiles\<profile>\cache2`

Keep `Cookies`, `History`, `Login Data`, `Web Data`, `Sessions`, `Service Worker`, `IndexedDB`, `Local Storage`, `Session Storage`, and `Preferences`.

## Trash

```powershell
Get-ChildItem 'C:\$RECYCLE.BIN' -Recurse -Force -File -ErrorAction SilentlyContinue |
  Measure-Object Length -Sum
Clear-RecycleBin -DriveLetter C -Force
```

Check the size first. On a non-system drive, use that drive letter. Some recycle bins need admin to read or clear.

## Size check

Compare `Get-Volume` free space before and after. A big folder can free little when its files are hardlinked, sparse, or held open.

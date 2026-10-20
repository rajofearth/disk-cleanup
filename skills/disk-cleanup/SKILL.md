---
name: disk-cleanup
description: Disk cleanup. Use when the user asks to free up space, asks what is using disk space, or wants caches, build artifacts, old SDKs, VMs, or app databases cleaned without losing personal data.
license: MIT
metadata:
  version: "1.0.0"
---

# Disk cleanup

Free space without breaking the machine. Every deletion has a restore cost: reinstall, rebuild, or restore from backup. Price it before touching anything.

Audit first. Measure, prove what is safe, get approval, then delete.

## Build the keep list

Write down what must survive before measuring anything. Ask only about gaps the request leaves open.

- personal files, photos, videos, documents, downloads
- browser cookies, history, sessions, profiles, extensions
- app chats, settings, databases, credentials, licenses
- project source, uncommitted work, `.git`, LFS objects, datasets
- VM disks, game saves, ROMs, emulator images, snapshots, and backups still in use
- package stores, SDKs, toolchains, and caches that active projects reference
- anything the user names

Every candidate gets checked against this list. If you cannot tell, keep it and ask.

The keep list is done when every constraint the user named is on it, and every category above is either kept or the user has put it out of scope.

## 1. Measure the volume

Find the mount point, filesystem, total size, free space, and whether it is a fixed disk, removable disk, or network share. Record the baseline. Stay on the volume the user asked about. If they did not name one, ask.

Platform commands live in `references/`. Read `references/windows.md`, `references/macos.md`, or `references/linux.md` for the current OS. Measure the top level, then the next level down. You want the few directories that explain most of the usage, not a perfect tree.

Apparent size lies. Hardlinks, sparse files, compression, deduplication, snapshots, and virtual disks can make a folder look bigger than the space it will free. A 40 GB folder can free 25 GB. When the numbers matter, use allocated size or the free-space delta.

Some paths need admin or sudo. Ask for elevation when a check requires it. Mark unreadable paths as unknown. `System Volume Information` on Windows and snapshot stores on Linux are common examples.

Measurement is done when the baseline is recorded and every top-level directory is measured or marked unreadable.

## 2. Find candidates

Look for these groups. Verify each one before it reaches a tier.

- trash and recycle bins
- browser HTTP and GPU caches, not profiles
- package manager caches and stores: npm, pnpm, yarn, bun, pip, uv, conda, cargo, Go, Gradle, Maven, NuGet
- build outputs: `target`, `build`, `dist`, `.next`, `.venv`, `__pycache__`, `.cxx`, Xcode DerivedData
- reinstallable dependencies: `node_modules`, virtualenvs, old SDKs, duplicate NDKs, old emulator images
- logs, crash dumps, thumbnails, old installers, temp files
- idle app versions, VM images, snapshots, large SQLite databases
- user content: games, media, models, archives

The biggest folder is often a package store that active projects need. Size alone is not proof.

The audit is done when every large directory or file is explained, on the keep list, or on the candidate list. If something is unexplained, keep digging.

## 3. Prove what is safe

For each candidate, answer five questions.

1. Is it a cache or a rebuildable artifact? If it holds state or the only copy of data, it is not a cache.
2. Is something using it now? Check processes, open handles, and recent writes. If a process holds it, ask the user to close it or skip the path.
3. Does an active project reference it? Check lockfiles, `packageManager`, `.modules.yaml`, Gradle wrapper versions, `rust-toolchain.toml`, Docker volumes, and IDE settings. If a project pins a store or toolchain, keep it.
4. Is the size real? Compare apparent size with the free-space change where possible. Hardlinked `node_modules` can look huge and free little.
5. What is the restore cost? Reinstall, rebuild, or restore from backup. A VM disk, app database, game save, or LFS object may cost data.

Write the evidence next to the path. "Safe" without a reason is not safe.

Proof is done when every candidate has five written answers, including its restore cost.

## 4. Build the tiers

Put every candidate in one tier. Ask for approval by tier.

1. Caches and trash. Temporary data an app can rebuild, plus the recycle bin. Restore cost: none or a re-download for caches; data for anything the recycle bin still holds. Inspect the bin and get approval before emptying it.
2. Rebuildable artifacts. Outputs that cost time to regenerate. Restore cost: rebuild time.
3. Reinstallable dependencies and unused toolchains. Packages and pinned versions that can be downloaded again. Check project pins first, including build files, environment variables, and AVDs. Restore cost: reinstall time. Say which projects need it.
4. Idle apps, VMs, snapshots or shadow copies, and large app databases. These can hold state. Restore cost: app state or data. Get explicit approval and a backup where relevant. For databases, close the app, back up, check integrity, compact, and verify. For VMs, unregister before deleting disks. For snapshots, check active reverts and backups.
5. User content. Games, media, models, archives, datasets. Restore cost: data. Get per-item approval.

Estimate reclaim per tier. Mark estimates that hardlinks or sparse files may inflate. If the user says "clean whatever is safe," tiers 1 and 2 are fair game. Tier 3 waits for a reference check and a direct yes after you explain the reinstall cost. Tiers 4 and 5 wait for a direct yes.

Tiering is done when every candidate sits in one tier and the approval request names the estimate and restore cost.

## 5. Execute

Record free space again. Work one tier at a time. Check free space after each tier, not just at the end. Write a log to a temp file and keep it until verification passes.

- Delete caches with the application's own cleanup command when it exists, so metadata stays consistent.
- Move user data to the OS trash when possible. Emptying the trash is a separate, explicit step.
- Log every path and the result. A skipped locked file is a result, not a failure to hide.
- If a file is locked, skip it and report it. Never widen the delete to its parent.
- For package stores, run the manager's prune command. Keep any store active projects reference.
- For an app database, close the app and its service. Read `references/app-databases.md` before compacting.
- For a VM or virtual disk, read `references/vms-and-virtual-disks.md` before removing it.
- For browser profiles, delete only cache directories. Keep cookies, history, sessions, service workers, IndexedDB, local storage, and preferences.

A tier is done when every path in it is deleted, skipped with a reason, or moved to a later tier. Execution is done when every approved tier meets that bar. If free space barely moves after a large delete, stop and check hardlinks, sparse files, open handles, and snapshots before deleting more.

## 6. Verify

Check the keep list first. Those paths should still exist, with the same contents or size. Then check:

- free space before and after
- app databases pass integrity checks
- project source, `.git`, LFS, and lockfiles still exist
- browser profiles still have cookies, history, and sessions
- package stores and toolchains still exist
- no candidate is left half-deleted

Verification is done when the keep list passes and the free-space delta is explained. If an app fails, restore the backup before doing anything else.

## 7. Report

Report the before and after free space, the actual amount freed, and each tier's contribution. Name what was preserved, what needs a reinstall or rebuild, and what still needs a decision. Mention locked or ambiguous paths.

The report is done when the user can see what was freed, what was preserved, what needs a reinstall or rebuild, and what still needs a decision.

## Guardrails

The keep list is the default. Stop and ask before touching it.

Ask before:

- killing a process to unlock a file
- entering tier 3, 4, or 5 without a direct yes
- continuing after a delete frees much less than the path's apparent size
- deleting a snapshot or shadow copy that is the only restore point

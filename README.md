# disk-cleanup

An agent skill that frees disk space without breaking the machine.

## What it does

`disk-cleanup` audits a disk before deleting anything. It prices each deletion by restore cost, groups candidates into approval tiers, and verifies the keep list afterward.

It works on Windows, macOS, and Linux. It covers caches, build artifacts, package stores, app databases, VMs, WSL, and Docker.

## Install

```sh
npx skills add rajofearth/disk-cleanup
```

Install only the disk-cleanup skill:

```sh
npx skills add rajofearth/disk-cleanup --skill disk-cleanup
```

With GitHub CLI 2.90 or newer:

```sh
gh skill install rajofearth/disk-cleanup disk-cleanup
```

Pin a release:

```sh
gh skill install rajofearth/disk-cleanup disk-cleanup@v1.0.0
```

## What is inside

```text
skills/
└── disk-cleanup/
    ├── SKILL.md
    └── references/
        ├── app-databases.md
        ├── linux.md
        ├── macos.md
        ├── vms-and-virtual-disks.md
        └── windows.md
CHANGELOG.md
LICENSE
README.md
```

## Safety

The skill keeps personal files, browser profiles, project source, Git and LFS data, package stores in use, and VM disks by default. It asks before tiered deletions and backs up app databases before compacting them. Read it before running it.

## Versioning

The current version is `1.0.0`. It is stored in `skills/disk-cleanup/SKILL.md` under `metadata.version`, and each release is tagged `vX.Y.Z`.

To ship an update:

1. Bump `metadata.version` and `metadata.updated` in `skills/disk-cleanup/SKILL.md`.
2. Add the change to `CHANGELOG.md`.
3. Commit, tag `vX.Y.Z`, and push the tag.
4. Create a GitHub release from the tag.

## License

MIT

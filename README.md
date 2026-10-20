# GH Use It

A small public repo for agent skills.

## disk-cleanup

`disk-cleanup` audits a disk before deleting anything. It prices each deletion by restore cost, groups candidates into approval tiers, and verifies the keep list afterward.

It works on Windows, macOS, and Linux. It covers caches, build artifacts, package stores, app databases, VMs, WSL, and Docker.

### Install

```sh
npx skills add rajofearth/gh-use-it
```

Install only the disk-cleanup skill:

```sh
npx skills add rajofearth/gh-use-it --skill disk-cleanup
```

With GitHub CLI 2.90 or newer:

```sh
gh skill install rajofearth/gh-use-it disk-cleanup
```

### What is inside

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
```

### Safety

The skill keeps personal files, browser profiles, project source, Git and LFS data, package stores in use, and VM disks by default. It asks before tiered deletions and backs up app databases before compacting them. Read it before running it.

### License

MIT

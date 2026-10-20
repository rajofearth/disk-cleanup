# VMs and virtual disks

Use this when a VM image, WSL distro, or Docker disk is a candidate in tier 3 or 4.

Confirm the VM or distro is off. Save its configuration. Unregister it before deleting disks. Keep volumes and distro data unless the user approves their removal.

## Hyper-V

```powershell
Get-VM
Get-VMHardDiskDrive -VMName <name>
Remove-VM -Name <name> -Force
```

`Remove-VM` unregisters the VM. It may not delete the disks. Delete the VHDX files only after approval.

## VirtualBox

```sh
VBoxManage list vms
VBoxManage showvminfo <uuid>
VBoxManage unregistervm <uuid> --delete
```

`--delete` deletes attached disks. Use it only after approval.

## VMware

```sh
vmrun list
```

Remove the VM from the inventory, then delete its disks after approval. For Parallels, UTM, or another hypervisor, use its list command, confirm the VM is off, remove it from inventory, then delete disks after approval.

## WSL

```powershell
wsl --list --verbose
wsl --manage <distro> --set-sparse true
```

If `--set-sparse` is unavailable, compact the VHDX with `diskpart` from an elevated prompt:

```text
select vdisk file="C:\path\to\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
```

Keep the distro and its data unless the user approves removal.

## Docker

```sh
docker system df
docker ps -a
docker system prune
docker volume ls
```

`docker system prune` removes stopped containers, unused networks, dangling images, and build cache. Check `docker ps -a` first because stopped containers may hold state.

Keep volumes unless the user approves their removal. If Docker Desktop's disk file does not shrink after the prune, use its documented disk compaction, not a reset.

## Linux VMs

```sh
virsh list --all
virsh undefine <name> --remove-all-storage
```

`--remove-all-storage` deletes disks. Use it only after approval.

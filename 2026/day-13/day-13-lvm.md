# Day 13 – Linux Logical Volume Management (LVM)


# Task 1: Check Current Storage

Before creating LVM components, check the current storage configuration.

## Commands

```bash
lsblk
pvs
vgs
lvs
df -h
```

# Task 2: Create Physical Volume

A Physical Volume (PV) is created from a disk or partition.

## Command

```bash
pvcreate /dev/sdb
```

## Verify

```bash
pvs
```

# Task 3: Create Volume Group

A Volume Group (VG) combines one or more physical volumes into a storage pool.

## Command

```bash
vgcreate devops-vg /dev/sdb
```

## Verify

```bash
vgs
```

# Task 4: Create Logical Volume

A Logical Volume (LV) is created inside the volume group and acts like a partition.

## Command

```bash
lvcreate -L 500M -n app-data devops-vg
```

## Verify

```bash
lvs
```


# Task 5: Format and Mount Logical Volume

The logical volume must be formatted with a filesystem and mounted.

## Format Filesystem

```bash
mkfs.ext4 /dev/devops-vg/app-data
```

## Create Mount Directory

```bash
mkdir -p /mnt/app-data
```

## Mount the Volume

```bash
mount /dev/devops-vg/app-data /mnt/app-data
```

## Verify Mount

```bash
df -h /mnt/app-data
```

# Task 6: Extend the Logical Volume

LVM allows expanding storage without recreating the filesystem.

## Extend Logical Volume

```bash
lvextend -L +200M /dev/devops-vg/app-data
```

## Resize Filesystem

```bash
resize2fs /dev/devops-vg/app-data
```

## Verify Extension

```bash
df -h /mnt/app-data
```


# Commands Summary

| Step              | Command                                     |
| ----------------- | ------------------------------------------- |
| Check storage     | lsblk, pvs, vgs, lvs, df -h                 |
| Create PV         | pvcreate /dev/sdb                           |
| Create VG         | vgcreate devops-vg /dev/sdb                 |
| Create LV         | lvcreate -L 500M -n app-data devops-vg      |
| Format filesystem | mkfs.ext4 /dev/devops-vg/app-data           |
| Mount volume      | mount /dev/devops-vg/app-data /mnt/app-data |
| Extend volume     | lvextend -L +200M /dev/devops-vg/app-data   |
| Resize filesystem | resize2fs /dev/devops-vg/app-data           |

---
## Screenshots

<img width="1920" height="972" alt="Screenshot from 2026-03-13 11-11-08" src="https://github.com/user-attachments/assets/199c773e-afc5-4dc4-adcf-0f9ed8d2c536" />

<img width="1920" height="640" alt="Screenshot from 2026-03-13 11-15-52" src="https://github.com/user-attachments/assets/ba98a98c-b3fb-4b94-a886-02f67b69f3db" />

<img width="1920" height="773" alt="Screenshot from 2026-03-13 11-20-52" src="https://github.com/user-attachments/assets/aceaec05-7069-4014-953d-7f375816d764" />

---
# What I Learned

1. LVM provides flexible storage management by allowing logical volumes to be resized dynamically without affecting running systems.

2. LVM storage hierarchy follows the structure:

Disk → PV → VG → LV → Filesystem → Mount

3. Logical volumes can be extended easily using lvextend and resize2fs without needing to reformat or unmount the filesystem.

---



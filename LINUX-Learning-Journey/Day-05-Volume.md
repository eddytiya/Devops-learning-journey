# Linux for DevOps - Day 4

## AWS Volume Management and LVM Practice

Today I practiced AWS EBS volume management on an Ubuntu EC2 instance. The main focus was understanding how storage is attached, checked, formatted, mounted, and managed using LVM.

## What I Practiced

- Created a new EC2 instance
- Checked the default root volume
- Created new EBS volumes
- Attached multiple EBS volumes to the instance
- Verified attached disks using Linux commands
- Learned the difference between attached storage and mounted storage
- Created physical volumes using LVM
- Created a volume group
- Created logical volumes
- Formatted logical volumes
- Mounted volumes into directories
- Checked mounted storage using `df -h`

## Important Concept

Attaching a volume means AWS has connected the disk to the EC2 instance.

Mounting a volume means Linux has connected that disk to a directory so users and applications can store files there.

## 1. Check Current Disk Layout

Use `lsblk` to view block devices:

```bash
lsblk
```

Expected output:

```text
Shows root disk and any attached EBS volumes.
```

Use `df -h` to check mounted filesystems:

```bash
df -h
```

Expected output:

```text
Shows only mounted filesystems and their available storage.
```

## 2. Create and Attach EBS Volumes in AWS

Steps followed in AWS Console:

```text
EC2 Dashboard -> Elastic Block Store -> Volumes -> Create Volume
```

Then attach the volume:

```text
Select Volume -> Actions -> Attach Volume -> Choose EC2 Instance -> Attach
```

Device names should commonly start from `/dev/sdf`, because earlier device names are already used by root/system disks.

Example attached volumes:

```text
10 GB volume
12 GB volume
14 GB volume
```

After attaching, check again:

```bash
lsblk
```

Expected output:

```text
New disks appear, but they are not mounted yet.
```

## 3. Create Physical Volumes

Check available physical volumes:

```bash
sudo pvs
```

Create physical volumes from attached disks:

```bash
sudo pvcreate /dev/xvdf /dev/xvdg /dev/xvdh
```

Expected output:

```text
Physical volume successfully created.
```

Verify:

```bash
sudo pvs
```

## 4. Create a Volume Group

Create a volume group using the physical volumes:

```bash
sudo vgcreate devops-vg /dev/xvdf /dev/xvdg /dev/xvdh
```

Expected output:

```text
Volume group devops-vg successfully created.
```

Check volume groups:

```bash
sudo vgs
```

## 5. Create Logical Volumes

Create a logical volume:

```bash
sudo lvcreate -L 10G -n devops-lv devops-vg
```

Expected output:

```text
Logical volume devops-lv created.
```

Check logical volumes:

```bash
sudo lvs
```

Detailed view:

```bash
sudo lvdisplay
```

## 6. Format the Logical Volume

Before mounting, format the logical volume:

```bash
sudo mkfs.ext4 /dev/devops-vg/devops-lv
```

Expected output:

```text
Creates an ext4 filesystem on the logical volume.
```

## 7. Create a Mount Directory

Create a directory where the volume will be mounted:

```bash
sudo mkdir /mnt/devops-data
```

## 8. Mount the Logical Volume

Mount the logical volume:

```bash
sudo mount /dev/devops-vg/devops-lv /mnt/devops-data
```

Verify:

```bash
df -h
```

Expected output:

```text
The new logical volume appears mounted at /mnt/devops-data.
```

## 9. Check Block Devices Again

```bash
lsblk
```

Expected output:

```text
Shows physical disks, LVM mapping, and mount point.
```

## 10. Optional: Make Mount Permanent

Find UUID:

```bash
sudo blkid
```

Edit `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Add an entry:

```text
UUID=<your-volume-uuid> /mnt/devops-data ext4 defaults,nofail 0 2
```

Test the file:

```bash
sudo mount -a
```

## Command Summary

| Command | Purpose |
|---|---|
| `lsblk` | Shows block devices and disk layout |
| `df -h` | Shows mounted filesystem usage |
| `sudo pvs` | Shows LVM physical volumes |
| `sudo pvcreate /dev/xvdf` | Creates a physical volume |
| `sudo vgcreate devops-vg /dev/xvdf` | Creates a volume group |
| `sudo vgs` | Shows volume groups |
| `sudo lvcreate -L 10G -n devops-lv devops-vg` | Creates a logical volume |
| `sudo lvs` | Shows logical volumes |
| `sudo mkfs.ext4 /dev/devops-vg/devops-lv` | Formats the logical volume |
| `sudo mkdir /mnt/devops-data` | Creates mount directory |
| `sudo mount /dev/devops-vg/devops-lv /mnt/devops-data` | Mounts logical volume |
| `sudo blkid` | Shows filesystem UUIDs |
| `sudo mount -a` | Tests `/etc/fstab` entries |

## What I Learned

- Root volumes have minimum size limits depending on the AMI.
- EBS volumes must be created and attached before Linux can use them.
- Attached volumes do not automatically become usable directories.
- `lsblk` shows disks and partitions.
- `df -h` shows mounted filesystems.
- LVM gives flexible storage management using physical volumes, volume groups, and logical volumes.
- A volume must be formatted before it can be mounted.
- Mounting connects a disk or logical volume to a directory path.
- `/etc/fstab` is used for permanent mounts after reboot.

## Day 4 Outcome

By the end of this practice, I understood how AWS EBS volumes connect with Linux storage management and how DevOps engineers prepare storage for applications on cloud servers.

## Hashtags

`#Linux` `#DevOps` `#AWS` `#EC2` `#EBS` `#LVM` `#CloudComputing` `#Ubuntu` `#LearningInPublic`

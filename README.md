# Mount a new disk and extend an LVM volume on an Ubuntu 22.04 VM running in VMware
In Ubuntu 22.04 on a VMware VM, after adding a new virtual disk, you can extend an existing LVM volume by creating a physical volume (pvcreate), adding it to the volume group (vgextend), and then resizing the logical volume (lvextend). Finally, resize the filesystem (e.g., with resize2fs) to use the new space.

### Prerequisites:
* You have added a new virtual disk to your VM in VMware.

* You have root or sudo access on the Ubuntu VM.

* The existing LVM setup is used for your root or data partitions.

### 1. Rescan for the new disk

```shell
sudo lsblk
```

```shell
sudo dmesg | grep sd
```
You should see something like /dev/sdb (the new disk).

If it doesn't show, rescan manually:

```shell
echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan
```
Then check again:
```shell
lsblk
```
### 2. Create a new physical volume (PV)
Assuming the new disk is /dev/sdb:
```shell
sudo pvcreate /dev/sdb
```
### 3. Extend the Volume Group (VG)
Find your volume group name:
```shell
sudo vgdisplay
```
Example output:
```shell
VG Name               ubuntu-vg
```
Add the new PV to the VG:
```shell
sudo vgextend ubuntu-vg /dev/sdb
```

### 4. Extend the Logical Volume (LV)
List logical volumes:
```shell
sudo lvdisplay
```
Example LV path: /dev/ubuntu-vg/ubuntu-lv

Now extend it (e.g., use all available space):

```shell
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

### 5. Resize the filesystem
Check the filesystem type:
```shell
df -Th
``
If it's ext4:
```shell
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```
If it's xfs (less common on Ubuntu):
```shell
sudo xfs_growfs /
```
#### 6. ✅ Confirm
Check the new space is available:
```shell
df -h
```

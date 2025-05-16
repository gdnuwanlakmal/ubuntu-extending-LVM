# ubuntu-extending-LVM
In Ubuntu 22.04 on a VMware VM, after adding a new virtual disk, you can extend an existing LVM volume by creating a physical volume (pvcreate), adding it to the volume group (vgextend), and then resizing the logical volume (lvextend). Finally, resize the filesystem (e.g., with resize2fs) to use the new space.

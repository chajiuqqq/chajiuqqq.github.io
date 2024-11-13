由于主分区大小只有8g，pip安装依赖时提示空间不足，考虑对主分区进行扩容。

 
本来看了 https://askubuntu.com/a/937351 ，用growpart扩容/dev/sda5即可，但是出现`The filesystem is already 9906432 (4k) blocks long.  Nothing to do!`这种提示。

看到[Ubuntu ext4 partition is not being extended or resized as expected with growpart or resize2fs](https://unix.stackexchange.com/questions/687485/ubuntu-ext4-partition-is-not-being-extended-or-resized-as-expected-with-growpart)，如果`fdisk -l`看到主分区和扩展分区有重合（漏截图了），需要先用growpart扩容/dev/sda2，再扩容/dev/sda5  ，最后用resize2fs修改/dev/sda5 才可以。（似乎是因为/dev/sda2是/dev/sda5的父分区）

命令是：

```jsx
growpart /dev/sda 2
growpart /dev/sda 5
resize2fs /dev/sda5
```

如果没有growpart，先安装：

```jsx
sudo apt-get install cloud-utils 
```

过程：可以看到/大小变大了

```jsx
~# growpart /dev/sda 2
CHANGED: partition=2 start=1052670 old: size=15722498 end=16775168 new: size=62215137 end=63267807
root@mininet-vm:~# growpart /dev/sda 5
CHANGED: partition=5 start=1052672 old: size=15722496 end=16775168 new: size=62215135 end=63267807
root@mininet-vm:~# resize2fs /dev/sda5
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/sda5 is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 4
The filesystem on /dev/sda5 is now 7776891 (4k) blocks long.

root@mininet-vm:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           797M  696K  796M   1% /run
/dev/sda5        30G  4.6G   24G  17% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/sda1       511M  4.0K  511M   1% /boot/efi
/dev/sdb1        11G   48M  9.5G   1% /work
tmpfs           797M     0  797M   0% /run/user/1000

# fdisk -l
Disk /dev/sda: 30.17 GiB, 32393134080 bytes, 63267840 sectors
Disk model: HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x54b142dc

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1050623  1048576  512M  b W95 FAT32
/dev/sda2       1052670 63267806 62215137 29.7G  5 Extended
/dev/sda5       1052672 63267806 62215135 29.7G 83 Linux
```



另外的手动扩容方法容易出问题，比如[https://hellogitlab.com/OS/Centos/X_VirtualBox_enhance_hdds#查看当前lvm信息](https://hellogitlab.com/OS/Centos/X_VirtualBox_enhance_hdds#%E6%9F%A5%E7%9C%8B%E5%BD%93%E5%89%8Dlvm%E4%BF%A1%E6%81%AF) 
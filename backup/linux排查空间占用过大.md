首先`df -h`看到/下占用基本满了，但不知道大文件在哪

```bash
root@caijiachen:~# df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              3.2G  1.4M  3.2G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   96G   92G     0 100% /
tmpfs                               16G     0   16G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  243M  1.6G  14% /boot
/dev/sda1                          1.1G  6.1M  1.1G   1% /boot/efi
tmpfs                              3.2G  4.0K  3.2G   1% /run/user/1000
overlay                             96G   92G     0 100% /var/lib/docker/overlay2/badb290f0c550b2ca8a7481aa23c332eb489e34729530984f20d501a0c7399c3/merged
```
到/下，然后用`du -h --max-depth=1`看到48G都在 ./vmagent-remotewrite-data，就知道原来是vmagent持久化的数据太大了
```bash
root@caijiachen:/# du -h --max-depth=1
4.0K    ./mnt
1.5M    ./run
1.3G    ./snap
18G     ./var
8.0K    ./srv
5.2G    ./usr
16K     ./lost+found
du: cannot access './proc/3586560/task/3586560/fd/4': No such file or directory
du: cannot access './proc/3586560/task/3586560/fdinfo/4': No such file or directory
du: cannot access './proc/3586560/fd/3': No such file or directory
du: cannot access './proc/3586560/fdinfo/3': No such file or directory
0       ./proc
0       ./dev
0       ./sys
910M    ./opt
92M     ./tmp
6.7M    ./etc
4.0K    ./work
48G     ./vmagent-remotewrite-data
249M    ./boot
4.6G    ./root
14G     ./home
143M    ./data
32K     ./test
4.0K    ./media
93G     .
```
top查看vmagent-prod一直在跑，当时为了测试用的，直接systemctl把vmagent停掉，再把./vmagent-remotewrite-data删完就可以了。
```bash
top - 10:43:30 up 145 days, 25 min, 12 users,  load average: 0.41, 0.24, 0.19
Tasks: 364 total,   1 running, 363 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  32137.8 total,  15424.3 free,   4149.4 used,  12564.1 buff/cache
MiB Swap:   2048.0 total,   1571.5 free,    476.5 used.  27511.2 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                     
 592944 test      20   0   14.2g   3.5g 532396 S   6.6  11.3  10574:53 ld-linux-x86-64                                             
   1222 root      20   0 1246516  30648   7972 S   1.0   0.1 548:18.82 vmagent-prod
```
删完立刻空了：
```bash
root@caijiachen:~# df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              3.2G  1.4M  3.2G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   96G   44G   48G  49% /
tmpfs                               16G     0   16G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  243M  1.6G  14% /boot
/dev/sda1                          1.1G  6.1M  1.1G   1% /boot/efi
tmpfs                              3.2G  4.0K  3.2G   1% /run/user/1000
overlay                             96G   44G   48G  49% /var/lib/docker/overlay2/badb290f0c550b2ca8a7481aa23c332eb489e34729530984f20d501a0c7399c3/merged
```

ref: https://www.cnblogs.com/kirito-c/p/11170320.html
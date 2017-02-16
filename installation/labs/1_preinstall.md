[ec2-user@ip-172-31-17-228 ~]$ sudo  sysctl vm.swappiness=1

vm.swappiness = 1

df -k

Filesystem     1K-blocks   Used Available Use% Mounted on

/dev/xvda2      10473452 964104   9509348  10% /

devtmpfs         7599332      0   7599332   0% /dev

tmpfs            7486540      0   7486540   0% /dev/shm

tmpfs            7486540  16664   7469876   1% /run

tmpfs            7486540      0   7486540   0% /sys/fs/cgroup

tmpfs            1497312      0   1497312   0% /run/user/1000

tmpfs            1497312      0   1497312   0% /run/user/0

[ec2-user@ip-172-31-17-228 ~]$ mount

sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime,seclabel)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
devtmpfs on /dev type devtmpfs (rw,nosuid,seclabel,size=7599332k,nr_inodes=1899833,mode=755)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,seclabel)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,seclabel,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,seclabel,mode=755)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,seclabel,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/net_cls type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
configfs on /sys/kernel/config type configfs (rw,relatime)
/dev/xvda2 on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
selinuxfs on /sys/fs/selinux type selinuxfs (rw,relatime)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=30,pgrp=1,timeout=300,minproto=5,maxproto=5,direct)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,seclabel)
mqueue on /dev/mqueue type mqueue (rw,relatime,seclabel)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=1497312k,mode=700,uid=1000,gid=1000)
tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=1497312k,mode=700)


[ec2-user@ip-172-31-17-228 ~]$ sudo /sbin/tune2fs -l /dev/xvda2

tune2fs 1.42.9 (28-Dec-2013)
/sbin/tune2fs: Bad magic number in super-block while trying to open /dev/xvda2
Couldn't find valid filesystem superblock.

DISABLE TRANSPARENT HUGEPAGE

[ec2-user@ip-172-31-17-228 ~]$ egrep 'trans|thp' /proc/vmstat

nr_anon_transparent_hugepages 3
thp_fault_alloc 26
thp_fault_fallback 0
thp_collapse_alloc 1
thp_collapse_alloc_failed 0
thp_split 0
thp_zero_page_alloc 0
thp_zero_page_alloc_failed 0

sudo vi /etc/sysconfig/grub
GRUB_CMDLINE_LINUX="transparent_hugepage=never"

[ec2-user@ip-172-31-17-228 ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg

Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-327.10.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-327.10.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-e8f19fd1b8f1495db0b922f17f8112eb
Found initrd image: /boot/initramfs-0-rescue-e8f19fd1b8f1495db0b922f17f8112eb.img
done

[ec2-user@ip-172-31-17-228 ~]$ sudo reboot


[ec2-user@ip-172-31-17-228 ~]$ cat /proc/cmdline

BOOT_IMAGE=/boot/vmlinuz-3.10.0-327.10.1.el7.x86_64 root=UUID=65722bd1-fccc-453e-a96a-8f3599aa0466 ro tranparent_hugepage=never

[ec2-user@ip-172-31-17-228 ~]$ egrep 'trans|thp' /proc/vmstat

nr_anon_transparent_hugepages 0
thp_fault_alloc 0
thp_fault_fallback 0
thp_collapse_alloc 0
thp_collapse_alloc_failed 0
thp_split 0
thp_zero_page_alloc 0
thp_zero_page_alloc_failed 0

[ec2-user@ip-172-31-17-228 ~]$ ifconfig

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.17.228  netmask 255.255.240.0  broadcast 172.31.31.255
        inet6 fe80::8c:d1ff:fed0:1889  prefixlen 64  scopeid 0x20<link>
        ether 02:8c:d1:d0:18:89  txqueuelen 1000  (Ethernet)
        RX packets 422  bytes 41830 (40.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 415  bytes 52060 (50.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 4  bytes 340 (340.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4  bytes 340 (340.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
       
[ec2-user@ip-172-31-17-228 ~]$ getent networks

default               0.0.0.0
loopback              127.0.0.0
link-local            169.254.0.0

[ec2-user@ip-172-31-17-228 ~]$ getent hosts cloudera.com yahoo.com cnn.com

52.52.119.122   cloudera.com
52.52.13.233    cloudera.com
2001:4998:44:204::a7 yahoo.com
2001:4998:58:c02::a9 yahoo.com
2001:4998:c:a06::2:4008 yahoo.com
151.101.64.73   cnn.com
151.101.128.73  cnn.com
151.101.192.73  cnn.com
151.101.0.73    cnn.com

INSTALLING AND STARTING NSCD and NTPD

[ec2-user@ip-172-31-17-228 ~]$ sudo yum install nscd

Installed:
  nscd.x86_64 0:2.17-157.el7_3.1

Dependency Updated:
  glibc.x86_64 0:2.17-157.el7_3.1     glibc-common.x86_64 0:2.17-157.el7_3.1

Complete!

[ec2-user@ip-172-31-17-228 ~]$ sudo yum install ntp

[ec2-user@ip-172-31-17-228 ~]$ sudo systemctl start nscd

[ec2-user@ip-172-31-17-228 ~]$ sudo systemctl start ntpd









        
        








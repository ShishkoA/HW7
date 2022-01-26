## Repositories and Packages

- Use rpm for the following tasks:
1. Download sysstat package.
```bash
yum install yum-utils
yumdownloader sysstat
```
2. Get information from downloaded sysstat package file.
```bash
rpm -qip sysstat-10.1.5-19.el7.x86_64.rpm
```
3. Install sysstat package and get information about files installed by this package.
```bash
rpm -ivh sysstat-10.1.5-19.el7.x86_64.rpm --nodeps
repoquery --installed -l sysstat
```
- Add NGINX repository (need to find repository config on https://www.nginx.com/) and complete the following tasks using yum:
1. Check if NGINX repository enabled or not.
```bash
repoquery -i nginx
```
2. Install NGINX.
```bash
nano /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=0
enabled=1
yum install nginx
```
3. Check yum history and undo NGINX installation.
```bash
[root@localhost linux]# yum history
Loaded plugins: fastestmirror
ID     | Login user               | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     7 | linux <linux>            | 2021-12-22 18:16 | Install        |    1 E<
     6 | linux <linux>            | 2021-12-22 13:56 | Install        |    4 >
     5 | root <root>              | 2021-12-08 22:36 | Install        |    1
     4 | linux <linux>            | 2021-12-02 20:49 | Install        |    1
     3 | linux <linux>            | 2021-11-25 18:37 | Install        |    1
     2 | root <root>              | 2021-11-25 17:41 | I, U           |   99
     1 | System <unset>           | 2021-11-25 10:35 | Install        |  299
history list
yum history undo 7
```
4. Disable NGINX repository.
```bash
[root@localhost linux]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.docker.ru
 * extras: mirror.docker.ru
 * updates: mirror.docker.ru
repo id                                                                    repo name                                                                    status
base/7/x86_64                                                              CentOS-7 - Base                                                              10,072
extras/7/x86_64                                                            CentOS-7 - Extras                                                               500
nginx/x86_64                                                               nginx repo                                                                      875
updates/7/x86_64                                                           CentOS-7 - Updates                                                            3,242
repolist: 14,689
[root@localhost linux]# yum --disablerepo=nginx/x86_64 update
Loaded plugins: fastestmirror


Error getting repository data for nginx/x86_64, repository not found
[root@localhost linux]# nano /etc/yum.repos.d/nginx.repo
[root@localhost linux]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.docker.ru
 * extras: mirror.docker.ru
 * updates: mirror.docker.ru
repo id                                                                    repo name                                                                    status
base/7/x86_64                                                              CentOS-7 - Base                                                              10,072
extras/7/x86_64                                                            CentOS-7 - Extras                                                               500
updates/7/x86_64                                                           CentOS-7 - Updates                                                            3,242
repolist: 13,814
[root@localhost linux]#
```
5. Remove sysstat package installed in the first task.
```bash
yum remove sysstat
```
6. Install EPEL repository and get information about it.
```bash
yum -y install epel-release
yum repoinfo epel
```
7. Find how much packages provided exactly by EPEL repository.
```bash
yum repoinfo epel | grep Repo-pkgs 
Repo-pkgs    : 13,699
```
8. Install ncdu package from EPEL repo.
```bash
yum --enablerepo=epel install ncdu
```
*Extra task:
    Need to create an rpm package consists of a shell script and a text file. The script should output words count stored in file.

## Work with files

1. Find all regular files below 100 bytes inside your home directory.
```bash
[root@localhost linux]# find ~ -type f -size -100c
/root/.bash_logout
/root/.lesshst
```
2. Find an inode number and a hard links count for the root directory. The hard link count should be about 17. Why?
```bash
[root@localhost linux]# stat /
  File: ‘/’
  Size: 237             Blocks: 0          IO Block: 4096   directory
Device: fd00h/64768d    Inode: 64          Links: 18
Access: (0555/dr-xr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:root_t:s0
Access: 2021-12-22 11:50:54.980000054 +0300
Modify: 2021-12-17 14:20:52.702050507 +0300
Change: 2021-12-17 14:20:52.702050507 +0300
 Birth: -
 Because (root)/ has 17 subfolders. In my case 18.
```
3. Check what inode numbers have "/" and "/boot" directory. Why?
```bash
[root@localhost linux]# ls -id /
64 /
[root@localhost linux]# ls -id /boot
64 /boot
Because inode numbers are only unique within a filesystem. So those two are on separate devices/filesystems
```
4. Check the root directory space usage by du command. Compare it with an information from df. If you find differences, try to find out why it happens.
```bash
[root@localhost linux]# cd /
[root@localhost /]# du -sh
du: cannot access ‘./proc/2648/task/2648/fd/4’: No such file or directory
du: cannot access ‘./proc/2648/task/2648/fdinfo/4’: No such file or directory
du: cannot access ‘./proc/2648/fd/3’: No such file or directory
du: cannot access ‘./proc/2648/fdinfo/3’: No such file or directory
2.4G    .
[root@localhost /]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 899M     0  899M   0% /dev
tmpfs                    910M     0  910M   0% /dev/shm
tmpfs                    910M  9.5M  901M   2% /run
tmpfs                    910M     0  910M   0% /sys/fs/cgroup
/dev/mapper/centos-root   17G  2.2G   15G  13% /
/dev/sda1               1014M  194M  821M  20% /boot
tmpfs                    182M     0  182M   0% /run/user/1000
Because du shows us size in folder with virtual fs(processes). df shows us only root filesystem size
```
5. Check disk space usage of /var/log directory using ncdu
```bash
ncdu /var/log
```

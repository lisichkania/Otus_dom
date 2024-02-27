## Cоздать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере
### Далее создать инстанс виртуальной машины с дефолтными параметрами. Добавить свой ssh ключ в metadata ВМ
```
lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ cd ~   
lisichkania@LAPTOP-JVR9DUC8:~$ cd .ssh
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ ssh-keygen -t rsa -b 2048  
Generating public/private rsa key pair.  
Enter file in which to save the key (/home/lisichkania/.ssh/id_rsa): yc_key  
Enter passphrase (empty for no passphrase):  
Enter same passphrase again:  
Your identification has been saved in yc_key  
Your public key has been saved in yc_key.pub  
The key fingerprint is:  
SHA256:SNfZ2lGLJ3M7LGugIbpZwoKWK1dKu1HaEENrC0z3ZLY lisichkania@LAPTOP-JVR9DUC8  
The key's randomart image is:  
+---[RSA 2048]----+
| ... +        .  |
|o...= .  . o o . |
|..=  E. . o * +  |
| o + . o   o B . |
|  o . o S o o +  |
| .o*.. . o . o . |
|.+++= . .   o    |
|o =o =     .     |
|.o..o            |
+----[SHA256]-----+  
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ ls -l  
total 12  
-rw------- 1 lisichkania lisichkania    0 Feb 22 11:53 authorized_keys  
-rw-r--r-- 1 lisichkania lisichkania  142 Feb 22 12:04 known_hosts  
-rw------- 1 lisichkania lisichkania 1831 Feb 22 12:24 yc_key  
-rw-r--r-- 1 lisichkania lisichkania  409 Feb 22 12:24 yc_key.pub  
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ cat yc_key.pub  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6+aJZdBOz5WsVCYgqCnUiXksidbzQoRI4MG08MYZS0rQy3mBJtqSf4xakUNaHO83rzwrsBcygcSExIC8bT47ZV1ey65tytj0lEcUUSXcc1W9xkG5Dv56Oua5+vXF1SIzItYQHW4GcULuiXaFGEIHkrGdjeJ7eGYcoKtxM5gllILwgItYrnpZPWKCzNyuUxey7DC8VB9qN//7ie+3+iZi3wvA3EGG/2+Ndq5N2J2Q0jO/dIcX1/KQ6qnIKR0vP6IFaleHzqwh8MsN3jW3CLSkV7umoZuDPeGReA1EPnamJkIh+wDZbp8G2cawDiqvPHEAmQqq0DFRjEc+UDcdahXmv lisichkania@LAPTOP-JVR9DUC8  
```
### Зайти удаленным ssh (первая сессия), не забывайте про ssh-add
```
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ ssh -i ~/.ssh/yc_key otus@158.160.67.175
The authenticity of host '158.160.67.175 (158.160.67.175)' can't be established.
ED25519 key fingerprint is SHA256:E9u+W3nugnmD8wSilhsfs8n4jCGy6rd6qyVzu6Z+nus.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '158.160.67.175' (ED25519) to the list of known hosts.
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-171-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.
```
### Поставить PostgreSQL
*Установила 15 версию, потом 16, потом 16-ю удалила и обновила 15-ю до 16-й как на лекции*
```otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu focal InRelease
Get:2 http://mirror.yandex.ru/ubuntu focal-updates InRelease [114 kB]
Hit:3 http://mirror.yandex.ru/ubuntu focal-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:5 http://mirror.yandex.ru/ubuntu focal-updates/main i386 Packages [935 kB]
Get:6 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 Packages [3,109 kB]
Get:7 http://mirror.yandex.ru/ubuntu focal-updates/main Translation-en [498 kB]
Get:8 http://mirror.yandex.ru/ubuntu focal-updates/restricted amd64 Packages [2,689 kB]
Get:9 http://mirror.yandex.ru/ubuntu focal-updates/restricted Translation-en [374 kB]
Get:10 http://mirror.yandex.ru/ubuntu focal-updates/universe amd64 Packages [1,165 kB]
Get:11 http://mirror.yandex.ru/ubuntu focal-updates/universe i386 Packages [772 kB]
Get:12 http://mirror.yandex.ru/ubuntu focal-updates/universe Translation-en [279 kB]
Get:13 http://mirror.yandex.ru/ubuntu focal-updates/multiverse i386 Packages [8,436 B]
Get:14 http://mirror.yandex.ru/ubuntu focal-updates/multiverse amd64 Packages [26.1 kB]
Get:15 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [2,729 kB]
Get:16 http://security.ubuntu.com/ubuntu focal-security/main i386 Packages [710 kB]
Get:17 http://security.ubuntu.com/ubuntu focal-security/main Translation-en [414 kB]
Get:18 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [2,572 kB]
Get:19 http://security.ubuntu.com/ubuntu focal-security/restricted Translation-en [359 kB]
Get:20 http://security.ubuntu.com/ubuntu focal-security/universe i386 Packages [645 kB]
Get:21 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [939 kB]
Get:22 http://security.ubuntu.com/ubuntu focal-security/universe Translation-en [198 kB]
Fetched 18.7 MB in 4s (5,027 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
6 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following NEW packages will be installed:
  linux-headers-5.4.0-172 linux-headers-5.4.0-172-generic linux-image-5.4.0-172-generic
  linux-modules-5.4.0-172-generic linux-modules-extra-5.4.0-172-generic
The following packages will be upgraded:
  bind9-dnsutils bind9-host bind9-libs linux-generic linux-headers-generic linux-image-generic
6 upgraded, 5 newly installed, 0 to remove and 0 not upgraded.
6 standard LTS security updates
Need to get 78.4 MB of archives.
After this operation, 380 MB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 bind9-dnsutils amd64 1:9.16.48-0ubuntu0.20.04.1 [136 kB]
Get:2 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 bind9-libs amd64 1:9.16.48-0ubuntu0.20.04.1 [1,145 kB]
Get:3 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 bind9-host amd64 1:9.16.48-0ubuntu0.20.04.1 [43.9 kB]
Get:4 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-modules-5.4.0-172-generic amd64 5.4.0-172.190 [15.0 MB]
Get:5 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-image-5.4.0-172-generic amd64 5.4.0-172.190 [10.5 MB]
Get:6 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-modules-extra-5.4.0-172-generic amd64 5.4.0-172.190 [39.2 MB]
Get:7 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-generic amd64 5.4.0.172.170 [1,900 B]
Get:8 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-image-generic amd64 5.4.0.172.170 [2,540 B]
Get:9 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-headers-5.4.0-172 all 5.4.0-172.190 [11.0 MB]
Get:10 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-headers-5.4.0-172-generic amd64 5.4.0-172.190 [1,381 kB]
Get:11 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 linux-headers-generic amd64 5.4.0.172.170 [2,412 B]
Fetched 78.4 MB in 9s (8,679 kB/s)
(Reading database ... 102638 files and directories currently installed.)
Preparing to unpack .../00-bind9-dnsutils_1%3a9.16.48-0ubuntu0.20.04.1_amd64.deb ...
Unpacking bind9-dnsutils (1:9.16.48-0ubuntu0.20.04.1) over (1:9.16.1-0ubuntu2.16) ...
Preparing to unpack .../01-bind9-libs_1%3a9.16.48-0ubuntu0.20.04.1_amd64.deb ...
Unpacking bind9-libs:amd64 (1:9.16.48-0ubuntu0.20.04.1) over (1:9.16.1-0ubuntu2.16) ...
Preparing to unpack .../02-bind9-host_1%3a9.16.48-0ubuntu0.20.04.1_amd64.deb ...
Unpacking bind9-host (1:9.16.48-0ubuntu0.20.04.1) over (1:9.16.1-0ubuntu2.16) ...
Selecting previously unselected package linux-modules-5.4.0-172-generic.
Preparing to unpack .../03-linux-modules-5.4.0-172-generic_5.4.0-172.190_amd64.deb ...
Unpacking linux-modules-5.4.0-172-generic (5.4.0-172.190) ...
Selecting previously unselected package linux-image-5.4.0-172-generic.
Preparing to unpack .../04-linux-image-5.4.0-172-generic_5.4.0-172.190_amd64.deb ...
Unpacking linux-image-5.4.0-172-generic (5.4.0-172.190) ...
Selecting previously unselected package linux-modules-extra-5.4.0-172-generic.
Preparing to unpack .../05-linux-modules-extra-5.4.0-172-generic_5.4.0-172.190_amd64.deb ...
Unpacking linux-modules-extra-5.4.0-172-generic (5.4.0-172.190) ...
Preparing to unpack .../06-linux-generic_5.4.0.172.170_amd64.deb ...
Unpacking linux-generic (5.4.0.172.170) over (5.4.0.171.169) ...
Preparing to unpack .../07-linux-image-generic_5.4.0.172.170_amd64.deb ...
Unpacking linux-image-generic (5.4.0.172.170) over (5.4.0.171.169) ...
Selecting previously unselected package linux-headers-5.4.0-172.
Preparing to unpack .../08-linux-headers-5.4.0-172_5.4.0-172.190_all.deb ...
Unpacking linux-headers-5.4.0-172 (5.4.0-172.190) ...
Selecting previously unselected package linux-headers-5.4.0-172-generic.
Preparing to unpack .../09-linux-headers-5.4.0-172-generic_5.4.0-172.190_amd64.deb ...
Unpacking linux-headers-5.4.0-172-generic (5.4.0-172.190) ...
Preparing to unpack .../10-linux-headers-generic_5.4.0.172.170_amd64.deb ...
Unpacking linux-headers-generic (5.4.0.172.170) over (5.4.0.171.169) ...
Setting up bind9-libs:amd64 (1:9.16.48-0ubuntu0.20.04.1) ...
Setting up linux-headers-5.4.0-172 (5.4.0-172.190) ...
Setting up linux-modules-5.4.0-172-generic (5.4.0-172.190) ...
Setting up bind9-host (1:9.16.48-0ubuntu0.20.04.1) ...
Setting up linux-headers-5.4.0-172-generic (5.4.0-172.190) ...
Setting up linux-headers-generic (5.4.0.172.170) ...
Setting up linux-image-5.4.0-172-generic (5.4.0-172.190) ...
I: /boot/vmlinuz.old is now a symlink to vmlinuz-5.4.0-171-generic
I: /boot/initrd.img.old is now a symlink to initrd.img-5.4.0-171-generic
I: /boot/vmlinuz is now a symlink to vmlinuz-5.4.0-172-generic
I: /boot/initrd.img is now a symlink to initrd.img-5.4.0-172-generic
Setting up bind9-dnsutils (1:9.16.48-0ubuntu0.20.04.1) ...
Setting up linux-modules-extra-5.4.0-172-generic (5.4.0-172.190) ...
Setting up linux-image-generic (5.4.0.172.170) ...
Setting up linux-generic (5.4.0.172.170) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.14) ...
Processing triggers for linux-image-5.4.0-172-generic (5.4.0-172.190) ...
/etc/kernel/postinst.d/initramfs-tools:
update-initramfs: Generating /boot/initrd.img-5.4.0-172-generic
/etc/kernel/postinst.d/zz-update-grub:
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.4.0-172-generic
Found initrd image: /boot/initrd.img-5.4.0-172-generic
Found linux image: /boot/vmlinuz-5.4.0-171-generic
Found initrd image: /boot/initrd.img-5.4.0-171-generic
Found linux image: /boot/vmlinuz-5.4.0-42-generic
Found initrd image: /boot/initrd.img-5.4.0-42-generic
done
OK
Hit:1 http://mirror.yandex.ru/ubuntu focal InRelease
Hit:2 http://mirror.yandex.ru/ubuntu focal-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu focal-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu focal-security InRelease
Get:5 http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease [123 kB]
Get:6 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 Packages [293 kB]
Fetched 416 kB in 1s (485 kB/s)
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease' doesn't support architecture 'i386'
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-headers-5.4.0-42 linux-headers-5.4.0-42-generic linux-image-5.4.0-42-generic linux-modules-5.4.0-42-generic
  linux-modules-extra-5.4.0-42-generic
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  libcommon-sense-perl libgdbm-compat4 libjson-perl libjson-xs-perl libllvm10 libperl5.30 libpq5 libsensors-config
  libsensors5 libtypes-serialiser-perl libxslt1.1 perl perl-modules-5.30 postgresql-client-15 postgresql-client-common
  postgresql-common ssl-cert sysstat
Suggested packages:
  lm-sensors perl-doc libterm-readline-gnu-perl | libterm-readline-perl-perl make libb-debug-perl liblocale-codes-perl
  postgresql-doc-15 openssl-blacklist isag
The following NEW packages will be installed:
  libcommon-sense-perl libgdbm-compat4 libjson-perl libjson-xs-perl libllvm10 libperl5.30 libpq5 libsensors-config
  libsensors5 libtypes-serialiser-perl libxslt1.1 perl perl-modules-5.30 postgresql-15 postgresql-client-15
  postgresql-client-common postgresql-common ssl-cert sysstat
0 upgraded, 19 newly installed, 0 to remove and 0 not upgraded.
Need to get 41.8 MB of archives.
After this operation, 187 MB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 perl-modules-5.30 all 5.30.0-9ubuntu0.5 [2,739 kB]
Get:2 http://mirror.yandex.ru/ubuntu focal/main amd64 libgdbm-compat4 amd64 1.18.1-5 [6,244 B]
Get:3 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 libperl5.30 amd64 5.30.0-9ubuntu0.5 [3,941 kB]
Get:4 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-client-common all 257.pgdg20.04+1 [94.3 kB]
Get:5 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 perl amd64 5.30.0-9ubuntu0.5 [224 kB]
Get:6 http://mirror.yandex.ru/ubuntu focal/main amd64 libjson-perl all 4.02000-2 [80.9 kB]
Get:7 http://mirror.yandex.ru/ubuntu focal/main amd64 ssl-cert all 1.0.39 [17.0 kB]
Get:8 http://mirror.yandex.ru/ubuntu focal/main amd64 libcommon-sense-perl amd64 3.74-2build6 [20.1 kB]
Get:9 http://mirror.yandex.ru/ubuntu focal/main amd64 libtypes-serialiser-perl all 1.0-1 [12.1 kB]
Get:10 http://mirror.yandex.ru/ubuntu focal/main amd64 libjson-xs-perl amd64 4.020-1build1 [83.7 kB]
Get:11 http://mirror.yandex.ru/ubuntu focal/main amd64 libllvm10 amd64 1:10.0.0-4ubuntu1 [15.3 MB]
Get:12 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-common all 257.pgdg20.04+1 [239 kB]
Get:13 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 libpq5 amd64 16.2-1.pgdg20.04+1 [214 kB]
Get:14 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-client-15 amd64 15.6-1.pgdg20.04+1 [1,688 kB]
Get:15 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-15 amd64 15.6-1.pgdg20.04+1 [16.5 MB]
Get:16 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 libsensors-config all 1:3.6.0-2ubuntu1.1 [6,052 B]
Get:17 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 libsensors5 amd64 1:3.6.0-2ubuntu1.1 [27.2 kB]
Get:18 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 libxslt1.1 amd64 1.1.34-4ubuntu0.20.04.1 [151 kB]
Get:19 http://mirror.yandex.ru/ubuntu focal-updates/main amd64 sysstat amd64 12.2.0-2ubuntu0.3 [448 kB]
Fetched 41.8 MB in 2s (21.2 MB/s)
Preconfiguring packages ...
Selecting previously unselected package perl-modules-5.30.
(Reading database ... 139032 files and directories currently installed.)
Preparing to unpack .../00-perl-modules-5.30_5.30.0-9ubuntu0.5_all.deb ...
Unpacking perl-modules-5.30 (5.30.0-9ubuntu0.5) ...
Selecting previously unselected package libgdbm-compat4:amd64.
Preparing to unpack .../01-libgdbm-compat4_1.18.1-5_amd64.deb ...
Unpacking libgdbm-compat4:amd64 (1.18.1-5) ...
Selecting previously unselected package libperl5.30:amd64.
Preparing to unpack .../02-libperl5.30_5.30.0-9ubuntu0.5_amd64.deb ...
Unpacking libperl5.30:amd64 (5.30.0-9ubuntu0.5) ...
Selecting previously unselected package perl.
Preparing to unpack .../03-perl_5.30.0-9ubuntu0.5_amd64.deb ...
Unpacking perl (5.30.0-9ubuntu0.5) ...
Selecting previously unselected package libjson-perl.
Preparing to unpack .../04-libjson-perl_4.02000-2_all.deb ...
Unpacking libjson-perl (4.02000-2) ...
Selecting previously unselected package postgresql-client-common.
Preparing to unpack .../05-postgresql-client-common_257.pgdg20.04+1_all.deb ...
Unpacking postgresql-client-common (257.pgdg20.04+1) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../06-ssl-cert_1.0.39_all.deb ...
Unpacking ssl-cert (1.0.39) ...
Selecting previously unselected package postgresql-common.
Preparing to unpack .../07-postgresql-common_257.pgdg20.04+1_all.deb ...
Adding 'diversion of /usr/bin/pg_config to /usr/bin/pg_config.libpq-dev by postgresql-common'
Unpacking postgresql-common (257.pgdg20.04+1) ...
Selecting previously unselected package libcommon-sense-perl.
Preparing to unpack .../08-libcommon-sense-perl_3.74-2build6_amd64.deb ...
Unpacking libcommon-sense-perl (3.74-2build6) ...
Selecting previously unselected package libtypes-serialiser-perl.
Preparing to unpack .../09-libtypes-serialiser-perl_1.0-1_all.deb ...
Unpacking libtypes-serialiser-perl (1.0-1) ...
Selecting previously unselected package libjson-xs-perl.
Preparing to unpack .../10-libjson-xs-perl_4.020-1build1_amd64.deb ...
Unpacking libjson-xs-perl (4.020-1build1) ...
Selecting previously unselected package libllvm10:amd64.
Preparing to unpack .../11-libllvm10_1%3a10.0.0-4ubuntu1_amd64.deb ...
Unpacking libllvm10:amd64 (1:10.0.0-4ubuntu1) ...
Selecting previously unselected package libpq5:amd64.
Preparing to unpack .../12-libpq5_16.2-1.pgdg20.04+1_amd64.deb ...
Unpacking libpq5:amd64 (16.2-1.pgdg20.04+1) ...
Selecting previously unselected package libsensors-config.
Preparing to unpack .../13-libsensors-config_1%3a3.6.0-2ubuntu1.1_all.deb ...
Unpacking libsensors-config (1:3.6.0-2ubuntu1.1) ...
Selecting previously unselected package libsensors5:amd64.
Preparing to unpack .../14-libsensors5_1%3a3.6.0-2ubuntu1.1_amd64.deb ...
Unpacking libsensors5:amd64 (1:3.6.0-2ubuntu1.1) ...
Selecting previously unselected package libxslt1.1:amd64.
Preparing to unpack .../15-libxslt1.1_1.1.34-4ubuntu0.20.04.1_amd64.deb ...
Unpacking libxslt1.1:amd64 (1.1.34-4ubuntu0.20.04.1) ...
Selecting previously unselected package postgresql-client-15.
Preparing to unpack .../16-postgresql-client-15_15.6-1.pgdg20.04+1_amd64.deb ...
Unpacking postgresql-client-15 (15.6-1.pgdg20.04+1) ...
Selecting previously unselected package postgresql-15.
Preparing to unpack .../17-postgresql-15_15.6-1.pgdg20.04+1_amd64.deb ...
Unpacking postgresql-15 (15.6-1.pgdg20.04+1) ...
Selecting previously unselected package sysstat.
Preparing to unpack .../18-sysstat_12.2.0-2ubuntu0.3_amd64.deb ...
Unpacking sysstat (12.2.0-2ubuntu0.3) ...
Setting up perl-modules-5.30 (5.30.0-9ubuntu0.5) ...
Setting up libsensors-config (1:3.6.0-2ubuntu1.1) ...
Setting up libpq5:amd64 (16.2-1.pgdg20.04+1) ...
Setting up libllvm10:amd64 (1:10.0.0-4ubuntu1) ...
Setting up ssl-cert (1.0.39) ...
Setting up libgdbm-compat4:amd64 (1.18.1-5) ...
Setting up libsensors5:amd64 (1:3.6.0-2ubuntu1.1) ...
Setting up libxslt1.1:amd64 (1.1.34-4ubuntu0.20.04.1) ...
Setting up libperl5.30:amd64 (5.30.0-9ubuntu0.5) ...
Setting up sysstat (12.2.0-2ubuntu0.3) ...

Creating config file /etc/default/sysstat with new version
update-alternatives: using /usr/bin/sar.sysstat to provide /usr/bin/sar (sar) in auto mode
Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sysstat.service.
Setting up perl (5.30.0-9ubuntu0.5) ...
Setting up libjson-perl (4.02000-2) ...
Setting up postgresql-client-common (257.pgdg20.04+1) ...
Setting up libcommon-sense-perl (3.74-2build6) ...
Setting up postgresql-client-15 (15.6-1.pgdg20.04+1) ...
update-alternatives: using /usr/share/postgresql/15/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up postgresql-common (257.pgdg20.04+1) ...
Adding user postgres to group ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
Removing obsolete dictionary files:
'/etc/apt/trusted.gpg.d/apt.postgresql.org.gpg' -> '/usr/share/postgresql-common/pgdg/apt.postgresql.org.gpg'
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
Setting up libtypes-serialiser-perl (1.0-1) ...
Setting up postgresql-15 (15.6-1.pgdg20.04+1) ...
Creating new PostgreSQL cluster 15/main ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main --auth-local peer --auth-host scram-sha-256 --no-instru
ctions
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/15/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Setting up libjson-xs-perl (4.020-1build1) ...
Processing triggers for systemd (245.4-4ubuntu3.23) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.14) ...
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
psql (15.6 (Ubuntu 15.6-1.pgdg20.04+1))
Type "help" for help.

postgres=# \l
                                                 List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
(3 rows)

postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-16
Hit:1 http://mirror.yandex.ru/ubuntu focal InRelease
Hit:2 http://mirror.yandex.ru/ubuntu focal-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu focal-backports InRelease
Hit:4 http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease
Hit:5 http://security.ubuntu.com/ubuntu focal-security InRelease
Reading package lists... Done
Building dependency tree
Reading state information... Done
All packages are up to date.
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease' doesn't support architecture 'i386'
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following packages were automatically installed and are no longer required:
  linux-headers-5.4.0-42 linux-headers-5.4.0-42-generic linux-image-5.4.0-42-generic linux-modules-5.4.0-42-generic
  linux-modules-extra-5.4.0-42-generic
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
OK
Hit:1 http://mirror.yandex.ru/ubuntu focal InRelease
Hit:2 http://mirror.yandex.ru/ubuntu focal-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu focal-backports InRelease
Hit:4 http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease
Hit:5 http://security.ubuntu.com/ubuntu focal-security InRelease
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'http://apt.postgresql.org/pub/repos/apt focal-pgdg InRelease' doesn't support architecture 'i386'
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-headers-5.4.0-42 linux-headers-5.4.0-42-generic linux-image-5.4.0-42-generic linux-modules-5.4.0-42-generic
  linux-modules-extra-5.4.0-42-generic
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  postgresql-client-16
Suggested packages:
  postgresql-doc-16
The following NEW packages will be installed:
  postgresql-16 postgresql-client-16
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 19.4 MB of archives.
After this operation, 68.6 MB of additional disk space will be used.
Get:1 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-client-16 amd64 16.2-1.pgdg20.04+1 [1,891 kB]
Get:2 http://apt.postgresql.org/pub/repos/apt focal-pgdg/main amd64 postgresql-16 amd64 16.2-1.pgdg20.04+1 [17.5 MB]
Fetched 19.4 MB in 1s (20.9 MB/s)
Preconfiguring packages ...
Selecting previously unselected package postgresql-client-16.
(Reading database ... 143285 files and directories currently installed.)
Preparing to unpack .../postgresql-client-16_16.2-1.pgdg20.04+1_amd64.deb ...
Unpacking postgresql-client-16 (16.2-1.pgdg20.04+1) ...
Selecting previously unselected package postgresql-16.
Preparing to unpack .../postgresql-16_16.2-1.pgdg20.04+1_amd64.deb ...
Unpacking postgresql-16 (16.2-1.pgdg20.04+1) ...
Setting up postgresql-client-16 (16.2-1.pgdg20.04+1) ...
update-alternatives: using /usr/share/postgresql/16/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up postgresql-16 (16.2-1.pgdg20.04+1) ...
Creating new PostgreSQL cluster 16/main ...
/usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/main --auth-local peer --auth-host scram-sha-256 --no-instru
ctions
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/16/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Processing triggers for postgresql-common (257.pgdg20.04+1) ...
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
Removing obsolete dictionary files:
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
16  main    5433 online postgres /var/lib/postgresql/16/main /var/log/postgresql/postgresql-16-main.log
otus@otus-db-pg-vm-1:~$ sudo postgres
sudo: postgres: command not found
otus@otus-db-pg-vm-1:~$ sudo -u postgres
usage: sudo -h | -K | -k | -V
usage: sudo -v [-AknS] [-g group] [-h host] [-p prompt] [-u user]
usage: sudo -l [-AknS] [-g group] [-h host] [-p prompt] [-U user] [-u user] [command]
usage: sudo [-AbEHknPS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p prompt] [-T timeout] [-u user]
            [VAR=value] [-i|-s] [<command>]
usage: sudo -e [-AknS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p prompt] [-T timeout] [-u user] file ...
otus@otus-db-pg-vm-1:~$ sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$ sudo pg_ctlcluster 12 main stop
[sudo] password for postgres:
Sorry, try again.
[sudo] password for postgres:
Sorry, try again.
[sudo] password for postgres:
sudo: 3 incorrect password attempts
postgres@otus-db-pg-vm-1:/home/otus$ sudo pg_ctlcluster 16 main stop
[sudo] password for postgres:
Sorry, try again.
[sudo] password for postgres:
Sorry, try again.
[sudo] password for postgres:
sudo: 3 incorrect password attempts
postgres@otus-db-pg-vm-1:/home/otus$ pg_ctlcluster 16 main stop
Warning: stopping the cluster using pg_ctlcluster will mark the systemd unit as failed. Consider using systemctl:
  sudo systemctl stop postgresql@16-main
postgres@otus-db-pg-vm-1:/home/otus$ pg_dropcluster 16 main
Warning: systemd was not informed about the removed cluster yet. Operations like "service postgresql start" might fail. To fix, run:
  sudo systemctl daemon-reload
postgres@otus-db-pg-vm-1:/home/otus$ pg_upgradecluster 15 main
Stopping old cluster...
Warning: stopping the cluster using pg_ctlcluster will mark the systemd unit as failed. Consider using systemctl:
  sudo systemctl stop postgresql@15-main
Restarting old cluster with restricted connections...
Notice: extra pg_ctl/postgres options given, bypassing systemctl for start operation
Creating new PostgreSQL cluster 16/main ...
/usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/main --auth-local peer --auth-host scram-sha-256 --no-instructions --encoding UTF8 --lc-collate en_US.UTF-8 --lc-ctype en_US.UTF-8 --locale-provider libc
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/16/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Warning: systemd does not know about the new cluster yet. Operations like "service postgresql start" will not handle it. To fix, run:
  sudo systemctl daemon-reload

Copying old configuration files...
Copying old start.conf...
Copying old pg_ctl.conf...
Starting new cluster...
Notice: extra pg_ctl/postgres options given, bypassing systemctl for start operation
Running init phase upgrade hook scripts ...

Roles, databases, schemas, ACLs...
 set_config
------------

(1 row)

 set_config
------------

(1 row)

Fixing hardcoded library paths for stored procedures...
Upgrading database template1...
Fixing hardcoded library paths for stored procedures...
Upgrading database postgres...
Stopping target cluster...
Stopping old cluster...
Disabling automatic startup of old cluster...
Starting upgraded cluster on port 5432...
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@16-main
Running finish phase upgrade hook scripts ...
vacuumdb: processing database "postgres": Generating minimal optimizer statistics (1 target)
vacuumdb: processing database "template1": Generating minimal optimizer statistics (1 target)
vacuumdb: processing database "postgres": Generating medium optimizer statistics (10 targets)
vacuumdb: processing database "template1": Generating medium optimizer statistics (10 targets)
vacuumdb: processing database "postgres": Generating default (full) optimizer statistics
vacuumdb: processing database "template1": Generating default (full) optimizer statistics

Success. Please check that the upgraded cluster works. If it does,
you can remove the old cluster with
    pg_dropcluster 15 main

Ver Cluster Port Status Owner    Data directory              Log file
15  main    5433 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
Ver Cluster Port Status Owner    Data directory              Log file
16  main    5432 online postgres /var/lib/postgresql/16/main /var/log/postgresql/postgresql-16-main.log
postgres@otus-db-pg-vm-1:/home/otus$ exit
exit
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5433 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
16  main    5432 online postgres /var/lib/postgresql/16/main /var/log/postgresql/postgresql-16-main.log
```
### зайти вторым ssh (вторая сессия). Запустить везде psql из под пользователя postgres
```
PS C:\Users\USER> bash
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ cd ~/.ssh
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ ssh -i ~/.ssh/yc_key otus@158.160.67.175
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-171-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
New release '22.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Thu Feb 22 09:28:45 2024 from 91.108.28.103
otus@otus-db-pg-vm-1:~$ sudo -u postgres
usage: sudo -h | -K | -k | -V
usage: sudo -v [-AknS] [-g group] [-h host] [-p prompt] [-u user]
usage: sudo -l [-AknS] [-g group] [-h host] [-p prompt] [-U user] [-u user] [command]
usage: sudo [-AbEHknPS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p prompt] [-T timeout] [-u user]
            [VAR=value] [-i|-s] [<command>]
usage: sudo -e [-AknS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p prompt] [-T timeout] [-u user] file ...
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql -d iso
psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  database "iso" does not exist
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
Type "help" for help.
```
*Создание базы данных iso в 1-м подключении*
```
postgres=# CREATE DATABASE iso;
DATABASECREATE DATABASE
postgres=# \c iso
You are now connected to database "iso" as user "postgres".
iso=# SELECT current_database();
 (i serial, amount int) current_database
------------------
 iso
(1 row)
```
*Подключение к созданной базе во втором подключении*
```
PS C:\Users\USER> bash
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ cd ~/.ssh
lisichkania@LAPTOP-JVR9DUC8:~/.ssh$ ssh -i ~/.ssh/yc_key otus@158.160.67.175
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-171-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
New release '22.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Thu Feb 22 09:52:32 2024 from 91.108.28.103
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql -d iso
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
Type "help" for help.
```
*Выключаем auto commit*
```
iso=# \echo :AUTOCOMMIT
on
iso=# \set AUTOCOMMIT OFF
```
*Создаем таблицу*
```
iso=# create table persons(id serial, first_name text, second_name text);
CREATE TABLE
iso=# insert into persons(first_name, second_name) values('ivan', 'ivanov');
INSERT 0 1
iso=# insert into persons(first_name, second_name) values('petr', 'petrov');
INSERT 0 1
iso=# commit;
WARNING:  there is no transaction in progress
COMMIT
iso=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
```
*Посмотреть текущий уровень изоляции: show transaction isolation level*
```
iso=# show transaction isolation level;
 transaction_isolation
-----------------------
 read committed
(1 row)
```
*в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');*
```
iso=# insert into persons(first_name, second_name) values('sergey', 'sergeev');
INSERT 0 1
```
*сделать select from persons во второй сессии*
```
iso=# select * from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
```
*видите ли вы новую запись и если да то почему?  
Новой записи нет, так как auto commit отключен, а commit в первой сессии мы не сделали, поэтому запись во второй транзакции не видна, а текущий уровень транзакции у нас read committed.  
завершить первую транзакцию - commit;*
```
iso=*# commit;
COMMIT
```
*сделать select from persons во второй сессии*
```
iso=# select * from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
*видите ли вы новую запись и если да то почему?  
В первой сессии сделали commit и во второй появилась строка  
завершите транзакцию во второй сессии*
```
iso=# commit;
WARNING:  there is no transaction in progress
COMMIT
```
*начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;*
```
iso=# BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN
```
*в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');*
```
iso=*# insert into persons(first_name, second_name) values('sveta', 'svetova');
INSERT 0 1
```
*сделать select* from persons во второй сессии*
```
iso=*# select* from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | darya      | durandina
  6 | denis      | durandin
(5 rows)
```
*Добавляла и другие строки, так как не отключила auto commit в самом начале и не поняла, почему вижу строку сразу после её добавления в первой транзакции, на 5-й делала rollback, поэтому её нет, потом все пошло как надо  
видите ли вы новую запись и если да то почему?  
Новую запись не вижу во второй транзакции так как commit в первой еще не сделан, а исходя из нового уровня транзакции нужно будет сделать commit и в первом и во втором подключении  
завершить первую транзакцию - commit;*
```
iso=*# commit;
COMMIT
```
*сделать select from persons во второй сессии*
```
iso=*# select* from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | darya      | durandina
  6 | denis      | durandin
(5 rows)
```
*видите ли вы новую запись и если да то почему?  
Новой записи во втором подключении нет, так как уровень изоляции предполагает сделать commit и в первом и во втором подключении  
завершить вторую транзакцию*
```
iso=*# commit;
COMMIT
iso=# select* from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | darya      | durandina
  6 | denis      | durandin
  7 | sveta      | svetova
(6 rows)
```
*сделать select * from persons во второй сессии  
видите ли вы новую запись и если да то почему? 
После выполненных действий вижу новую строку, так как commit выполнен и в первом и во втором подключении, уровень транзации REPEATABLE READ*

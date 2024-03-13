# Нагрузочное тестирование и тюнинг PostgreSQL
## развернуть виртуальную машину любым удобным способом
*Виртуальная машина развернута на ЯО*
## поставить на неё PostgreSQL 15 любым способом
```
otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
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
```
## настроить кластер PostgreSQL 15 на максимальную производительность не обращая внимание на возможные проблемы с надежностью в случае аварийной перезагрузки виртуальной машины
> DB Version: 15
> 
> OS Type: linux
>
> DB Type: dw
>
> Total Memory (RAM): 4 GB
>
> CPUs num: 1
>
> Data Storage: hdd
>
> max_connections = 60
>
> shared_buffers = 3GB
>
> effective_cache_size = 18GB
>
> maintenance_work_mem = 2GB
>
> checkpoint_completion_target = 0.9
>
> checkpoint_timeout = 1h
>
> wal_buffers = 16MB
>
> default_statistics_target = 500
>
> random_page_cost = 1.0
>
> effective_io_concurrency = 2
> 
> work_mem = 16GB
>
> min_wal_size = 1GB
>
> max_wal_size = 10GB
>
> bgwriter_lru_maxpages = 1000
>
> bgwriter_lru_multiplier = 10.0
>
> wal_compression - on
>
> fsync - off
>
> full_page_writes - off
>
> synchronous_commit - off

```
otus@otus-db-pg-vm-1:~$ sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$ psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show config_file;
               config_file
-----------------------------------------
 /etc/postgresql/15/main/postgresql.conf
(1 row)

postgres=# \q
postgres@otus-db-pg-vm-1:/home/otus$ nano /etc/postgresql/15/main/postgresql.conf
postgres@otus-db-pg-vm-1:/home/otus$ cd /etc/postgresql/15/main
postgres@otus-db-pg-vm-1:/etc/postgresql/15/main$ cd conf.d/
postgres@otus-db-pg-vm-1:/etc/postgresql/15/main/conf.d$ nano pgotus.conf
postgres@otus-db-pg-vm-1:/etc/postgresql/15/main/conf.d$ exit
exit
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# select * from pg_file_settings where name='shared_buffers';
                 sourcefile                 | sourceline | seqno |      name      | setting | applied |            error

--------------------------------------------+------------+-------+----------------+---------+---------+------------------------------
 /etc/postgresql/15/main/postgresql.conf    |        127 |    11 | shared_buffers | 128MB   | f       |
 /etc/postgresql/15/main/conf.d/pgotus.conf |          9 |    26 | shared_buffers | 3GB     | f       | setting could not be applied
(2 rows)
postgres=# show shared_buffers;
 shared_buffers
----------------
 128MB
(1 row)

postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show shared_buffers;
 shared_buffers
----------------
 3GB
(1 row)
```
## нагрузить кластер через утилиту через утилиту pgbench (https://postgrespro.ru/docs/postgrespro/14/pgbench)

*Нагрузка кластера через pgbench при стандартных параметрах*
```
otus@otus-db-pg-vm-1:~$ sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -i postgres
dropping old tables...
NOTICE:  table "pgbench_accounts" does not exist, skipping
NOTICE:  table "pgbench_branches" does not exist, skipping
NOTICE:  table "pgbench_history" does not exist, skipping
NOTICE:  table "pgbench_tellers" does not exist, skipping
creating tables...
generating data (client-side)...
100000 of 100000 tuples (100%) done (elapsed 0.07 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 1.86 s (drop tables 0.04 s, create tables 0.61 s, client-side generate 0.16 s, vacuum 0.32 s, primary keys 0.73 s).
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c 50 -j 2 -P 10 -T 60 postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 10.0 s, 598.0 tps, lat 82.680 ms stddev 78.193, 0 failed
progress: 20.0 s, 687.3 tps, lat 72.654 ms stddev 56.047, 0 failed
progress: 30.0 s, 673.7 tps, lat 74.359 ms stddev 57.664, 0 failed
progress: 40.0 s, 613.7 tps, lat 81.442 ms stddev 71.085, 0 failed
progress: 50.0 s, 707.8 tps, lat 70.587 ms stddev 59.425, 0 failed
progress: 60.0 s, 704.1 tps, lat 71.074 ms stddev 54.197, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 50
number of threads: 2
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 39896
number of failed transactions: 0 (0.000%)
latency average = 75.188 ms
latency stddev = 62.997 ms
initial connection time = 59.529 ms
tps = 664.409989 (without initial connection time)
```

*Нагрузка кластера через pgbench при установленных параметрах*
```
otus@otus-db-pg-vm-1:~$ sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -i postgres
dropping old tables...
creating tables...
generating data (client-side)...
100000 of 100000 tuples (100%) done (elapsed 0.07 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 0.46 s (drop tables 0.07 s, create tables 0.00 s, client-side generate 0.08 s, vacuum 0.21 s, primary keys 0.09 s).
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c 50 -j 2 -P 10 -T 60 postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 10.0 s, 2438.9 tps, lat 20.344 ms stddev 12.817, 0 failed
progress: 20.0 s, 2414.4 tps, lat 20.709 ms stddev 12.257, 0 failed
progress: 30.0 s, 2354.2 tps, lat 21.231 ms stddev 12.368, 0 failed
progress: 40.0 s, 2420.5 tps, lat 20.660 ms stddev 12.259, 0 failed
progress: 50.0 s, 2429.6 tps, lat 20.582 ms stddev 12.500, 0 failed
progress: 60.0 s, 2399.5 tps, lat 20.836 ms stddev 13.761, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 50
number of threads: 2
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 144621
number of failed transactions: 0 (0.000%)
latency average = 20.739 ms
latency stddev = 12.710 ms
initial connection time = 62.869 ms
tps = 2408.857592 (without initial connection time)
```
## написать какого значения tps удалось достичь, показать какие параметры в какие значения устанавливали и почему
*tps = 2408.857592 против tps = 664.409989 прирост больше чем в 3,5 раза по всем показателям.*
> Задача на тест была максимально ограничить нагрузку на диск, улучшить работу с памятью, и отключить параметры, влияющие на надежность системы в случае аварийной перезагрузки виртуальной машины.
* shared_buffers (3GB) - выделяем большую часть памяти на shared_buffers, чтобы заставить PostgreSQL вести себя как можно ближе к базе данных в памяти
* work_mem (16GB) - Т.к. скорее-всего на сервере вы не одни и сессий много, то каждая из них может использовать этот параметр по нескольку раз, поэтому не рекомендуется делать его слишком большим. Можно выставить небольшое значение для глобального параметра в конфиге и потом, в случае сложных запросов, менять этот параметр локально (для текущей сессии). Так как конкуренции при выполнении тестов у нас нет, соединение было всего одно, ставим этот параметр побольше, чтобы точно избежать использования временных файлов при выполнении запросов.
* random_page_cost (1.0) - определяет стоимость, которую будет иметь страница диска с непоследовательной выборкой, и напрямую влияет на решения планировщика запросов. Выбор консервативных значений особенно важен при использовании хранилища с высокой задержкой, например вращающихся дисков. Под наш вариант это не подходит, поэтому уравняем random_page_cost с seq_page_cost.
* wal_compression (on) - Уменьшает объем создаваемого wal-файла, тем самым уменьшая нагрузку на диск, увеличивает нагрузку на процессор.
* checkpoint_completion_target, checkpoint_timeout, min_wal_size, max_wal_size (0.9, 1h, 1GB, 10GB) - растягиваем время между контрольными точками, чтобы во время теста не произошло записи изменений, таким образом снижаем нагрузку на дисковую подсистему.
* bgwriter_lru_maxpages, bgwriter_lru_multiplier (1000, 10.0) - параметры процесса фоновой записи, который отвечает за синхронизацию страниц в shared_buffers с диском. Решила поиграть с этими параметрами, чтобы снизить нагрузку на диск во время теста.
* fsync (off) - Заставляет сервер добиваться физической записи изменений на диск. Выключение данной опции хотя и позволяет повысить производительность, но значительно увеличивает риск неисправимой порчи данных при внезапном выключении питания. Выключаем, мы ничего не испортим.
* synchronous_commit (off) - позволяет серверу не ждать сохранения данных на диске, прежде чем сообщить клиенту об успешном завершении операции. Позволяет достаточно безопасно повысить производительность работы. В случае внезапного выключения питания могут быть потеряны несколько последних транзакций, но сама база останется в рабочем состоянии, также, как и при штатной отмене потерянных транзакций.
* full_page_writes (off) - Когда этот параметр включён, сервер Postgres Pro записывает в WAL всё содержимое каждой страницы при первом изменении этой страницы после контрольной точки. Отключение этого параметра ускоряет обычные операции, но может привести к неисправимому повреждению или незаметной порче данных после сбоя системы.

## Задание со *: аналогично протестировать через утилиту https://github.com/Percona-Lab/sysbench-tpcc (требует установки https://github.com/akopytov/sysbench)
```
otus@otus-db-pg-vm-1:~$ curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | sudo bash
Detected operating system as Ubuntu/jammy.
Checking for curl...
Detected curl...
Checking for gpg...
Detected gpg...
Detected apt version as 2.4.11
Running apt-get update... done.
Installing apt-transport-https... done.
Installing /etc/apt/sources.list.d/akopytov_sysbench.list...done.
Importing packagecloud gpg key... Packagecloud gpg key imported to /etc/apt/keyrings/akopytov_sysbench-archive-keyring.gpg
done.
Running apt-get update... done.

The repository is setup! You can now install packages.
otus@otus-db-pg-vm-1:~$ sudo apt -y install sysbench
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libluajit-5.1-2 libluajit-5.1-common libmysqlclient21 mysql-common
The following NEW packages will be installed:
  libluajit-5.1-2 libluajit-5.1-common libmysqlclient21 mysql-common sysbench
0 upgraded, 5 newly installed, 0 to remove and 2 not upgraded.
Need to get 1,711 kB of archives.
After this operation, 8,056 kB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy/universe amd64 libluajit-5.1-common all 2.1.0~beta3+dfsg-6 [44.3 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy/universe amd64 libluajit-5.1-2 amd64 2.1.0~beta3+dfsg-6 [238 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy/main amd64 mysql-common all 5.8+1.0.8 [7,212 B]
Get:4 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libmysqlclient21 amd64 8.0.36-0ubuntu0.22.04.1 [1,302 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy/universe amd64 sysbench amd64 1.0.20+ds-2 [120 kB]
Fetched 1,711 kB in 0s (15.9 MB/s)
Selecting previously unselected package libluajit-5.1-common.
(Reading database ... 112348 files and directories currently installed.)
Preparing to unpack .../libluajit-5.1-common_2.1.0~beta3+dfsg-6_all.deb ...
Unpacking libluajit-5.1-common (2.1.0~beta3+dfsg-6) ...
Selecting previously unselected package libluajit-5.1-2:amd64.
Preparing to unpack .../libluajit-5.1-2_2.1.0~beta3+dfsg-6_amd64.deb ...
Unpacking libluajit-5.1-2:amd64 (2.1.0~beta3+dfsg-6) ...
Selecting previously unselected package mysql-common.
Preparing to unpack .../mysql-common_5.8+1.0.8_all.deb ...
Unpacking mysql-common (5.8+1.0.8) ...
Selecting previously unselected package libmysqlclient21:amd64.
Preparing to unpack .../libmysqlclient21_8.0.36-0ubuntu0.22.04.1_amd64.deb ...
Unpacking libmysqlclient21:amd64 (8.0.36-0ubuntu0.22.04.1) ...
Selecting previously unselected package sysbench.
Preparing to unpack .../sysbench_1.0.20+ds-2_amd64.deb ...
Unpacking sysbench (1.0.20+ds-2) ...
Setting up mysql-common (5.8+1.0.8) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Setting up libmysqlclient21:amd64 (8.0.36-0ubuntu0.22.04.1) ...
Setting up libluajit-5.1-common (2.1.0~beta3+dfsg-6) ...
Setting up libluajit-5.1-2:amd64 (2.1.0~beta3+dfsg-6) ...
Setting up sysbench (1.0.20+ds-2) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
otus@otus-db-pg-vm-1:~$ git clone https://github.com/Percona-Lab/sysbench-tpcc
Cloning into 'sysbench-tpcc'...
remote: Enumerating objects: 226, done.
remote: Counting objects: 100% (58/58), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 226 (delta 32), reused 52 (delta 32), pack-reused 168
Receiving objects: 100% (226/226), 77.81 KiB | 1.53 MiB/s, done.
Resolving deltas: 100% (120/120), done.
```

*Изменяем метод аутентификации в pg_hba.conf на trust (чтобы можно было запустить тесты без авторизации)*
```
otus@otus-db-pg-vm-1:~/sysbench-tpcc$ sudo nano /etc/postgresql/15/main/pg_hba.conf
otus@otus-db-pg-vm-1:~/sysbench-tpcc$ sudo pg_ctlcluster 15 main restart
```

*Создаём базу данных sbtest и готовим данные для теста (--time - период генерации одной порции данных --report-interval - временной интервал одной строки --scale - количество хранилищ --tables - количество таблиц)*
```
postgres=# create database sbtest;
CREATE DATABASE
postgres=# select datname, pg_size_pretty(pg_database_size(datname)) as "DB_Size" from pg_stat_database where datname = 'sbtest';
 datname | DB_Size
---------+---------
 sbtest  | 7425 kB
(1 row)
otus@otus-db-pg-vm-1:~/sysbench-tpcc$  ./tpcc.lua --pgsql-user=postgres --pgsql-db=sbtest --time=120 --report-interval=1 --tables=10 --scale=10 --use_fk=0 --trx_level=RC --db-driver=pgsql prepare
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

sbtest=# select datname, pg_size_pretty(pg_database_size(datname)) as "DB_Size" from pg_stat_database where datname = 'sbtest';
-[ RECORD 1 ]----
datname | sbtest
DB_Size | 7299 MB
```

*На дефолтной конфигурации сервера запускаем бенчмарк*
```
otus@otus-db-pg-vm-1:~/sysbench-tpcc$ ./tpcc.lua --pgsql-user=postgres --pgsql-db=sbtest --time=600 --report-interval=1 --tables=10 --scale=10 --use_fk=0 --trx_level=RC --db-driver=pgsql run

SQL statistics:
    queries performed:
        read:                            192861
        write:                           200163
        other:                           29616
        total:                           422640
    transactions:                        14807  (24.68 per sec.)
    queries:                             422640 (704.35 per sec.)
    ignored errors:                      51     (0.08 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          600.0419s
    total number of events:              14807

Latency (ms):
         min:                                    0.66
         avg:                                   40.52
         max:                                 5294.55
         95th percentile:                      112.67
         sum:                               599947.17

Threads fairness:
    events (avg/stddev):           14807.0000/0.00
    execution time (avg/stddev):   599.9472/0.00
```

*Поправляем конфиг. Перезагружаем кластер и запускаем при выбранных ранее для настройки кластера параметрах*
> DB Version: 15
> 
> OS Type: linux
>
> DB Type: dw
>
> Total Memory (RAM): 4 GB
>
> CPUs num: 1
>
> Data Storage: hdd
>
> max_connections = 60
>
> shared_buffers = 3GB
>
> effective_cache_size = 18GB
>
> maintenance_work_mem = 2GB
>
> checkpoint_completion_target = 0.9
>
> checkpoint_timeout = 1h
>
> wal_buffers = 16MB
>
> default_statistics_target = 500
>
> random_page_cost = 1.0
>
> effective_io_concurrency = 2
> 
> work_mem = 16GB
>
> min_wal_size = 1GB
>
> max_wal_size = 10GB
>
> bgwriter_lru_maxpages = 1000
>
> bgwriter_lru_multiplier = 10.0
>
> wal_compression - on
>
> fsync - off
>
> full_page_writes - off
>
> synchronous_commit - off

```
SQL statistics:
    queries performed:
        read:                            576012
        write:                           597999
        other:                           89438
        total:                           1263449
    transactions:                        44718  (74.52 per sec.)
    queries:                             1263449 (2105.60 per sec.)
    ignored errors:                      186    (0.31 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          600.0421s
    total number of events:              44718

Latency (ms):
         min:                                    0.61
         avg:                                   13.41
         max:                                  292.47
         95th percentile:                       33.72
         sum:                               599860.83

Threads fairness:
    events (avg/stddev):           44718.0000/0.00
    execution time (avg/stddev):   599.8608/0.00
```
*виден прирост около 200% по всем показателям: чтение, запись, tps*

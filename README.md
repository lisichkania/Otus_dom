# Резервное копирование и восстановление
## Создаем ВМ/докер c ПГ.

*Вм создана в ЯО, я просто её останавливаю по окончанию работы, чтобы каждый раз не создавать заново, а кластер чищу, получается быстрее*
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
## Создаем БД, схему и в ней таблицу.
```
postgres=# create database otus;
CREATE DATABASE
postgres=# \c otus
You are now connected to database "otus" as user "postgres".
otus=# CREATE SCHEMA postgresotus;
CREATE SCHEMA
otus=# SET search_path TO postgreotus,public;
SET
otus=# SHOW search_path;
     search_path
---------------------
 postgreotus, public
(1 row)
```
## Заполним таблицы автосгенерированными 100 записями.
```
otus=# SET search_path TO postgresotus;
SET
otus=# create table students as select generate_series(1, 100) as id, md5(random()::text)::char(10) as fio;
SELECT 100
otus=# \dt
             List of relations
    Schema    |   Name   | Type  |  Owner
--------------+----------+-------+----------
 postgresotus | students | table | postgres
(1 row)
```
## Под линукс пользователем Postgres создадим каталог для бэкапов

*Создала дирректорию под root сначала, пришлось сделать postgres владельцем, дать права на чтение и запись*
```
otus@otus-db-pg-vm-1:/tmp$  sudo mkdir -p /tmp/postgresql/backups
otus@otus-db-pg-vm-1:~$ cd /tmp/postgresql
otus@otus-db-pg-vm-1:/tmp/postgresql$ ls -la
total 12
drwxr-xr-x  3 root     root     4096 Mar 15 22:30 .
drwxrwxrwt 15 root     root     4096 Mar 15 22:30 ..
drwxr-xr-x  2 postgres postgres 4096 Mar 15 22:59 backups
otus@otus-db-pg-vm-1:/tmp$ cd /tmp/postgresql/backups
otus@otus-db-pg-vm-1:/tmp/postgresql/backups$ ls -la
total 8
drwxr-xr-x 2 root root 4096 Mar 15 22:30 .
drwxr-xr-x 3 root root 4096 Mar 15 22:30 ..
otus@otus-db-pg-vm-1:/tmp/postgresql/backups$ sudo chown -R postgres:postgres /tmp/postgresql/backups
otus@otus-db-pg-vm-1:/tmp/postgresql$ sudo pg_ctlcluster 15 main restart
```
## Сделаем логический бэкап используя утилиту COPY
```
otus=# \copy students to '/tmp/postgresql/backups/backup_copy.sql';
COPY 100
```

*Проверяем наличие файла в директории и смотрим, что в нём находится*
```
otus@otus-db-pg-vm-1:/tmp/postgresql$ cd /tmp/postgresql/backups
otus@otus-db-pg-vm-1:/tmp/postgresql/backups$ ls -la
total 12
drwxr-xr-x 2 postgres postgres 4096 Mar 15 22:59 .
drwxr-xr-x 3 root     root     4096 Mar 15 22:30 ..
-rw-rw-r-- 1 postgres postgres 1392 Mar 15 22:59 backup_copy.sql
otus@otus-db-pg-vm-1:/tmp/postgresql/backups$ cat backup_copy.sql
1       fa428b257e
2       b43f61e213
3       a379472bc2
4       78ee5c11f9
5       426c8ac821
6       756ee77db7
7       c853dc719a
8       1091a567d7
9       71b4de238f
10      36e6cca332
11      1b2949b1b8
12      c9f2b815e9
13      7895483f5e
14      8568f9a471
15      1ad506d0f1
16      8766936cbd
17      db3f68cc94
18      f96b96b841
19      08cc5166b8
20      6bfe558b5d
21      47d12b84ae
22      c2cfb52401
23      84edfa5a97
24      2775bc1015
25      7ff61a3aa6
26      f9cd40b096
27      f1cc06d87e
28      4aee25f8e9
29      fd3ea9aaa3
30      861f3d17ae
31      f44673a719
32      192f1a22d9
33      58ea1f7831
34      2f3ee6d1db
35      dd29284c71
36      f85ba3be5b
37      76f0f0104c
38      4b8bb48769
39      fa70a13ef5
40      3271070938
41      64147c5f2b
42      a8c65f49ed
43      df20e003ea
44      f77cbd0d83
45      4bb4deefce
46      1dc69edeac
47      11ca6860ac
48      931ffaa5c7
49      642f3c1500
50      dcf05e9889
51      65fd0a9a09
52      feaad78a83
53      3f3b2754df
54      903de0f99c
55      7d5fbe2fb3
56      cb24bc35ee
57      8565794ac8
58      d9d3d0f203
59      f10d28df52
60      c781fc097c
61      eefbafd904
62      6f7e2e7643
63      6207462e1e
64      38e5a498e1
65      d83710d767
66      8c7027d58e
67      db73c5bd39
68      771be2cd86
69      c57c4f48e4
70      ba46cfb540
71      e0d8543911
72      a1e996dd4d
73      d4fe9d4dde
74      61b147aa3d
75      e61e8ed43b
76      1b073aa064
77      10c190f049
78      0cb2faffe1
79      c605759bce
80      e2583f0f50
81      33712cfaf2
82      a00aae2228
83      4a4eaf5203
84      4b313e2cd7
85      11fe3a4051
86      0657fae61d
87      614cc6ea34
88      f262c465ab
89      96221c982c
90      87dddaa589
91      09f865608c
92      f42a5dc24f
93      c1841c61c1
94      c4aae0f936
95      ac62cb6e6f
96      edcf20efa5
97      78e1771c3f
98      5e294590f7
99      68898bc665
100     01782db899
```
## Восстановим в 2 таблицу данные из бэкапа.

*Без сюрпризов с первого раза, просто ВАУ эффект*
```
otus=# create table students2 (id int, fio char(10));
CREATE TABLE
otus=# \copy students2 from '/tmp/postgresql/backups/backup_copy.sql';
COPY 100
otus=# select * from students2;
otus=# select * from students2 limit 10;
 id |    fio
----+------------
  1 | fa428b257e
  2 | b43f61e213
  3 | a379472bc2
  4 | 78ee5c11f9
  5 | 426c8ac821
  6 | 756ee77db7
  7 | c853dc719a
  8 | 1091a567d7
  9 | 71b4de238f
 10 | 36e6cca332
(10 rows)
```
## Используя утилиту pg_dump создадим бэкап в кастомном сжатом формате двух таблиц

*Создаём бэкап в кастомном сжатом формате двух конкретных таблиц, задавая параметр -Fc и адресацию -t с перечислением самих таблиц в базе (с указанием схемы)*
```
postgres@otus-db-pg-vm-1:/tmp/postgresql/backups$ sudo -u postgres pg_dump -Fc -t postgresotus.students -t postgresotus.students2 otus > postgresotus_schema.dmp
postgres@otus-db-pg-vm-1:/tmp/postgresql/backups$ ls -la
total 24
drwxr-xr-x 2 postgres postgres 4096 Mar 16 00:41 .
drwxr-xr-x 3 root     root     4096 Mar 15 22:30 ..
-rw-rw-r-- 1 postgres postgres 2799 Mar 15 23:24 backup_copy.sql
-rw-rw-r-- 1 postgres postgres 6242 Mar 15 23:43 backup_dump.sql
-rw-rw-r-- 1 postgres postgres 3449 Mar 16 00:41 postgresotus_schema.dmp
-rw-rw-r-- 1 postgres postgres    0 Mar 16 00:13 students.dump
```
## Используя утилиту pg_restore восстановим в новую БД только вторую таблицу!

*Создаем новую БД, а в ней схему в которую будем восстанавливать таблицу*
```
postgres=# create database otus2;
CREATE DATABASE
postgres=# \c otus2
You are now connected to database "otus2" as user "postgres".
otus2=# CREATE SCHEMA postgresotus;
CREATE SCHEMA
otus2=# \dt
Did not find any relations.
```

*Делаем восстановление из созданного ранее бэкапа и проверяем таблицу в базе*
```
postgres@otus-db-pg-vm-1:/tmp/postgresql/backups$ sudo -u postgres pg_restore --table=students2 -d otus2 /tmp/postgresql/backups/postgresotus_schema.dmp
otus2=# \dt
              List of relations
    Schema    |   Name    | Type  |  Owner
--------------+-----------+-------+----------
 postgresotus | students2 | table | postgres
(1 row)

otus2=# select * from students2;
```

*Несколько раз дропнула таблицу и восстановила её из бэкапа, потому что не сразу поняла получилось ли )))*

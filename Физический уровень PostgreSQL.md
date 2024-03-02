# Физический уровень PostgreSQL
## Cоздайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере
 1. ВМ создана в ЯО в первой домашней работе, сделана прерываемой, поэтому по окончании работы просто останавливается, IP постоянный, чтобы не менять его в кодировке.
 2. Подключаемся к ВМ по IP.
```
lisichkania@LAPTOP-JVR9DUC8:~$ ssh -i ~/.ssh/yc_key otus@158.160.67.175
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Wed Feb 28 11:05:29 PM UTC 2024

  System load:  0.0                Processes:             140
  Usage of /:   26.3% of 17.63GB   Users logged in:       1
  Memory usage: 6%                 IPv4 address for eth0: 10.129.0.18
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Wed Feb 28 23:05:29 2024 from 91.108.28.103
```
## поставьте на нее PostgreSQL 15 через sudo apt
```
otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,421 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [279 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/restricted amd64 Packages [1,504 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/restricted Translation-en [247 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,052 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/universe Translation-en [237 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-updates/multiverse amd64 Packages [42.1 kB]
Get:12 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,205 kB]
Get:13 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [219 kB]
Get:14 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1,476 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [244 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [846 kB]
Get:17 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [161 kB]
Get:18 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [37.1 kB]
Fetched 9,197 kB in 2s (4,406 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
13 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following packages will be upgraded:
  binutils binutils-common binutils-x86-64-linux-gnu cloud-init less libbinutils libctf-nobfd0 libctf0 libssl3 libuv1
  libxml2 openssl tzdata
13 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
12 standard LTS security updates
Need to get 8,414 kB of archives.
After this operation, 8,192 B of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libssl3 amd64 3.0.2-0ubuntu1.15 [1,905 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 less amd64 590-1ubuntu0.22.04.2 [143 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libxml2 amd64 2.9.13+dfsg-1ubuntu0.4 [763 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 openssl amd64 3.0.2-0ubuntu1.15 [1,186 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 tzdata all 2024a-0ubuntu0.22.04 [348 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libuv1 amd64 1.43.0-1ubuntu0.1 [92.7 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libctf0 amd64 2.38-4ubuntu2.6 [103 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libctf-nobfd0 amd64 2.38-4ubuntu2.6 [108 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils-x86-64-linux-gnu amd64 2.38-4ubuntu2.6 [2,326 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libbinutils amd64 2.38-4ubuntu2.6 [662 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils amd64 2.38-4ubuntu2.6 [3,200 B]
Get:12 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils-common amd64 2.38-4ubuntu2.6 [222 kB]
Get:13 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 cloud-init all 23.4.4-0ubuntu0~22.04.1 [551 kB]
Fetched 8,414 kB in 0s (103 MB/s)
Preconfiguring packages ...
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../libssl3_3.0.2-0ubuntu1.15_amd64.deb ...
Unpacking libssl3:amd64 (3.0.2-0ubuntu1.15) over (3.0.2-0ubuntu1.14) ...
Setting up libssl3:amd64 (3.0.2-0ubuntu1.15) ...
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../00-less_590-1ubuntu0.22.04.2_amd64.deb ...
Unpacking less (590-1ubuntu0.22.04.2) over (590-1ubuntu0.22.04.1) ...
Preparing to unpack .../01-libxml2_2.9.13+dfsg-1ubuntu0.4_amd64.deb ...
Unpacking libxml2:amd64 (2.9.13+dfsg-1ubuntu0.4) over (2.9.13+dfsg-1ubuntu0.3) ...
Preparing to unpack .../02-openssl_3.0.2-0ubuntu1.15_amd64.deb ...
Unpacking openssl (3.0.2-0ubuntu1.15) over (3.0.2-0ubuntu1.14) ...
Preparing to unpack .../03-tzdata_2024a-0ubuntu0.22.04_all.deb ...
Unpacking tzdata (2024a-0ubuntu0.22.04) over (2023d-0ubuntu0.22.04) ...
Preparing to unpack .../04-libuv1_1.43.0-1ubuntu0.1_amd64.deb ...
Unpacking libuv1:amd64 (1.43.0-1ubuntu0.1) over (1.43.0-1) ...
Preparing to unpack .../05-libctf0_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libctf0:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../06-libctf-nobfd0_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libctf-nobfd0:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../07-binutils-x86-64-linux-gnu_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils-x86-64-linux-gnu (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../08-libbinutils_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libbinutils:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../09-binutils_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../10-binutils-common_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils-common:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../11-cloud-init_23.4.4-0ubuntu0~22.04.1_all.deb ...
Unpacking cloud-init (23.4.4-0ubuntu0~22.04.1) over (23.4.3-0ubuntu0~22.04.1) ...
Setting up cloud-init (23.4.4-0ubuntu0~22.04.1) ...
Setting up binutils-common:amd64 (2.38-4ubuntu2.6) ...
Setting up less (590-1ubuntu0.22.04.2) ...
Setting up libctf-nobfd0:amd64 (2.38-4ubuntu2.6) ...
Setting up tzdata (2024a-0ubuntu0.22.04) ...

Current default time zone: 'Etc/UTC'
Local time is now:      Sat Mar  2 13:21:43 UTC 2024.
Universal Time is now:  Sat Mar  2 13:21:43 UTC 2024.
Run 'dpkg-reconfigure tzdata' if you wish to change it.

Setting up libuv1:amd64 (1.43.0-1ubuntu0.1) ...
Setting up libbinutils:amd64 (2.38-4ubuntu2.6) ...
Setting up openssl (3.0.2-0ubuntu1.15) ...
Setting up libxml2:amd64 (2.9.13+dfsg-1ubuntu0.4) ...
Setting up libctf0:amd64 (2.38-4ubuntu2.6) ...
Setting up binutils-x86-64-linux-gnu (2.38-4ubuntu2.6) ...
Setting up binutils (2.38-4ubuntu2.6) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Processing triggers for rsyslog (8.2112.0-2ubuntu2.2) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
 /etc/needrestart/restart.d/systemd-manager
 systemctl restart packagekit.service ssh.service systemd-journald.service systemd-networkd.service systemd-resolved.service systemd-timesyncd.service systemd-udevd.service udisks2.service
Service restarts being deferred:
 systemctl restart systemd-logind.service
 systemctl restart user@1000.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
OK
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Hit:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu jammy-security InRelease
Get:5 http://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease [123 kB]
Get:6 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 Packages [296 kB]
Fetched 419 kB in 1s (312 kB/s)
Reading package lists... Done
W: http://apt.postgresql.org/pub/repos/apt/dists/jammy-pgdg/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
Suggested packages:
  lm-sensors postgresql-doc-15 isag
The following NEW packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-15 postgresql-client-15 postgresql-client-common postgresql-common ssl-cert
  sysstat
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 45.4 MB of archives.
After this operation, 184 MB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-perl all 4.04000-1 [81.8 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy/main amd64 ssl-cert all 1.1.2 [17.4 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy/main amd64 libcommon-sense-perl amd64 3.75-2build1 [21.1 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy/main amd64 libtypes-serialiser-perl all 1.01-1 [11.6 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-xs-perl amd64 4.030-1build3 [87.2 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libllvm15 amd64 1:15.0.7-0ubuntu0.22.04.3 [25.4 MB]
Get:7 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-common all 257.pgdg22.04+1 [94.3 kB]
Get:8 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-common all 257.pgdg22.04+1 [239 kB]
Get:9 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 libpq5 amd64 16.2-1.pgdg22.04+1 [214 kB]
Get:10 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-15 amd64 15.6-1.pgdg22.04+1 [1,686 kB]
Get:11 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-15 amd64 15.6-1.pgdg22.04+1 [17.1 MB]
Get:12 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors-config all 1:3.6.0-7ubuntu1 [5,274 B]
Get:13 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors5 amd64 1:3.6.0-7ubuntu1 [26.3 kB]
Get:14 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 sysstat amd64 12.5.2-2ubuntu0.2 [487 kB]
Fetched 45.4 MB in 3s (14.6 MB/s)
Preconfiguring packages ...
Selecting previously unselected package libjson-perl.
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../00-libjson-perl_4.04000-1_all.deb ...
Unpacking libjson-perl (4.04000-1) ...
Selecting previously unselected package postgresql-client-common.
Preparing to unpack .../01-postgresql-client-common_257.pgdg22.04+1_all.deb ...
Unpacking postgresql-client-common (257.pgdg22.04+1) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../02-ssl-cert_1.1.2_all.deb ...
Unpacking ssl-cert (1.1.2) ...
Selecting previously unselected package postgresql-common.
Preparing to unpack .../03-postgresql-common_257.pgdg22.04+1_all.deb ...
Adding 'diversion of /usr/bin/pg_config to /usr/bin/pg_config.libpq-dev by postgresql-common'
Unpacking postgresql-common (257.pgdg22.04+1) ...
Selecting previously unselected package libcommon-sense-perl:amd64.
Preparing to unpack .../04-libcommon-sense-perl_3.75-2build1_amd64.deb ...
Unpacking libcommon-sense-perl:amd64 (3.75-2build1) ...
Selecting previously unselected package libtypes-serialiser-perl.
Preparing to unpack .../05-libtypes-serialiser-perl_1.01-1_all.deb ...
Unpacking libtypes-serialiser-perl (1.01-1) ...
Selecting previously unselected package libjson-xs-perl.
Preparing to unpack .../06-libjson-xs-perl_4.030-1build3_amd64.deb ...
Unpacking libjson-xs-perl (4.030-1build3) ...
Selecting previously unselected package libllvm15:amd64.
Preparing to unpack .../07-libllvm15_1%3a15.0.7-0ubuntu0.22.04.3_amd64.deb ...
Unpacking libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) ...
Selecting previously unselected package libpq5:amd64.
Preparing to unpack .../08-libpq5_16.2-1.pgdg22.04+1_amd64.deb ...
Unpacking libpq5:amd64 (16.2-1.pgdg22.04+1) ...
Selecting previously unselected package libsensors-config.
Preparing to unpack .../09-libsensors-config_1%3a3.6.0-7ubuntu1_all.deb ...
Unpacking libsensors-config (1:3.6.0-7ubuntu1) ...
Selecting previously unselected package libsensors5:amd64.
Preparing to unpack .../10-libsensors5_1%3a3.6.0-7ubuntu1_amd64.deb ...
Unpacking libsensors5:amd64 (1:3.6.0-7ubuntu1) ...
Selecting previously unselected package postgresql-client-15.
Preparing to unpack .../11-postgresql-client-15_15.6-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-client-15 (15.6-1.pgdg22.04+1) ...
Selecting previously unselected package postgresql-15.
Preparing to unpack .../12-postgresql-15_15.6-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-15 (15.6-1.pgdg22.04+1) ...
Selecting previously unselected package sysstat.
Preparing to unpack .../13-sysstat_12.5.2-2ubuntu0.2_amd64.deb ...
Unpacking sysstat (12.5.2-2ubuntu0.2) ...
Setting up postgresql-client-common (257.pgdg22.04+1) ...
Setting up libsensors-config (1:3.6.0-7ubuntu1) ...
Setting up libpq5:amd64 (16.2-1.pgdg22.04+1) ...
Setting up libcommon-sense-perl:amd64 (3.75-2build1) ...
Setting up postgresql-client-15 (15.6-1.pgdg22.04+1) ...
update-alternatives: using /usr/share/postgresql/15/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up ssl-cert (1.1.2) ...
Setting up libsensors5:amd64 (1:3.6.0-7ubuntu1) ...
Setting up libtypes-serialiser-perl (1.01-1) ...
Setting up libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) ...
Setting up libjson-perl (4.04000-1) ...
Setting up sysstat (12.5.2-2ubuntu0.2) ...

Creating config file /etc/default/sysstat with new version
update-alternatives: using /usr/bin/sar.sysstat to provide /usr/bin/sar (sar) in auto mode
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-collect.timer → /lib/systemd/system/sysstat-collect.timer.
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-summary.timer → /lib/systemd/system/sysstat-summary.timer.
Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sysstat.service.
Setting up libjson-xs-perl (4.030-1build3) ...
Setting up postgresql-common (257.pgdg22.04+1) ...
Adding user postgres to group ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
Removing obsolete dictionary files:
'/etc/apt/trusted.gpg.d/apt.postgresql.org.gpg' -> '/usr/share/postgresql-common/pgdg/apt.postgresql.org.gpg'
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
Setting up postgresql-15 (15.6-1.pgdg22.04+1) ...
Creating new PostgreSQL cluster 15/main ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main --auth-local peer --auth-host scram-sha-256 --no-instructions
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
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
Service restarts being deferred:
 systemctl restart systemd-logind.service
 systemctl restart user@1000.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
## проверьте что кластер запущен через sudo -u postgres pg_lsclusters
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
## зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
postgres=# create table test(c1 text);
postgres=# insert into test values('1');
\q
```
otus@otus-db-pg-vm-1:~$  sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# ALTER USER postgres PASSWORD 'otus';
ALTER ROLE
postgres=# \q
lisichkania@LAPTOP-JVR9DUC8:~$ ssh -i ~/.ssh/yc_key otus@158.160.67.175
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Sat Mar  2 01:33:42 PM UTC 2024

  System load:  0.0                Processes:             136
  Usage of /:   26.3% of 17.63GB   Users logged in:       1
  Memory usage: 7%                 IPv4 address for eth0: 10.129.0.19
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

Last login: Sat Mar  2 13:21:06 2024 from 91.108.28.103
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# create table test(c1 text);
CREATE TABLE
postgres=# insert into test values('1');
INSERT 0 1
postgres=# \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | test | table | postgres
(1 row)

postgres=# SELECT * FROM test;
 c1
----
 1
(1 row)
postgres=# \q
```
## остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop
```
otus@otus-db-pg-vm-1:~$ sudo systemctl stop postgresql@15-main
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
otus@otus-db-pg-vm-1:~$
```
## создайте новый диск к ВМ размером 10GB
Добавляю диск в ЯО и подсоединяю к ВМ
## добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
```
otus@otus-db-pg-vm-1:~$ sudo lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL
NAME   FSTYPE     SIZE MOUNTPOINT        LABEL
loop0  squashfs  63.3M /snap/core20/1822
loop1  squashfs 111.9M /snap/lxd/24322
loop2  squashfs  49.8M /snap/snapd/18357
loop3  squashfs  40.4M /snap/snapd/20671
loop4            63.9M /snap/core20/2182
loop5              87M /snap/lxd/27037
vda                18G
├─vda1              1M
└─vda2 ext4        18G /
vdb                10G
```
## проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
```
otus@otus-db-pg-vm-1:~$ sudo apt update
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Hit:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Hit:4 http://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease
Get:5 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Fetched 110 kB in 1s (146 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
otus@otus-db-pg-vm-1:~$ sudo apt install parted
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
parted is already the newest version (3.4-2build1).
parted set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
otus@otus-db-pg-vm-1:~$ sudo parted -l | grep
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
Error: /dev/vdb: unrecognised disk label
otus@otus-db-pg-vm-1:~$ sudo parted /dev/vdb mklabel gpt
Information: You may need to update /etc/fstab.

otus@otus-db-pg-vm-1:~$ sudo mount -a
otus@otus-db-pg-vm-1:~$ sudo parted /dev/vdb mklabel gpt
Warning: The existing disk label on /dev/vdb will be destroyed and all data on this disk will be lost. Do you want to
continue?
Yes/No? yes
Information: You may need to update /etc/fstab.

otus@otus-db-pg-vm-1:~$ sudo parted -a opt /dev/vdb mkpart primary ext4 0% 100%
Information: You may need to update /etc/fstab.

otus@otus-db-pg-vm-1:~$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0    7:0    0  63.3M  1 loop /snap/core20/1822
loop1    7:1    0 111.9M  1 loop /snap/lxd/24322
loop2    7:2    0  49.8M  1 loop /snap/snapd/18357
loop3    7:3    0  40.4M  1 loop /snap/snapd/20671
loop4    7:4    0  63.9M  1 loop /snap/core20/2182
loop5    7:5    0    87M  1 loop /snap/lxd/27037
vda    252:0    0    18G  0 disk
├─vda1 252:1    0     1M  0 part
└─vda2 252:2    0    18G  0 part /
vdb    252:16   0    10G  0 disk
└─vdb1 252:17   0    10G  0 part
otus@otus-db-pg-vm-1:~$ sudo mkfs.ext4 -L dom3 /dev/vdb1
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 2620928 4k blocks and 655360 inodes
Filesystem UUID: 9578ac23-85b9-42aa-8c9b-e2239658c823
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

otus@otus-db-pg-vm-1:~$ sudo lsblk --fs
NAME   FSTYPE   FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
loop0  squashfs 4.0                                                    0   100% /snap/core20/1822
loop1  squashfs 4.0                                                    0   100% /snap/lxd/24322
loop2  squashfs 4.0                                                    0   100% /snap/snapd/18357
loop3  squashfs 4.0                                                    0   100% /snap/snapd/20671
loop4                                                                  0   100% /snap/core20/2182
loop5                                                                  0   100% /snap/lxd/27037
vda
├─vda1
└─vda2 ext4     1.0         ed465c6e-049a-41c6-8e0b-c8da348a3577   12.2G    26% /
vdb
└─vdb1 ext4     1.0   dom3  9578ac23-85b9-42aa-8c9b-e2239658c823
otus@otus-db-pg-vm-1:~$ sudo nano /etc/fstab
otus@otus-db-pg-vm-1:~$ df -h -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2        18G  4.7G   13G  28% /
/dev/vdb1       9.8G   24K  9.3G   1% /mnt/data
otus@otus-db-pg-vm-1:~$ echo "success" | sudo tee /mnt/data/test_file
success
otus@otus-db-pg-vm-1:~$ cat /mnt/data/test_file
success
otus@otus-db-pg-vm-1:~$ sudo rm /mnt/data/test_file
```
## перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)
```
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
otus@otus-db-pg-vm-1:~$ df -h -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2        18G  4.7G   13G  28% /
/dev/vdb1       9.8G   24K  9.3G   1% /mnt/data
```
## сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/
```
otus@otus-db-pg-vm-1:~$ sudo chown -R postgres:postgres /mnt/data/
```
## перенесите содержимое /var/lib/postgres/15 в /mnt/data - mv /var/lib/postgresql/15 /mnt/data
Переношу вместе с папкой postgresql
```
otus@otus-db-pg-vm-1:~$ sudo mv /var/lib/postgresql /mnt/data
```
## попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start напишите получилось или нет и почему
Кластер не запустился, так как по данному пути его не существует
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres pg_ctlcluster 15 main start
Error: /var/lib/postgresql/15/main is not accessible or does not exist
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner     Data directory              Log file
15  main    5432 down   <unknown> /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
## задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его? напишите что и почему поменяли
В файле postgresql.conf надо поменять data_directory = '/var/lib/postgresql/15/main' на data_directory = '/mnt/data/postgresql/15/main'
```
otus@otus-db-pg-vm-1:~$  sudo nano /etc/postgresql/15/main/postgresql.conf
otus@otus-db-pg-vm-1:~$ sudo systemctl start postgresql
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  main    5432 online postgres /mnt/data/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
## попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start напишите получилось или нет и почему
Кластер уже запущен после start postgresql, что мы и увидели в предыдущем пункте
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres pg_ctlcluster 15 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@15-main
Cluster is already running.
```
## зайдите через через psql и проверьте содержимое ранее созданной таблицы
```
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# SELECT * FROM test;
 c1
----
 1
(1 row)

postgres=# \q
```
### задание со звездочкой *: не удаляя существующий инстанс ВМ сделайте новый
```
lisichkania@LAPTOP-JVR9DUC8:~$ ssh -i ~/.ssh/yc_key otus@51.250.104.27
The authenticity of host '51.250.104.27 (51.250.104.27)' can't be established.
ED25519 key fingerprint is SHA256:WMPjuzTAl5SeyxJEFpyKW0XyI1gynflj/UJyvm+steo.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added '51.250.104.27' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Sat Mar  2 10:31:09 PM UTC 2024

  System load:  0.8857421875       Processes:             135
  Usage of /:   24.2% of 17.63GB   Users logged in:       0
  Memory usage: 5%                 IPv4 address for eth0: 10.129.0.34
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.
```
### поставьте на его PostgreSQL
```
otus@otus-db-pg-vm-2:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,421 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [279 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/restricted amd64 Packages [1,504 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/restricted Translation-en [247 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,052 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/universe Translation-en [237 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-updates/multiverse amd64 Packages [42.1 kB]
Get:12 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,205 kB]
Get:13 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [219 kB]
Get:14 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1,476 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [244 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [846 kB]
Get:17 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [161 kB]
Get:18 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [37.1 kB]
Fetched 9,197 kB in 2s (6,065 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
13 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following packages will be upgraded:
  binutils binutils-common binutils-x86-64-linux-gnu cloud-init less libbinutils libctf-nobfd0 libctf0 libssl3 libuv1
  libxml2 openssl tzdata
13 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
12 standard LTS security updates
Need to get 8,414 kB of archives.
After this operation, 8,192 B of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libssl3 amd64 3.0.2-0ubuntu1.15 [1,905 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 less amd64 590-1ubuntu0.22.04.2 [143 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libxml2 amd64 2.9.13+dfsg-1ubuntu0.4 [763 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 openssl amd64 3.0.2-0ubuntu1.15 [1,186 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 tzdata all 2024a-0ubuntu0.22.04 [348 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libuv1 amd64 1.43.0-1ubuntu0.1 [92.7 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libctf0 amd64 2.38-4ubuntu2.6 [103 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libctf-nobfd0 amd64 2.38-4ubuntu2.6 [108 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils-x86-64-linux-gnu amd64 2.38-4ubuntu2.6 [2,326 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libbinutils amd64 2.38-4ubuntu2.6 [662 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils amd64 2.38-4ubuntu2.6 [3,200 B]
Get:12 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 binutils-common amd64 2.38-4ubuntu2.6 [222 kB]
Get:13 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 cloud-init all 23.4.4-0ubuntu0~22.04.1 [551 kB]
Fetched 8,414 kB in 0s (107 MB/s)
Preconfiguring packages ...
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../libssl3_3.0.2-0ubuntu1.15_amd64.deb ...
Unpacking libssl3:amd64 (3.0.2-0ubuntu1.15) over (3.0.2-0ubuntu1.14) ...
Setting up libssl3:amd64 (3.0.2-0ubuntu1.15) ...
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../00-less_590-1ubuntu0.22.04.2_amd64.deb ...
Unpacking less (590-1ubuntu0.22.04.2) over (590-1ubuntu0.22.04.1) ...
Preparing to unpack .../01-libxml2_2.9.13+dfsg-1ubuntu0.4_amd64.deb ...
Unpacking libxml2:amd64 (2.9.13+dfsg-1ubuntu0.4) over (2.9.13+dfsg-1ubuntu0.3) ...
Preparing to unpack .../02-openssl_3.0.2-0ubuntu1.15_amd64.deb ...
Unpacking openssl (3.0.2-0ubuntu1.15) over (3.0.2-0ubuntu1.14) ...
Preparing to unpack .../03-tzdata_2024a-0ubuntu0.22.04_all.deb ...
Unpacking tzdata (2024a-0ubuntu0.22.04) over (2023d-0ubuntu0.22.04) ...
Preparing to unpack .../04-libuv1_1.43.0-1ubuntu0.1_amd64.deb ...
Unpacking libuv1:amd64 (1.43.0-1ubuntu0.1) over (1.43.0-1) ...
Preparing to unpack .../05-libctf0_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libctf0:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../06-libctf-nobfd0_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libctf-nobfd0:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../07-binutils-x86-64-linux-gnu_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils-x86-64-linux-gnu (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../08-libbinutils_2.38-4ubuntu2.6_amd64.deb ...
Unpacking libbinutils:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../09-binutils_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../10-binutils-common_2.38-4ubuntu2.6_amd64.deb ...
Unpacking binutils-common:amd64 (2.38-4ubuntu2.6) over (2.38-4ubuntu2.5) ...
Preparing to unpack .../11-cloud-init_23.4.4-0ubuntu0~22.04.1_all.deb ...
Unpacking cloud-init (23.4.4-0ubuntu0~22.04.1) over (23.4.3-0ubuntu0~22.04.1) ...
Setting up cloud-init (23.4.4-0ubuntu0~22.04.1) ...
Setting up binutils-common:amd64 (2.38-4ubuntu2.6) ...
Setting up less (590-1ubuntu0.22.04.2) ...
Setting up libctf-nobfd0:amd64 (2.38-4ubuntu2.6) ...
Setting up tzdata (2024a-0ubuntu0.22.04) ...

Current default time zone: 'Etc/UTC'
Local time is now:      Sat Mar  2 22:32:06 UTC 2024.
Universal Time is now:  Sat Mar  2 22:32:06 UTC 2024.
Run 'dpkg-reconfigure tzdata' if you wish to change it.

Setting up libuv1:amd64 (1.43.0-1ubuntu0.1) ...
Setting up libbinutils:amd64 (2.38-4ubuntu2.6) ...
Setting up openssl (3.0.2-0ubuntu1.15) ...
Setting up libxml2:amd64 (2.9.13+dfsg-1ubuntu0.4) ...
Setting up libctf0:amd64 (2.38-4ubuntu2.6) ...
Setting up binutils-x86-64-linux-gnu (2.38-4ubuntu2.6) ...
Setting up binutils (2.38-4ubuntu2.6) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Processing triggers for rsyslog (8.2112.0-2ubuntu2.2) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
 /etc/needrestart/restart.d/systemd-manager
 systemctl restart packagekit.service ssh.service systemd-journald.service systemd-networkd.service systemd-resolved.service systemd-timesyncd.service systemd-udevd.service udisks2.service
Service restarts being deferred:
 systemctl restart systemd-logind.service
 systemctl restart user@1000.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
OK
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Hit:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Get:4 http://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease [123 kB]
Hit:5 http://security.ubuntu.com/ubuntu jammy-security InRelease
Get:6 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 Packages [296 kB]
Fetched 419 kB in 1s (676 kB/s)
Reading package lists... Done
W: http://apt.postgresql.org/pub/repos/apt/dists/jammy-pgdg/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
Suggested packages:
  lm-sensors postgresql-doc-15 isag
The following NEW packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-15 postgresql-client-15 postgresql-client-common postgresql-common ssl-cert
  sysstat
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 45.4 MB of archives.
After this operation, 184 MB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-perl all 4.04000-1 [81.8 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy/main amd64 ssl-cert all 1.1.2 [17.4 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy/main amd64 libcommon-sense-perl amd64 3.75-2build1 [21.1 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy/main amd64 libtypes-serialiser-perl all 1.01-1 [11.6 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-xs-perl amd64 4.030-1build3 [87.2 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libllvm15 amd64 1:15.0.7-0ubuntu0.22.04.3 [25.4 MB]
Get:7 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-common all 257.pgdg22.04+1 [94.3 kB]
Get:8 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-common all 257.pgdg22.04+1 [239 kB]
Get:9 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 libpq5 amd64 16.2-1.pgdg22.04+1 [214 kB]
Get:10 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-15 amd64 15.6-1.pgdg22.04+1 [1,686 kB]
Get:11 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-15 amd64 15.6-1.pgdg22.04+1 [17.1 MB]
Get:12 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors-config all 1:3.6.0-7ubuntu1 [5,274 B]
Get:13 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors5 amd64 1:3.6.0-7ubuntu1 [26.3 kB]
Get:14 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 sysstat amd64 12.5.2-2ubuntu0.2 [487 kB]
Fetched 45.4 MB in 4s (11.2 MB/s)
Preconfiguring packages ...
Selecting previously unselected package libjson-perl.
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../00-libjson-perl_4.04000-1_all.deb ...
Unpacking libjson-perl (4.04000-1) ...
Selecting previously unselected package postgresql-client-common.
Preparing to unpack .../01-postgresql-client-common_257.pgdg22.04+1_all.deb ...
Unpacking postgresql-client-common (257.pgdg22.04+1) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../02-ssl-cert_1.1.2_all.deb ...
Unpacking ssl-cert (1.1.2) ...
Selecting previously unselected package postgresql-common.
Preparing to unpack .../03-postgresql-common_257.pgdg22.04+1_all.deb ...
Adding 'diversion of /usr/bin/pg_config to /usr/bin/pg_config.libpq-dev by postgresql-common'
Unpacking postgresql-common (257.pgdg22.04+1) ...
Selecting previously unselected package libcommon-sense-perl:amd64.
Preparing to unpack .../04-libcommon-sense-perl_3.75-2build1_amd64.deb ...
Unpacking libcommon-sense-perl:amd64 (3.75-2build1) ...
Selecting previously unselected package libtypes-serialiser-perl.
Preparing to unpack .../05-libtypes-serialiser-perl_1.01-1_all.deb ...
Unpacking libtypes-serialiser-perl (1.01-1) ...
Selecting previously unselected package libjson-xs-perl.
Preparing to unpack .../06-libjson-xs-perl_4.030-1build3_amd64.deb ...
Unpacking libjson-xs-perl (4.030-1build3) ...
Selecting previously unselected package libllvm15:amd64.
Preparing to unpack .../07-libllvm15_1%3a15.0.7-0ubuntu0.22.04.3_amd64.deb ...
Unpacking libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) ...
Selecting previously unselected package libpq5:amd64.
Preparing to unpack .../08-libpq5_16.2-1.pgdg22.04+1_amd64.deb ...
Unpacking libpq5:amd64 (16.2-1.pgdg22.04+1) ...
Selecting previously unselected package libsensors-config.
Preparing to unpack .../09-libsensors-config_1%3a3.6.0-7ubuntu1_all.deb ...
Unpacking libsensors-config (1:3.6.0-7ubuntu1) ...
Selecting previously unselected package libsensors5:amd64.
Preparing to unpack .../10-libsensors5_1%3a3.6.0-7ubuntu1_amd64.deb ...
Unpacking libsensors5:amd64 (1:3.6.0-7ubuntu1) ...
Selecting previously unselected package postgresql-client-15.
Preparing to unpack .../11-postgresql-client-15_15.6-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-client-15 (15.6-1.pgdg22.04+1) ...
Selecting previously unselected package postgresql-15.
Preparing to unpack .../12-postgresql-15_15.6-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-15 (15.6-1.pgdg22.04+1) ...
Selecting previously unselected package sysstat.
Preparing to unpack .../13-sysstat_12.5.2-2ubuntu0.2_amd64.deb ...
Unpacking sysstat (12.5.2-2ubuntu0.2) ...
Setting up postgresql-client-common (257.pgdg22.04+1) ...
Setting up libsensors-config (1:3.6.0-7ubuntu1) ...
Setting up libpq5:amd64 (16.2-1.pgdg22.04+1) ...
Setting up libcommon-sense-perl:amd64 (3.75-2build1) ...
Setting up postgresql-client-15 (15.6-1.pgdg22.04+1) ...
update-alternatives: using /usr/share/postgresql/15/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up ssl-cert (1.1.2) ...
Setting up libsensors5:amd64 (1:3.6.0-7ubuntu1) ...
Setting up libtypes-serialiser-perl (1.01-1) ...
Setting up libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) ...
Setting up libjson-perl (4.04000-1) ...
Setting up sysstat (12.5.2-2ubuntu0.2) ...

Creating config file /etc/default/sysstat with new version
update-alternatives: using /usr/bin/sar.sysstat to provide /usr/bin/sar (sar) in auto mode
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-collect.timer → /lib/systemd/system/sysstat-collect.timer.
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-summary.timer → /lib/systemd/system/sysstat-summary.timer.
Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sysstat.service.
Setting up libjson-xs-perl (4.030-1build3) ...
Setting up postgresql-common (257.pgdg22.04+1) ...
Adding user postgres to group ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
Removing obsolete dictionary files:
'/etc/apt/trusted.gpg.d/apt.postgresql.org.gpg' -> '/usr/share/postgresql-common/pgdg/apt.postgresql.org.gpg'
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
Setting up postgresql-15 (15.6-1.pgdg22.04+1) ...
Creating new PostgreSQL cluster 15/main ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main --auth-local peer --auth-host scram-sha-256 --no-instructions
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
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
Service restarts being deferred:
 systemctl restart systemd-logind.service
 systemctl restart user@1000.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
otus@otus-db-pg-vm-2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
### удалите файлы с данными из /var/lib/postgres, 
```
otus@otus-db-pg-vm-2:~$  sudo rm -R /var/lib/postgresql
```
### перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй и запустите PostgreSQL на второй машине так чтобы он работал с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось
1. Создала вторую ВМ на ЯО, переподключила созданный ранее диск к ней.
2. Создаем каталог, монтируем диск.
3. Прописываем в postgresql.conf data_directory = '/mnt/data/postgresql/15/main'
4. Проверяем созданную ранее с другой ВМ таблицу.
```
otus@otus-db-pg-vm-2:~$ sudo lsblk --fs
NAME   FSTYPE   FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
loop0  squashfs 4.0                                                    0   100% /snap/lxd/24322
loop1  squashfs 4.0                                                    0   100% /snap/core20/1822
loop2  squashfs 4.0                                                    0   100% /snap/snapd/18357
loop3  squashfs 4.0                                                    0   100% /snap/snapd/20671
loop4                                                                  0   100% /snap/core20/2182
loop5                                                                  0   100% /snap/lxd/27037
vda
├─vda1
└─vda2 ext4     1.0         ed465c6e-049a-41c6-8e0b-c8da348a3577   12.3G    26% /
vdb
└─vdb1 ext4     1.0   dom3  0373367e-59d5-4d94-8e2b-f869434cda10
otus@otus-db-pg-vm-2:~$ sudo mkdir -p /mnt/data
otus@otus-db-pg-vm-2:~$ sudo nano /etc/fstab
otus@otus-db-pg-vm-2:~$ sudo mount -a
otus@otus-db-pg-vm-2:~$ df -h -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2        18G  4.6G   13G  28% /
/dev/vdb1       9.8G   39M  9.2G   1% /mnt/data
otus@otus-db-pg-vm-2:~$ sudo nano /etc/postgresql/15/main/postgresql.conf
otus@otus-db-pg-vm-2:~$ sudo systemctl start postgresql
otus@otus-db-pg-vm-2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  main    5432 online postgres /mnt/data/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
otus@otus-db-pg-vm-2:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# SELECT * FROM test;
 c1
----
 1
(1 row)

postgres=# \q
```

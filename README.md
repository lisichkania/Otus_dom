# Виды и устройство репликации в PostgreSQL

*Производим подготовительные работы*
1. Создаем 3 ВМ на ЯО, otus-db-pg-vm-1, otus-db-pg-vm-2, otus-vm-db-pg-net-3 (не то копирнула, переделывать не стала)
2. Ставим на каждую ВМ postgresql-15 (на первой ВМ он был установлен, поэтому я его просто дропнула и восстановила)

*1 ВМ*
```
otus@otus-db-pg-vm-1:~$ sudo systemctl stop postgresql@15-main
otus@otus-db-pg-vm-1:~$ sudo pg_dropcluster 15 main
otus@otus-db-pg-vm-1:~$ sudo pg_createcluster 15 main
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
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
otus@otus-db-pg-vm-1:~$ sudo systemctl start postgresql@15-main
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

*2 ВМ*
    
```
otus@otus-db-pg-vm-2:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease [109 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,462 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [285 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/restricted amd64 Packages [1,559 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/restricted Translation-en [259 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,057 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/universe Translation-en [239 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-backports/main amd64 Packages [67.1 kB]
Get:12 http://mirror.yandex.ru/ubuntu jammy-backports/main Translation-en [11.0 kB]
Get:13 http://mirror.yandex.ru/ubuntu jammy-backports/universe amd64 Packages [28.4 kB]
Get:14 http://mirror.yandex.ru/ubuntu jammy-backports/universe Translation-en [16.6 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,246 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [225 kB]
Get:17 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1,531 kB]
Get:18 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [255 kB]
Get:19 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [850 kB]
Get:20 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [162 kB]
Fetched 9,590 kB in 3s (3,712 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
7 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following NEW packages will be installed:
  ubuntu-pro-client
The following packages have been kept back:
  libldap-2.5-0 libldap-common
The following packages will be upgraded:
  libexpat1 python3-update-manager ubuntu-advantage-tools ubuntu-pro-client-l10n update-manager-core
5 upgraded, 1 newly installed, 0 to remove and 2 not upgraded.
1 standard LTS security update
Need to get 370 kB of archives.
After this operation, 36.9 kB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libexpat1 amd64 2.4.7-1ubuntu0.3 [91.0 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-advantage-tools all 31.2~22.04 [10.8 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-pro-client amd64 31.2~22.04 [197 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-pro-client-l10n amd64 31.2~22.04 [20.3 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 python3-update-manager all 1:22.04.19 [39.1 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 update-manager-core all 1:22.04.19 [11.6 kB]
Fetched 370 kB in 0s (26.4 MB/s)
Preconfiguring packages ...
(Reading database ... 110047 files and directories currently installed.)
Preparing to unpack .../0-libexpat1_2.4.7-1ubuntu0.3_amd64.deb ...
Unpacking libexpat1:amd64 (2.4.7-1ubuntu0.3) over (2.4.7-1ubuntu0.2) ...
Preparing to unpack .../1-ubuntu-advantage-tools_31.2~22.04_all.deb ...
Unpacking ubuntu-advantage-tools (31.2~22.04) over (30~22.04) ...
dpkg: warning: unable to delete old directory '/var/lib/ubuntu-advantage': Directory not empty
dpkg: warning: unable to delete old directory '/etc/ubuntu-advantage': Directory not empty
Selecting previously unselected package ubuntu-pro-client.
Preparing to unpack .../2-ubuntu-pro-client_31.2~22.04_amd64.deb ...
Unpacking ubuntu-pro-client (31.2~22.04) ...
Preparing to unpack .../3-ubuntu-pro-client-l10n_31.2~22.04_amd64.deb ...
Unpacking ubuntu-pro-client-l10n (31.2~22.04) over (30~22.04) ...
Preparing to unpack .../4-python3-update-manager_1%3a22.04.19_all.deb ...
Unpacking python3-update-manager (1:22.04.19) over (1:22.04.18) ...
Preparing to unpack .../5-update-manager-core_1%3a22.04.19_all.deb ...
Unpacking update-manager-core (1:22.04.19) over (1:22.04.18) ...
Setting up libexpat1:amd64 (2.4.7-1ubuntu0.3) ...
Setting up python3-update-manager (1:22.04.19) ...
Setting up ubuntu-pro-client (31.2~22.04) ...
Setting up ubuntu-pro-client-l10n (31.2~22.04) ...
Setting up ubuntu-advantage-tools (31.2~22.04) ...
Setting up update-manager-core (1:22.04.19) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
 systemctl restart polkit.service
Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart unattended-upgrades.service

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
Fetched 419 kB in 1s (538 kB/s)
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
0 upgraded, 14 newly installed, 0 to remove and 2 not upgraded.
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
(Reading database ... 110055 files and directories currently installed.)
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
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart unattended-upgrades.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
otus@otus-db-pg-vm-2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

*3 ВМ*
    
```
otus@otus-vm-db-pg-net-3:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease [109 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,462 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [285 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/restricted amd64 Packages [1,559 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/restricted Translation-en [259 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,057 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/universe Translation-en [239 kB]
Get:11 http://mirror.yandex.ru/ubuntu jammy-backports/main amd64 Packages [67.1 kB]
Get:12 http://mirror.yandex.ru/ubuntu jammy-backports/main Translation-en [11.0 kB]
Get:13 http://mirror.yandex.ru/ubuntu jammy-backports/universe amd64 Packages [28.4 kB]
Get:14 http://mirror.yandex.ru/ubuntu jammy-backports/universe Translation-en [16.6 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,246 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [225 kB]
Get:17 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1,531 kB]
Get:18 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [255 kB]
Get:19 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [850 kB]
Get:20 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [162 kB]
Fetched 9,590 kB in 2s (5,697 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
7 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following NEW packages will be installed:
  ubuntu-pro-client
The following packages will be upgraded:
  libexpat1 libldap-2.5-0 libldap-common python3-update-manager ubuntu-advantage-tools ubuntu-pro-client-l10n
  update-manager-core
7 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
1 standard LTS security update
Need to get 569 kB of archives.
After this operation, 36.9 kB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libexpat1 amd64 2.4.7-1ubuntu0.3 [91.0 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-advantage-tools all 31.2~22.04 [10.8 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-pro-client amd64 31.2~22.04 [197 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 ubuntu-pro-client-l10n amd64 31.2~22.04 [20.3 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 python3-update-manager all 1:22.04.19 [39.1 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 update-manager-core all 1:22.04.19 [11.6 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libldap-2.5-0 amd64 2.5.17+dfsg-0ubuntu0.22.04.1 [183 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libldap-common all 2.5.17+dfsg-0ubuntu0.22.04.1 [15.8 kB]
Fetched 569 kB in 0s (35.6 MB/s)
Preconfiguring packages ...
(Reading database ... 110047 files and directories currently installed.)
Preparing to unpack .../0-libexpat1_2.4.7-1ubuntu0.3_amd64.deb ...
Unpacking libexpat1:amd64 (2.4.7-1ubuntu0.3) over (2.4.7-1ubuntu0.2) ...
Preparing to unpack .../1-ubuntu-advantage-tools_31.2~22.04_all.deb ...
Unpacking ubuntu-advantage-tools (31.2~22.04) over (30~22.04) ...
dpkg: warning: unable to delete old directory '/var/lib/ubuntu-advantage': Directory not empty
dpkg: warning: unable to delete old directory '/etc/ubuntu-advantage': Directory not empty
Selecting previously unselected package ubuntu-pro-client.
Preparing to unpack .../2-ubuntu-pro-client_31.2~22.04_amd64.deb ...
Unpacking ubuntu-pro-client (31.2~22.04) ...
Preparing to unpack .../3-ubuntu-pro-client-l10n_31.2~22.04_amd64.deb ...
Unpacking ubuntu-pro-client-l10n (31.2~22.04) over (30~22.04) ...
Preparing to unpack .../4-python3-update-manager_1%3a22.04.19_all.deb ...
Unpacking python3-update-manager (1:22.04.19) over (1:22.04.18) ...
Preparing to unpack .../5-update-manager-core_1%3a22.04.19_all.deb ...
Unpacking update-manager-core (1:22.04.19) over (1:22.04.18) ...
Preparing to unpack .../6-libldap-2.5-0_2.5.17+dfsg-0ubuntu0.22.04.1_amd64.deb ...
Unpacking libldap-2.5-0:amd64 (2.5.17+dfsg-0ubuntu0.22.04.1) over (2.5.16+dfsg-0ubuntu0.22.04.2) ...
Preparing to unpack .../7-libldap-common_2.5.17+dfsg-0ubuntu0.22.04.1_all.deb ...
Unpacking libldap-common (2.5.17+dfsg-0ubuntu0.22.04.1) over (2.5.16+dfsg-0ubuntu0.22.04.2) ...
Setting up libexpat1:amd64 (2.4.7-1ubuntu0.3) ...
Setting up libldap-common (2.5.17+dfsg-0ubuntu0.22.04.1) ...
Setting up libldap-2.5-0:amd64 (2.5.17+dfsg-0ubuntu0.22.04.1) ...
Setting up python3-update-manager (1:22.04.19) ...
Setting up ubuntu-pro-client (31.2~22.04) ...
Setting up ubuntu-pro-client-l10n (31.2~22.04) ...
Setting up ubuntu-advantage-tools (31.2~22.04) ...
Setting up update-manager-core (1:22.04.19) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
 systemctl restart packagekit.service polkit.service
Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart unattended-upgrades.service

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
Fetched 419 kB in 1s (555 kB/s)
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
Fetched 45.4 MB in 3s (14.7 MB/s)
Preconfiguring packages ...
Selecting previously unselected package libjson-perl.
(Reading database ... 110055 files and directories currently installed.)
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
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning candidates...
Scanning linux images...

Running kernel seems to be up-to-date.

Restarting services...
Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart unattended-upgrades.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
otus@otus-vm-db-pg-net-3:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

* Настраиваем файлы конфигурации на всех ВМ: слушать все адреса listen_addresses = '\*' командой sudo pg_conftool 15 main set listen_addresses '*' и /etc/postgresql/15/main/pg_hba.conf
```
# IPv4 local connections:
host    all             all             0.0.0.0/0            trust
```

* Проверяем параметр сервера wal_level, устанавливаем logical и перезапускаем кластеры на всех ВМ

*1 ВМ*
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 replica
(1 row)

postgres=# alter system set wal_level = logical;
ALTER SYSTEM
postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 logical
(1 row)
```

*2 ВМ*
```
otus@otus-db-pg-vm-2:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 replica
(1 row)

postgres=# alter system set wal_level = logical;
ALTER SYSTEM
postgres=# \q
otus@otus-db-pg-vm-2:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-2:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 logical
(1 row)
```

*3 ВМ*
```
otus@otus-vm-db-pg-net-3:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 replica
(1 row)

postgres=# alter system set wal_level = logical;
ALTER SYSTEM
postgres=# \q
otus@otus-vm-db-pg-net-3:~$ sudo pg_ctlcluster 15 main restart
otus@otus-vm-db-pg-net-3:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show wal_level;
 wal_level
-----------
 logical
(1 row)

```
> 1. На 1 ВМ создаем таблицы test для записи, test2 для запросов на чтение.
> 3. Создаем публикацию таблицы test и подписываемся на публикацию таблицы test2 с ВМ №2.
> 5. На 2 ВМ создаем таблицы test2 для записи, test для запросов на чтение.
> 6. Создаем публикацию таблицы test2 и подписываемся на публикацию таблицы test1 с ВМ №1.
> 7. 3 ВМ использовать как реплику для чтения и бэкапов (подписаться на таблицы из ВМ №1 и №2 ).

*Сделаем скопом немного подругому (результат будет тот же, просто последовательность работы другая) так как у меня постоянно отваливаются соединения к ВМ и переподключаться к ним уже немного надоело каждый раз)))*

#### * На всех ВМ создаём по 2 таблицы test, test2 сразу

*1 ВМ*
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# create table test (i int);
CREATE TABLE
postgres=#  create table test2 (i int);
CREATE TABLE
postgres=# \dt
         List of relations
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | test  | table | postgres
 public | test2 | table | postgres
(2 rows)
```

*2 ВМ*
```
otus@otus-db-pg-vm-2:~$  sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# create table test (i int);
CREATE TABLE
postgres=#  create table test2 (i int);
CREATE TABLE
postgres=# \dt
         List of relations
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | test  | table | postgres
 public | test2 | table | postgres
(2 rows)

```

*3 ВМ*
```
otus@otus-vm-db-pg-net-3:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# create table test (i int);
CREATE TABLE
postgres=# create table test2 (i int);
CREATE TABLE
postgres=# \dt
         List of relations
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | test  | table | postgres
 public | test2 | table | postgres
(2 rows)
```

#### * Создаём публикации. На 1-й ВМ таблицы test. На 2-ой ВМ таблицы test2

*1 ВМ*
```
postgres=# create publication test_pub for table test;
CREATE PUBLICATION
postgres=# \dRp+
                            Publication test_pub
  Owner   | All tables | Inserts | Updates | Deletes | Truncates | Via root
----------+------------+---------+---------+---------+-----------+----------
 postgres | f          | t       | t       | t       | t         | f
Tables:
    "public.test"
```

*2 ВМ*
```
postgres=# create publication test_pub for table test2;
CREATE PUBLICATION
postgres=# \dRp+
                            Publication test_pub
  Owner   | All tables | Inserts | Updates | Deletes | Truncates | Via root
----------+------------+---------+---------+---------+-----------+----------
 postgres | f          | t       | t       | t       | t         | f
Tables:
    "public.test2"
```

#### * Создаём подписки. На 1-й ВМ на публикацию test_pub 2-ой ВМ. На 2-ой ВМ на публикацию test_pub 1-ой ВМ. На 3-ей ВМ на публикацию test_pub 1-ой ВМ и test_pub 2-й ВМ.

*1 ВМ*
```
postgres=# create subscription test_sub
connection 'host=158.160.86.74 port=5432 user=postgres dbname=postgres'
publication test_pub with (copy_data = false);
NOTICE:  created replication slot "test_sub" on publisher
CREATE SUBSCRIPTION
postgres=# \dRs+
List of subscriptions
   Name   |  Owner   | Enabled | Publication | Binary | Streaming | Two-phase commit | Disable on error | Synchronous commit |                          Conninfo                          | Skip LSN
----------+----------+---------+-------------+--------+-----------+------------------+------------------+--------------------+------------------------------------------------------------+----------
 test_sub | postgres | t       | {test_pub}  | f      | f         | d                | f                | off                | host=158.160.86.74 port=5432 user=postgres dbname=postgres | 0/0
(1 row)
```

*2 ВМ*
```
postgres=# create subscription test_sub
connection 'host=158.160.67.175 port=5432 user=postgres dbname=postgres'
publication test_pub with (copy_data = false);
NOTICE:  created replication slot "test_sub" on publisher
CREATE SUBSCRIPTION
postgres=# \dRs+
List of subscriptions
   Name   |  Owner   | Enabled | Publication | Binary | Streaming | Two-phase commit | Disable on error | Synchronous commit |
       Conninfo                           | Skip LSN
----------+----------+---------+-------------+--------+-----------+------------------+------------------+--------------------+-------------------------------------------------------------+----------
 test_sub | postgres | t       | {test_pub}  | f      | f         | d                | f                | off                | host=158.160.67.175 port=5432 user=postgres dbname=postgres | 0/0
(1 row)
```

*3 ВМ*
```
postgres=# create subscription test_sub1
connection 'host=158.160.67.175 port=5432 user=postgres dbname=postgres'
publication test_pub with (copy_data = false);
NOTICE:  created replication slot "test_sub1" on publisher
CREATE SUBSCRIPTION
postgres=# create subscription test_sub2
connection 'host=158.160.86.74 port=5432 user=postgres dbname=postgres'
publication test_pub with (copy_data = false);
NOTICE:  created replication slot "test_sub2" on publisher
CREATE SUBSCRIPTION
postgres=# \dRs+
List of subscriptions
   Name    |  Owner   | Enabled | Publication | Binary | Streaming | Two-phase commit | Disable on error | Synchronous commit |
        Conninfo                           | Skip LSN
-----------+----------+---------+-------------+--------+-----------+------------------+------------------+--------------------+-------------------------------------------------------------+----------
 test_sub1 | postgres | t       | {test_pub}  | f      | f         | d                | f                | off                | host=158.160.67.175 port=5432 user=postgres dbname=postgres | 0/0
 test_sub2 | postgres | t       | {test_pub}  | f      | f         | d                | f                | off                | host=158.160.86.74 port=5432 user=postgres dbname=postgres  | 0/0
(2 rows)
```
#### * Проверяем что натворили (или нет)

##### 1. Немного подготовки, вставим данные на 1 ВМ в таблицу test и данные на 2 ВМ в таблицу test2 (когда делала для test накосячила с INSERT INTO, хотела от 1 до 10, а получился рандом какой-то странный, но для теста это не важно)

*1 ВМ*
```
postgres=# INSERT INTO test(i) SELECT random() FROM generate_series(1, 10);
INSERT 0 10
postgres=# select * from test;
 i
---
 0
 0
 1
 1
 0
 1
 0
 0
 0
 0
(10 rows)

postgres=# INSERT INTO test(i) SELECT generate_series(1, 10);
INSERT 0 10
postgres=# select * from test;
 i
----
  0
  0
  1
  1
  0
  1
  0
  0
  0
  0
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
(20 rows)
```

*2 ВМ*
```
postgres=# INSERT INTO test2(i) SELECT generate_series(11, 20);
INSERT 0 10
postgres=# select * from test2;
 i
----
 11
 12
 13
 14
 15
 16
 17
 18
 19
 20
(10 rows)
```
##### 2. На 1-й ВМ смотрим test2 со второй, на 2-й test с первой, на 3-й test с 1-й и test2 со 2-й.

*1 ВМ.*
```
postgres=# select * from public.test2;
 i
----
 11
 12
 13
 14
 15
 16
 17
 18
 19
 20
(10 rows)
```

*2 ВМ*
```
postgres=# select * from public.test;
 i
----
  0
  0
  1
  1
  0
  1
  0
  0
  0
  0
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
(20 rows)
```

*3 ВМ*
```
postgres=# select * from public.test;
 i
----
  0
  0
  1
  1
  0
  1
  0
  0
  0
  0
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
(20 rows)

postgres=# select * from public.test2;
 i
----
 11
 12
 13
 14
 15
 16
 17
 18
 19
 20
(10 rows)
```
*Все работает, все получилось* 

### * реализовать горячее реплицирование для высокой доступности на 4ВМ. Источником должна выступать ВМ №3. Написать с какими проблемами столкнулись.
```

```

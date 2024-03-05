# Логический уровень PostgreSQL 
## создайте новый кластер PostgresSQL 14
```
otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-14
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,422 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [279 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,052 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,206 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [219 kB]
Get:10 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [846 kB]
Fetched 5,253 kB in 2s (3,231 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following packages will be upgraded:
  cloud-init python3-cryptography tzdata
3 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
2 standard LTS security updates
Need to get 1,136 kB of archives.
After this operation, 4,096 B of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 tzdata all 2024a-0ubuntu0.22.04 [348 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 python3-cryptography amd64 3.4.8-1ubuntu2.2 [236 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 cloud-init all 23.4.4-0ubuntu0~22.04.1 [551 kB]
Fetched 1,136 kB in 0s (55.8 MB/s)
Preconfiguring packages ...
(Reading database ... 110044 files and directories currently installed.)
Preparing to unpack .../tzdata_2024a-0ubuntu0.22.04_all.deb ...
Unpacking tzdata (2024a-0ubuntu0.22.04) over (2023d-0ubuntu0.22.04) ...
Preparing to unpack .../python3-cryptography_3.4.8-1ubuntu2.2_amd64.deb ...
Unpacking python3-cryptography (3.4.8-1ubuntu2.2) over (3.4.8-1ubuntu2.1) ...
Preparing to unpack .../cloud-init_23.4.4-0ubuntu0~22.04.1_all.deb ...
Unpacking cloud-init (23.4.4-0ubuntu0~22.04.1) over (23.4.3-0ubuntu0~22.04.1) ...
Setting up cloud-init (23.4.4-0ubuntu0~22.04.1) ...
Setting up tzdata (2024a-0ubuntu0.22.04) ...

Current default time zone: 'Etc/UTC'
Local time is now:      Mon Mar  4 22:30:23 UTC 2024.
Universal Time is now:  Mon Mar  4 22:30:23 UTC 2024.
Run 'dpkg-reconfigure tzdata' if you wish to change it.

Setting up python3-cryptography (3.4.8-1ubuntu2.2) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for rsyslog (8.2112.0-2ubuntu2.2) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

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
Fetched 419 kB in 1s (329 kB/s)
Reading package lists... Done
W: http://apt.postgresql.org/pub/repos/apt/dists/jammy-pgdg/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-client-14 postgresql-client-common postgresql-common ssl-cert sysstat
Suggested packages:
  lm-sensors postgresql-doc-14 isag
The following NEW packages will be installed:
  libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5
  libtypes-serialiser-perl postgresql-14 postgresql-client-14 postgresql-client-common postgresql-common ssl-cert
  sysstat
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 44.7 MB of archives.
After this operation, 180 MB of additional disk space will be used.
Get:1 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-perl all 4.04000-1 [81.8 kB]
Get:2 http://mirror.yandex.ru/ubuntu jammy/main amd64 ssl-cert all 1.1.2 [17.4 kB]
Get:3 http://mirror.yandex.ru/ubuntu jammy/main amd64 libcommon-sense-perl amd64 3.75-2build1 [21.1 kB]
Get:4 http://mirror.yandex.ru/ubuntu jammy/main amd64 libtypes-serialiser-perl all 1.01-1 [11.6 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy/main amd64 libjson-xs-perl amd64 4.030-1build3 [87.2 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 libllvm15 amd64 1:15.0.7-0ubuntu0.22.04.3 [25.4 MB]
Get:7 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-common all 257.pgdg22.04+1 [94.3 kB]
Get:8 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-common all 257.pgdg22.04+1 [239 kB]
Get:9 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 libpq5 amd64 16.2-1.pgdg22.04+1 [214 kB]
Get:10 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-14 amd64 14.11-1.pgdg22.04+1 [1,621 kB]
Get:11 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-14 amd64 14.11-1.pgdg22.04+1 [16.4 MB]
Get:12 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors-config all 1:3.6.0-7ubuntu1 [5,274 B]
Get:13 http://mirror.yandex.ru/ubuntu jammy/main amd64 libsensors5 amd64 1:3.6.0-7ubuntu1 [26.3 kB]
Get:14 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 sysstat amd64 12.5.2-2ubuntu0.2 [487 kB]
Fetched 44.7 MB in 3s (13.9 MB/s)
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
Selecting previously unselected package postgresql-client-14.
Preparing to unpack .../11-postgresql-client-14_14.11-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-client-14 (14.11-1.pgdg22.04+1) ...
Selecting previously unselected package postgresql-14.
Preparing to unpack .../12-postgresql-14_14.11-1.pgdg22.04+1_amd64.deb ...
Unpacking postgresql-14 (14.11-1.pgdg22.04+1) ...
Selecting previously unselected package sysstat.
Preparing to unpack .../13-sysstat_12.5.2-2ubuntu0.2_amd64.deb ...
Unpacking sysstat (12.5.2-2ubuntu0.2) ...
Setting up postgresql-client-common (257.pgdg22.04+1) ...
Setting up libsensors-config (1:3.6.0-7ubuntu1) ...
Setting up libpq5:amd64 (16.2-1.pgdg22.04+1) ...
Setting up libcommon-sense-perl:amd64 (3.75-2build1) ...
Setting up postgresql-client-14 (14.11-1.pgdg22.04+1) ...
update-alternatives: using /usr/share/postgresql/14/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
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
Setting up postgresql-14 (14.11-1.pgdg22.04+1) ...
Creating new PostgreSQL cluster 14/main ...
/usr/lib/postgresql/14/bin/initdb -D /var/lib/postgresql/14/main --auth-local peer --auth-host scram-sha-256 --no-instru
ctions
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/14/main ... ok
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
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```
## зайдите в созданный кластер под пользователем postgres
```
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (14.11 (Ubuntu 14.11-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
## создайте новую базу данных testdb
```
postgres=# CREATE DATABASE testdb;
CREATE DATABASE
```
## зайдите в созданную базу данных под пользователем postgres
```
postgres=# \c testdb
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "postgres".
```
## создайте новую схему testnm
```
testdb=# CREATE SCHEMA testnm;
CREATE SCHEMA
```
## создайте новую таблицу t1 с одной колонкой c1 типа integer
```
testdb=# CREATE TABLE t1(c1 integer);
CREATE TABLE
```
## вставьте строку со значением c1=1
```
testdb=# INSERT INTO t1 values(1);
INSERT 0 1
```
## создайте новую роль readonly
```
testdb=# CREATE role readonly;
CREATE ROLE
```
## дайте новой роли право на подключение к базе данных testdb
```
testdb=# GRANT connect on DATABASE testdb TO readonly;
GRANT
```
## дайте новой роли право на использование схемы testnm
```
testdb=# GRANT usage on SCHEMA testnm to readonly;
GRANT
```
## дайте новой роли право на select для всех таблиц схемы testnm
```
testdb=# GRANT SELECT on all TABLEs in SCHEMA testnm TO readonly;
GRANT
```
## создайте пользователя testread с паролем test123
```
testdb=# CREATE USER testread with password 'test123';
CREATE ROLE
```
## дайте роль readonly пользователю testread
```
testdb=# grant readonly TO testread;
GRANT ROLE
```
## зайдите под пользователем testread в базу данных testdb
```
testdb=# \c testdb testread
Password for user testread:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=>
```
## сделайте select * from t1;
```
testdb=> SELECT * FROM t1;
ERROR:  permission denied for table t1
testdb=> \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | t1   | table | postgres
(1 row)
```
получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)
напишите что именно произошло в тексте домашнего задания
у вас есть идеи почему? ведь права то дали?

*Когда создавали таблицу она создалась в схеме public а не testnm, а пользователю testread мы дали права на select всех таблиц в схеме testnm*
## посмотрите на список таблиц
подсказка в шпаргалке под пунктом 20
```
testdb=> \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | t1   | table | postgres
(1 row)
```
а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)
```
postgres=# show search_path;
   search_path
-----------------
 "$user", public
(1 row)
```
*Если посмотрим search_path то сначала идет $user, потом public. Так как схемы $user у нас нет, то по умолчанию создается в public, это было в вебинаре, можно поменять эти параметры по умолчанию.*
## вернитесь в базу данных testdb под пользователем postgres
```
postgres=# \c testdb postgres
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "postgres".
testdb=#
```
## удалите таблицу t1
```
testdb=# DROP TABLE t1;
DROP TABLE
```
## создайте ее заново но уже с явным указанием имени схемы testnm
```
testdb=# CREATE TABLE testnm.t1(c1 integer);
CREATE TABLE
```
## вставьте строку со значением c1=1
```
testdb=# INSERT INTO testnm.t1 values(1);
INSERT 0 1
testdb=# SELECT * FROM testnm.t1;
 c1
----
  1
(1 row)
```
## зайдите под пользователем testread в базу данных testdb
```
testdb=# \c testdb testread
Password for user testread:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=>
```
## сделайте select * from testnm.t1;
```
testdb=> select * from testnm.t1;
ERROR:  permission denied for table t1
```
получилось?

*Пишет, что у пользователя нет прав просмотра таблицы t1, но она получается существует в базе*

есть идеи почему? если нет - смотрите шпаргалку

*Потому что мы создали таблицу после раздачи прав на select всех таблиц пользователю testread.*

как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку

*Нужно снова раздать права на чтение всех таблиц в базе пользователю. Нужно использовать ALTER default, который даст права и новым таблицам созданным в базе, а не grant SELECT, который даёт права только на те таблицы, которые на данный момент существуют в базе.*

```
testdb=> \c testdb postgres
Password for user postgres:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "postgres".
testdb=# ALTER default privileges in SCHEMA testnm grant SELECT on TABLES to readonly;
ALTER DEFAULT PRIVILEGES
testdb=# \c testdb testread;
Password for user testread:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=>
```
## сделайте select * from testnm.t1;
```
testdb=> select * from testnm.t1;
ERROR:  permission denied for table t1
```
получилось?

*А, мы сделали ALTER default, значит надо пересоздать таблицу, она была уже на момент выполнения этой команды, значит для неё надо было повторить grant SELECT или в данный момент просто пересоздать таблицу.*

есть идеи почему? если нет - смотрите шпаргалку

*Пересоздадим таблицу, чтобы сработал ALTER defaul*

```
testdb=> \c testdb postgres
Password for user postgres:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "postgres".
testdb=# DROP TABLE testnm.t1;
DROP TABLE
testdb=# CREATE TABLE testnm.t1(c1 integer);
CREATE TABLE
testdb=# \c testdb testread;
Password for user testread:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=> SELECT * FROM testnm.t1;
 c1
----
(0 rows)

testdb=>
```
## сделайте select * from testnm.t1;
```
testdb=> SELECT * FROM testnm.t1;
 c1
----
(0 rows)

testdb=>
```
получилось?

*Да, все ОК, так как теперь пользователь имеет права на чтение таблицы, ведь мы её пересоздали*

ура!
## теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);
```
testdb=> create table t2(c1 integer);
CREATE TABLE
testdb=> insert into t2 values (2);
INSERT 0 1
```
а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?

*Выполнив show search_path; мы выяснили что по умолчанию используется "$user", потом public схемы, так как схемы $user у нас нет, то обращаемся к public. Public создается всегда, соответственно при добавлении нового пользователя роль public автоматически ему присваевается, а значит пользователь, которого мы создали имеет право на создание объектов в схеме public в любой базе данных, а ведь мы разрешили данному пользователю доступ на подключение.* 

есть идеи как убрать эти права? если нет - смотрите шпаргалку

*Воспользуемся подсказкой, пункт 36.*

```
postgres=# \c testdb postgres;
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "postgres".
testdb=# REVOKE CREATE on SCHEMA public FROM public;
REVOKE
testdb=# REVOKE ALL on DATABASE testdb FROM public;
REVOKE
testdb=# \c testdb testread;
Password for user testread:
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=>
```
если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды

*Зайдем под пользователем postgres в testdb. REVOKE лишает прав всех пользователей на создание в схеме public. REVOKE отменяет любые привилегии на объекты базы данных для роли. Отзываем права в базе testdb в схеме public.*

## теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);
```
postgres=> \c testdb testread;
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
You are now connected to database "testdb" as user "testread".
testdb=> create table t3(c1 integer);
ERROR:  permission denied for schema public
LINE 1: create table t3(c1 integer);
                     ^
testdb=> insert into t2 values (2);
INSERT 0 1
testdb=>
```
расскажите что получилось и почему

*Нет прав на создание таблицы у пользователя, так как ранее мы их отозвали для public. А вот строка в таблицу добавилась? Мы её добавляли в таблицу t2, а её соответвенно создавали в предыдущем шаге данным пользователем в схеме public. Проверим*

```
testdb=> SELECT * FROM t2;
 c1
----
  2
  2
(2 rows)
```
*Значит мы можем добавлять данные в уже созданную таблицу определенным пользователем в схеме паблик, даже если после отозвали для него права.*

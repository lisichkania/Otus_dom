# MVCC, vacuum и autovacuum.
## Создать инстанс ВМ с 2 ядрами и 4 Гб ОЗУ и SSD 10GB
*ВМ создана в ЯО SSD ​vCPU-2; RAM-4 ГБ; Объём дискового пространства-10 ГБ*

## Установить на него PostgreSQL 15 с дефолтными настройками
```
otus@otus-db-pg-vm-1:~$ sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
Hit:1 http://mirror.yandex.ru/ubuntu jammy InRelease
Get:2 http://mirror.yandex.ru/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://mirror.yandex.ru/ubuntu jammy-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://mirror.yandex.ru/ubuntu jammy-updates/main amd64 Packages [1,424 kB]
Get:6 http://mirror.yandex.ru/ubuntu jammy-updates/main Translation-en [279 kB]
Get:7 http://mirror.yandex.ru/ubuntu jammy-updates/restricted amd64 Packages [1,508 kB]
Get:8 http://mirror.yandex.ru/ubuntu jammy-updates/restricted Translation-en [249 kB]
Get:9 http://mirror.yandex.ru/ubuntu jammy-updates/universe amd64 Packages [1,052 kB]
Get:10 http://mirror.yandex.ru/ubuntu jammy-updates/universe Translation-en [237 kB]
Get:11 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1,208 kB]
Get:12 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [219 kB]
Get:13 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1,480 kB]
Get:14 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [245 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [847 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [161 kB]
Fetched 9,139 kB in 2s (5,808 kB/s)
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
Fetched 1,136 kB in 0s (58.9 MB/s)
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
Local time is now:      Tue Mar  5 22:37:16 UTC 2024.
Universal Time is now:  Tue Mar  5 22:37:16 UTC 2024.
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
Get:4 http://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease [123 kB]
Hit:5 http://security.ubuntu.com/ubuntu jammy-security InRelease
Get:6 http://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 Packages [296 kB]
Fetched 419 kB in 1s (603 kB/s)
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
Fetched 45.4 MB in 3s (14.8 MB/s)
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
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```
## Создать БД для тестов: выполнить pgbench -i postgres
```
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
done in 0.74 s (drop tables 0.00 s, create tables 0.01 s, client-side generate 0.18 s, vacuum 0.03 s, primary keys 0.53 s).
```
## Запустить pgbench -c8 -P 6 -T 60 -U postgres postgres
```
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c8 -P 6 -T 60 -U postgres postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 581.5 tps, lat 13.706 ms stddev 8.245, 0 failed
progress: 12.0 s, 475.7 tps, lat 16.804 ms stddev 14.859, 0 failed
progress: 18.0 s, 632.7 tps, lat 12.656 ms stddev 6.698, 0 failed
progress: 24.0 s, 626.8 tps, lat 12.756 ms stddev 7.055, 0 failed
progress: 30.0 s, 683.5 tps, lat 11.714 ms stddev 5.966, 0 failed
progress: 36.0 s, 631.2 tps, lat 12.667 ms stddev 6.795, 0 failed
progress: 42.0 s, 446.7 tps, lat 17.914 ms stddev 14.114, 0 failed
progress: 48.0 s, 593.5 tps, lat 13.481 ms stddev 7.299, 0 failed
progress: 54.0 s, 564.2 tps, lat 14.179 ms stddev 15.257, 0 failed
progress: 60.0 s, 651.0 tps, lat 12.281 ms stddev 6.306, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 35328
number of failed transactions: 0 (0.000%)
latency average = 13.586 ms
latency stddev = 9.702 ms
initial connection time = 14.226 ms
tps = 588.710683 (without initial connection time)
```
## Применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла
```
postgres@otus-db-pg-vm-1:/home/otus$ nano /etc/postgresql/15/main/postgresql.conf
```
*Применяем параметры настройки, подписала моменты изменений и понимания, что за настройки мы меняем*

> max_connections = 40 + (Определяет максимальное число одновременных подключений к серверу.)
>
> shared_buffers = 1GB + (25% от общего объема оперативной памяти компьютера) - увеличили
>
> effective_cache_size = 3GB + (Этот параметр влияет на планировщик запросов, а не ограничивает дисковый кэш. Чем выше, тем больше вероятность, что будет применяться сканирование по индексу. Чем ниже, тем более вероятно, что будет выбрано последовательное сканирование.) - уменьшили
>
> maintenance_work_mem = 512MB + (задаёт максимальный объём памяти для операций обслуживания) - увеличили
>
> checkpoint_completion_target = 0.9 + (доля времени между контрольными точками для завершения контрольной точки)
>
> wal_buffers = 16MB + (если у вас много одновременных подключений, то более высокое значение может повысить производительность)
>
> default_statistics_target = 500 + (число записей просматриваемых при сборе статистики по таблицам)
>
> random_page_cost = 4 + (Сам сервер Postgres не знает, какая у нас дисковая подсистема. И с помощью этого параметра мы ему об этом сообщаем.)
>
> effective_io_concurrency = 2 + (допустимое число параллельных операций ввода/вывода)
>
> work_mem = 6553kB + (Максимальный лимит памяти, который выделяется для обработки запросов)
>
> min_wal_size = 4GB + (Этот параметр ограничивает размер WAL снизу)
>
> max_wal_size = 16GB + (Максимальный размер, до которого может вырастать WAL)

## Протестировать заново
```
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c8 -P 6 -T 60 -U postgres postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 653.7 tps, lat 12.197 ms stddev 7.894, 0 failed
progress: 12.0 s, 664.0 tps, lat 12.044 ms stddev 7.007, 0 failed
progress: 18.0 s, 681.8 tps, lat 11.736 ms stddev 6.261, 0 failed
progress: 24.0 s, 499.0 tps, lat 16.012 ms stddev 13.443, 0 failed
progress: 30.0 s, 656.5 tps, lat 12.200 ms stddev 6.820, 0 failed
progress: 36.0 s, 594.7 tps, lat 13.451 ms stddev 7.585, 0 failed
progress: 42.0 s, 606.8 tps, lat 13.183 ms stddev 7.699, 0 failed
progress: 48.0 s, 593.7 tps, lat 13.471 ms stddev 7.444, 0 failed
progress: 54.0 s, 467.2 tps, lat 17.127 ms stddev 15.379, 0 failed
progress: 60.0 s, 667.5 tps, lat 11.977 ms stddev 6.262, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 36517
number of failed transactions: 0 (0.000%)
latency average = 13.142 ms
latency stddev = 8.848 ms
initial connection time = 14.232 ms
tps = 608.600884 (without initial connection time)
```
Что изменилось и почему?
*На старте: За 1 минуту работы с 8-ю одновременными соединениями кластер обработал 35328 транзакций с пропускной способностью примерно 589 транзакций в секунду*

*После применения настроек: За 1 минуту работы с 8-ю одновременными соединениями кластер обработал 36517 транзакций с пропускной способностью примерно 609 транзакций в секунду.*

*Мы смогли увеличить пропускную способность базы данных с 589 TPS до 609 TPS при 8 одновременных соединениях, это примерно на 3%. Думаю потому что в настройках мы увеличили параметры wal_buffers, shared_buffers, maintenance_work_mem и таким образом повысили производительность.*

## Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк
```
postgres=# create table test5(test text);
CREATE TABLE
postgres=# \dt
              List of relations
 Schema |       Name       | Type  |  Owner
--------+------------------+-------+----------
 public | pgbench_accounts | table | postgres
 public | pgbench_branches | table | postgres
 public | pgbench_history  | table | postgres
 public | pgbench_tellers  | table | postgres
 public | test5            | table | postgres
(5 rows)
postgres=# INSERT INTO test5(test) SELECT 'noname' FROM generate_series(1,1000000);
INSERT 0 1000000
```
## Посмотреть размер файла с таблицей
```
postgres=# SELECT pg_size_pretty(pg_TABLE_size('test5'));
 pg_size_pretty
----------------
 35 MB
(1 row)
postgres=# SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs  WHERE relname = 'test5';
 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum
---------+------------+------------+--------+-------------------------------
 test5   |    1000000 |          0 |      0 | 2024-03-08 01:37:09.983705+00
(1 row)
```
## 5 раз обновить все строчки и добавить к каждой строчке любой символ
```
postgres=# update test5 set test = 'noname1';
UPDATE 1000000
postgres=# update test5 set test = 'noname12';
UPDATE 1000000
postgres=# update test5 set test = 'noname123';
UPDATE 1000000
postgres=# update test5 set test = 'noname1234';
UPDATE 1000000
postgres=# update test5 set test = 'noname12345';
UPDATE 1000000
```
## Посмотреть количество мертвых строчек в таблице и когда последний раз приходил автовакуум
```
postgres=# SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs  WHERE relname = 'test5';
 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum
---------+------------+------------+--------+-------------------------------
 test5   |     996313 |    1001574 |    100 | 2024-03-08 01:48:22.367829+00
(1 row)

```
## Подождать некоторое время, проверяя, пришел ли автовакуум
```
postgres=# SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs  WHERE relname = 'test5';
 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum
---------+------------+------------+--------+-------------------------------
 test5   |    1642715 |          0 |      0 | 2024-03-08 01:49:10.540125+00
(1 row)
postgres=# SELECT pg_size_pretty(pg_TABLE_size('test5'));
 pg_size_pretty
----------------
 238 MB
(1 row)
```
## 5 раз обновить все строчки и добавить к каждой строчке любой символ
```
postgres=# update test5 set test = 'noname123456';
UPDATE 1000000
postgres=# update test5 set test = 'noname1234567';
UPDATE 1000000
postgres=# update test5 set test = 'noname12345678';
UPDATE 1000000
postgres=# update test5 set test = 'noname123456789';
UPDATE 1000000
postgres=# update test5 set test = 'noname1234567890';
UPDATE 1000000
```
## Посмотреть размер файла с таблицей
```
postgres=# SELECT pg_size_pretty(pg_TABLE_size('test5'));
 pg_size_pretty
----------------
 238 MB
(1 row)
postgres=# SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs  WHERE relname = 'test5';
 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum
---------+------------+------------+--------+-------------------------------
 test5   |    1001794 |          0 |      0 | 2024-03-08 01:54:12.017963+00
(1 row)
```
## Отключить Автовакуум на конкретной таблице
```
postgres=# ALTER TABLE test5 SET (autovacuum_enabled = off);
ALTER TABLE
```
## 10 раз обновить все строчки и добавить к каждой строчке любой символ
```
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=#
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
postgres=# update test5
set test = CONCAT(test, 'x');
UPDATE 1000000
```
## Посмотреть размер файла с таблицей
```
postgres=# SELECT pg_size_pretty(pg_TABLE_size('test5'));
 pg_size_pretty
----------------
 570 MB
(1 row)

```
Объясните полученный результат
*Автовакуум отключен, при каждом update он создаёт столько же новых строк и таблица раздувается*

Не забудьте включить автовакуум)
## Задание со *:
### Написать анонимную процедуру, в которой в цикле 10 раз обновятся все строчки в искомой таблице.
```

```
Не забыть вывести номер шага цикла.

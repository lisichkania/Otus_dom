# Блокировки
## Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. 
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show log_lock_waits;
 log_lock_waits
----------------
 on
(1 row)

postgres=# show deadlock_timeout;
 deadlock_timeout
------------------
 1s
(1 row)

postgres=# select name, context from pg_settings where name in ('log_lock_waits', 'deadlock_timeout');
       name       |  context
------------------+-----------
 deadlock_timeout | superuser
 log_lock_waits   | superuser
(2 rows)

postgres=# alter system set deadlock_timeout = 200;
ALTER SYSTEM
postgres=# alter system set log_lock_waits = on;
ALTER SYSTEM
postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show log_lock_waits;
 log_lock_waits
----------------
 on
(1 row)

postgres=# show deadlock_timeout;
 deadlock_timeout
------------------
 200ms
(1 row)

```
### Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.
```
postgres=# create database locks;
CREATE DATABASE
postgres=# \c locks
You are now connected to database "locks" as user "postgres".
locks=# CREATE TABLE accounts(
  acc_no integer PRIMARY KEY,
  amount numeric
);
CREATE TABLE
locks=# INSERT INTO accounts VALUES (1,1000.00), (2,2000.00), (3,3000.00);
INSERT 0 3
locks=# BEGIN;
BEGIN
locks=*# UPDATE accounts SET amount = amount - 100.00 WHERE acc_no = 1;
UPDATE 1
```

*Открываем второе подключение*
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# \c locks
You are now connected to database "locks" as user "postgres".
locks=# BEGIN;
BEGIN
locks=*# UPDATE accounts SET amount = amount + 100.00 WHERE acc_no = 1;
```

*Возвращаемся к первому и через какое-то время >200мс делаем commit*
```
locks=*# SELECT pg_sleep(10);
 pg_sleep
----------

(1 row)

locks=*# COMMIT;
COMMIT
locks=# \q
```

*Во втором после закрытия транзакции в первом закрывается подвисший update и так же делаем commit*
```
UPDATE 1
locks=*# COMMIT;
COMMIT
```
*Проверяем журнал*
```
otus@otus-db-pg-vm-1:~$ sudo tail -n 20 /var/log/postgresql/postgresql-15-main.log
2024-03-11 00:25:42.623 UTC [1887] LOG:  shutting down
2024-03-11 00:25:42.623 UTC [2226] postgres@locks FATAL:  the database system is shutting down
2024-03-11 00:25:42.625 UTC [1887] LOG:  checkpoint starting: shutdown immediate
2024-03-11 00:25:42.635 UTC [1887] LOG:  checkpoint complete: wrote 1 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.002 s, sync=0.001 s, total=0.013 s; sync files=1, longest=0.001 s, average=0.001 s; distance=0 kB, estimate=3560 kB
2024-03-11 00:25:42.640 UTC [1886] LOG:  database system is shut down
2024-03-11 00:25:42.775 UTC [2233] LOG:  starting PostgreSQL 15.6 (Ubuntu 15.6-1.pgdg22.04+1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, 64-bit
2024-03-11 00:25:42.776 UTC [2233] LOG:  listening on IPv6 address "::1", port 5432
2024-03-11 00:25:42.776 UTC [2233] LOG:  listening on IPv4 address "127.0.0.1", port 5432
2024-03-11 00:25:42.777 UTC [2233] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2024-03-11 00:25:42.782 UTC [2236] LOG:  database system was shut down at 2024-03-11 00:25:42 UTC
2024-03-11 00:25:42.790 UTC [2233] LOG:  database system is ready to accept connections
2024-03-11 00:29:38.772 UTC [2341] postgres@locks LOG:  process 2341 still waiting for ShareLock on transaction 770 after 200.110 ms
2024-03-11 00:29:38.772 UTC [2341] postgres@locks DETAIL:  Process holding the lock: 2267. Wait queue: 2341.
2024-03-11 00:29:38.772 UTC [2341] postgres@locks CONTEXT:  while updating tuple (0,1) in relation "accounts"
2024-03-11 00:29:38.772 UTC [2341] postgres@locks STATEMENT:  UPDATE accounts SET amount = amount + 100.00 WHERE acc_no = 1;
2024-03-11 00:30:42.863 UTC [2234] LOG:  checkpoint starting: time
2024-03-11 00:30:43.376 UTC [2234] LOG:  checkpoint complete: wrote 7 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.504 s, sync=0.003 s, total=0.513 s; sync files=8, longest=0.002 s, average=0.001 s; distance=14 kB, estimate=14 kB
2024-03-11 00:31:10.408 UTC [2341] postgres@locks LOG:  process 2341 acquired ShareLock on transaction 770 after 91836.193 ms
2024-03-11 00:31:10.408 UTC [2341] postgres@locks CONTEXT:  while updating tuple (0,1) in relation "accounts"
2024-03-11 00:31:10.408 UTC [2341] postgres@locks STATEMENT:  UPDATE accounts SET amount = amount + 100.00 WHERE acc_no = 1;
```

*Вот эта запись 2024-03-11 00:29:38.772 UTC [2341] postgres@locks LOG:  process 2341 still waiting for ShareLock on transaction 770 after 200.110 ms*

## Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах. 
```
locks=# create table children (id int, "name" text);
CREATE TABLE
locks=# insert into children (id, "name") values (1, 'Ivan Ivanov'), (2, 'Petr Petrov');
INSERT 0 2
locks=# begin;
BEGIN
```

*Открываем еще две транзакции, так же делаем begin; смотрим у каждой их них pg_backend_pid и потом во всех трех выполняем команду update children set name = 'Ivan Ivanovich Ivanov' where id = 1; по очереди, в первом она выполняется, в остальных ждёт своей очереди*
```
locks=*# SELECT locktype, relation::REGCLASS, virtualxid AS virtxid, transactionid AS xid, mode, granted
FROM pg_locks WHERE pid = 3475;
   locktype    | relation | virtxid | xid |       mode       | granted
---------------+----------+---------+-----+------------------+---------
1. relation      | pg_locks |         |     | AccessShareLock  | t
2. relation      | children |         |     | RowExclusiveLock | t
3. virtualxid    |          | 11/6    |     | ExclusiveLock    | t
4. transactionid |          |         | 787 | ExclusiveLock    | t
(4 rows)

locks=*# SELECT locktype, relation::REGCLASS, virtualxid AS virtxid, transactionid AS xid, mode, granted
FROM pg_locks WHERE pid = 3558;
   locktype    | relation | virtxid | xid |       mode       | granted
---------------+----------+---------+-----+------------------+---------
5. relation      | children |         |     | RowExclusiveLock | t
6. virtualxid    |          | 12/3    |     | ExclusiveLock    | t
7. tuple         | children |         |     | ExclusiveLock    | t
8. transactionid |          |         | 787 | ShareLock        | f
9. transactionid |          |         | 788 | ExclusiveLock    | t
(5 rows)

locks=*# SELECT locktype, relation::REGCLASS, virtualxid AS virtxid, transactionid AS xid, mode, granted
FROM pg_locks WHERE pid = 3621;
   locktype    | relation | virtxid | xid |       mode       | granted
---------------+----------+---------+-----+------------------+---------
10. relation      | children |         |     | RowExclusiveLock | t
11. virtualxid    |          | 13/3    |     | ExclusiveLock    | t
12. transactionid |          |         | 789 | ExclusiveLock    | t
13. tuple         | children |         |     | ExclusiveLock    | f
(4 rows)

```
*Выводим pg_locks для каждой из транзакций по их pg_backend_pid.*

### Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны. 

*В 1-й строке блокировка отношений таблицы pg_locks в режиме AccessShareLock - одновременный доступ только для чтения к таблице pg_locks самостоятельно удерживается первой транзакцией.*

*1. Когда транзакции стартовали, им были присвоены виртуальные идентификаторы virtualxid => 11/6, 12/3, 13/3 и эти номера успешно (granted=t) заблокированы самими транзакциями в режиме исключительной блокировки (ExclusiveLock). 3,6,11 строки*

*2. Транзакции дополнительно получили физические номера transactionid (xid) => 787, 788, 789 при попытке изменить данные командой update, и самостоятельно их удерживают в режиме ExclusiveLock. 4,9,12 строки*

*3. Также из-за команды update появились блокировки с типом relation - блокировки отношений (таблицы children), в режиме RowExclusiveLock, выданы (granted=t), и самостоятельно удерживаются транзакциями. 2,5,10 строки*

*4. Вторая транзакция ожидает завершения первой транзакции - попыталась (granted=f) получить блокировку номера transactionid = 787 (первой транзакции) в режиме ShareLock, 8 строка, и наложила (granted=t) блокировку tuple (блокировка версии строки) на обновляемую строку в режиме ExclusiveLock, 7 строка*

*5. Третья транзакция тоже попыталась (granted=f) получить блокировку tuple на обновляемую строку в режиме ExclusiveLock, но неудачно, так как вторая транзакция уже наложила такую блокировку, 13 строка.*

## Воспроизведите взаимоблокировку трех транзакций. 
```
locks=# create table shops (shops_id integer, shops_name text, quantity integer);
CREATE TABLE
locks=# insert into shops (shops_id, shops_name, quantity)
values
  (1, 'OZON', 1200000),
  (2, 'Wildberries', 2500000),
  (3, 'Yandex', 56987523);
INSERT 0 3
locks=# select * from shops;
 shops_id | shops_name  | quantity
----------+-------------+----------
        1 | OZON        |  1200000
        2 | Wildberries |  2500000
        3 | Yandex      | 56987523
(3 rows)

locks=#
locks=# begin;
BEGIN
locks=*# update shops
set quantity = quantity + 100000
where shops_id = 1;
UPDATE 1
```

*Создали таблилцу магазины, заполнили данными, в первом подключении сделали update 1-й строки. Создадим второе подключение с update 2-й строки*
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# \c locks
You are now connected to database "locks" as user "postgres".
locks=#         begin;
BEGIN
locks=*# update shops
set quantity = quantity + 200000
where shops_id = 2;
UPDATE 1
```

*Создадим 3-е подключение с update 3-ей строки*
```
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# \c locks
You are now connected to database "locks" as user "postgres".
locks=#         begin;
BEGIN
locks=*# update shops
set quantity = quantity + 300000
where shops_id = 3;
UPDATE 1
```

*В первом подключении сделаем update 2-й строки, транзакция уйдет в очередь, ведь вторая строка обновляется во втором подключении*
```
locks=*# update shops
set quantity = quantity - 200000
where shops_id = 2;
```

*Во втором подключении так же идем в очередь на обновление 3-й строки*
```
locks=*# update shops
set quantity = quantity - 300000
where shops_id = 3;
```

*В третьем подключении обновляем 1-ю строку и получаем взаимоблокировку трёх транзакций с ошибкой. После commit тут делается rollback*
```
locks=*# update shops
set quantity = quantity - 100000
where shops_id = 1;
ERROR:  deadlock detected
DETAIL:  Process 3067 waits for ShareLock on transaction 777; blocked by process 2877.
Process 2877 waits for ShareLock on transaction 778; blocked by process 2992.
Process 2992 waits for ShareLock on transaction 779; blocked by process 3067.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,1) in relation "shops"
locks=!# commit;
ROLLBACK
```

Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?
```
otus@otus-db-pg-vm-1:~$ sudo tail -n 50 /var/log/postgresql/postgresql-15-main.log
2024-03-11 01:55:44.067 UTC [2234] LOG:  checkpoint starting: time
2024-03-11 01:55:46.386 UTC [2234] LOG:  checkpoint complete: wrote 24 buffers (0.1%); 0 WAL file(s) added, 0 removed, 0 recycled; write=2.308 s, sync=0.003 s, total=2.319 s; sync files=22, longest=0.002 s, average=0.001 s; distance=114 kB, estimate=140 kB
2024-03-11 01:58:11.838 UTC [2877] postgres@locks LOG:  process 2877 still waiting for ShareLock on transaction 778 after 200.102 ms
2024-03-11 01:58:11.838 UTC [2877] postgres@locks DETAIL:  Process holding the lock: 2992. Wait queue: 2877.
2024-03-11 01:58:11.838 UTC [2877] postgres@locks CONTEXT:  while updating tuple (0,2) in relation "shops"
2024-03-11 01:58:11.838 UTC [2877] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 200000
        where shops_id = 2;
2024-03-11 01:58:38.298 UTC [2992] postgres@locks LOG:  process 2992 still waiting for ShareLock on transaction 779 after 200.111 ms
2024-03-11 01:58:38.298 UTC [2992] postgres@locks DETAIL:  Process holding the lock: 3067. Wait queue: 2992.
2024-03-11 01:58:38.298 UTC [2992] postgres@locks CONTEXT:  while updating tuple (0,3) in relation "shops"
2024-03-11 01:58:38.298 UTC [2992] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 300000
        where shops_id = 3;
2024-03-11 01:59:05.607 UTC [3067] postgres@locks LOG:  process 3067 detected deadlock while waiting for ShareLock on transaction 777 after 200.097 ms
2024-03-11 01:59:05.607 UTC [3067] postgres@locks DETAIL:  Process holding the lock: 2877. Wait queue: .
2024-03-11 01:59:05.607 UTC [3067] postgres@locks CONTEXT:  while updating tuple (0,1) in relation "shops"
2024-03-11 01:59:05.607 UTC [3067] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 100000
        where shops_id = 1;
2024-03-11 01:59:05.607 UTC [3067] postgres@locks ERROR:  deadlock detected
2024-03-11 01:59:05.607 UTC [3067] postgres@locks DETAIL:  Process 3067 waits for ShareLock on transaction 777; blocked by process 2877.
        Process 2877 waits for ShareLock on transaction 778; blocked by process 2992.
        Process 2992 waits for ShareLock on transaction 779; blocked by process 3067.
        Process 3067: update shops
        set quantity = quantity - 100000
        where shops_id = 1;
        Process 2877: update shops
        set quantity = quantity - 200000
        where shops_id = 2;
        Process 2992: update shops
        set quantity = quantity - 300000
        where shops_id = 3;
2024-03-11 01:59:05.607 UTC [3067] postgres@locks HINT:  See server log for query details.
2024-03-11 01:59:05.607 UTC [3067] postgres@locks CONTEXT:  while updating tuple (0,1) in relation "shops"
2024-03-11 01:59:05.607 UTC [3067] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 100000
        where shops_id = 1;
2024-03-11 01:59:05.607 UTC [2992] postgres@locks LOG:  process 2992 acquired ShareLock on transaction 779 after 27508.803 ms
2024-03-11 01:59:05.607 UTC [2992] postgres@locks CONTEXT:  while updating tuple (0,3) in relation "shops"
2024-03-11 01:59:05.607 UTC [2992] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 300000
        where shops_id = 3;
2024-03-11 02:00:14.954 UTC [2877] postgres@locks LOG:  process 2877 acquired ShareLock on transaction 778 after 123315.373 ms
2024-03-11 02:00:14.954 UTC [2877] postgres@locks CONTEXT:  while updating tuple (0,2) in relation "shops"
2024-03-11 02:00:14.954 UTC [2877] postgres@locks STATEMENT:  update shops
        set quantity = quantity - 200000
        where shops_id = 2;
2024-03-11 02:00:44.395 UTC [2234] LOG:  checkpoint starting: time
2024-03-11 02:00:44.507 UTC [2234] LOG:  checkpoint complete: wrote 2 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.102 s, sync=0.003 s, total=0.112 s; sync files=2, longest=0.003 s, average=0.002 s; distance=1 kB, estimate=126 kB
```
*в ситуации разобраться можно, посмотрев внимательно лог. Видно, что deadlock зафиксирован и все процессы до и после него тоже.*

## Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?

*Взаимоблокировка 2-х транзакций, выполняющих UPDATE одной и той же таблицы (без where) возможна, если одна команда будет обновлять строки таблицы в прямом порядке, а другая - в обратном*

## Задание со звездочкой* Попробуйте воспроизвести такую ситуацию.
*Продемонстрировать эту ситуацию можно с помощью курсоров, они дают возможность управлять порядком чтения*
```
locks=# begin;
BEGIN
locks=*# declare cur1 cursor for
select shops_id, shops_name, quantity from shops
order by shops_id for update;
DECLARE CURSOR
```

*Во втором терминале в обратном порядке*
```
locks=# begin;
BEGIN
locks=*# declare cur2 cursor for
select shops_id, shops_name, quantity from shops
order by shops_id desc for update;
DECLARE CURSOR
```

*Получаем строку в первом терминале*
```
locks=*# fetch cur1;
 shops_id | shops_name | quantity
----------+------------+----------
        1 | OZON       |  1300000
(1 row)
```

*Получаем строку во втором терминале*
```
locks=*# fetch cur2;
 shops_id | shops_name | quantity
----------+------------+----------
        3 | Yandex     | 56687523
(1 row)
```

*Снова в первом*
```
locks=*# fetch cur1;
 shops_id | shops_name  | quantity
----------+-------------+----------
        2 | Wildberries |  2500000
(1 row)
```

*Во втором транзакция будет ожидать снятие блокировки*
```
locks=*# fetch cur2;
```

*В первом выдается ошибка, произошла взаимоблокировка*
```
locks=*# fetch cur1;
ERROR:  deadlock detected
DETAIL:  Process 3280 waits for ShareLock on transaction 781; blocked by process 3364.
Process 3364 waits for ShareLock on transaction 780; blocked by process 3280.
HINT:  See server log for query details.
CONTEXT:  while locking tuple (0,7) in relation "shops"
```

# Установка PostgreSQL
## Cоздать ВМ с Ubuntu 20.04/22.04 или развернуть докер любым удобным способом
 1. ВМ создана в ЯО в первой домашней работе, сделана прерываемой, поэтому по окончании работы просто останавливается, IP постоянный, чтобы не менять его в кодировке.
 2. Подключаемся к ВМ по IP.
```
PS C:\Users\USER> bash
lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ cd ~
lisichkania@LAPTOP-JVR9DUC8:~$  ssh -i ~/.ssh/yc_key otus@158.160.67.175
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-172-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
New release '22.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Mon Feb 26 23:24:21 2024 from 91.108.28.103
```
 3.  Устанавливаем клиент PostgreSQL во втором окне
```
PS C:\Users\USER> bash
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.133.1-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


This message is shown once a day. To disable it please create the
/home/lisichkania/.hushlogin file.
lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ sudo apt-get update
[sudo] password for lisichkania:
Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1192 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [217 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1452 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [239 kB]
Get:10 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [842 kB]
Get:11 http://security.ubuntu.com/ubuntu jammy-security/universe Translation-en [161 kB]
Get:12 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 c-n-f Metadata [16.8 kB]
Get:13 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [37.1 kB]
Get:14 http://security.ubuntu.com/ubuntu jammy-security/multiverse Translation-en [7476 B]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 c-n-f Metadata [260 B]
Get:16 http://archive.ubuntu.com/ubuntu jammy/universe Translation-en [5652 kB]
Get:17 http://archive.ubuntu.com/ubuntu jammy/universe amd64 c-n-f Metadata [286 kB]
Get:18 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [217 kB]
Get:19 http://archive.ubuntu.com/ubuntu jammy/multiverse Translation-en [112 kB]
Get:20 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 c-n-f Metadata [8372 B]
Get:21 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1412 kB]
Get:22 http://archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [277 kB]
Get:23 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [1490 kB]
Get:24 http://archive.ubuntu.com/ubuntu jammy-updates/restricted Translation-en [245 kB]
Get:25 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1049 kB]
Get:26 http://archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [237 kB]
Get:27 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 c-n-f Metadata [22.1 kB]
Get:28 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [42.1 kB]
Get:29 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse Translation-en [10.1 kB]
Get:30 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 c-n-f Metadata [472 B]
Get:31 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [41.7 kB]
Get:32 http://archive.ubuntu.com/ubuntu jammy-backports/main Translation-en [10.5 kB]
Get:33 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 c-n-f Metadata [388 B]
Get:34 http://archive.ubuntu.com/ubuntu jammy-backports/restricted amd64 c-n-f Metadata [116 B]
Get:35 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [24.3 kB]
Get:36 http://archive.ubuntu.com/ubuntu jammy-backports/universe Translation-en [16.5 kB]
Get:37 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 c-n-f Metadata [644 B]
Get:38 http://archive.ubuntu.com/ubuntu jammy-backports/multiverse amd64 c-n-f Metadata [116 B]
Fetched 29.7 MB in 12s (2507 kB/s)
Reading package lists... Done
lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ sudo apt-get install postgresql-client
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libpq5 postgresql-client-14
Suggested packages:
  postgresql-14 postgresql-doc-14
The following NEW packages will be installed:
  libpq5 postgresql-client postgresql-client-14
0 upgraded, 3 newly installed, 0 to remove and 77 not upgraded.
Need to get 1366 kB/1369 kB of archives.
After this operation, 4409 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpq5 amd64 14.11-0ubuntu0.22.04.1 [144 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 postgresql-client-14 amd64 14.11-0ubuntu0.22.04.1 [1222 kB]
Fetched 1366 kB in 2s (698 kB/s)
Selecting previously unselected package libpq5:amd64.
(Reading database ... 24245 files and directories currently installed.)
Preparing to unpack .../libpq5_14.11-0ubuntu0.22.04.1_amd64.deb ...
Unpacking libpq5:amd64 (14.11-0ubuntu0.22.04.1) ...
Selecting previously unselected package postgresql-client-14.
Preparing to unpack .../postgresql-client-14_14.11-0ubuntu0.22.04.1_amd64.deb ...
Unpacking postgresql-client-14 (14.11-0ubuntu0.22.04.1) ...
Selecting previously unselected package postgresql-client.
Preparing to unpack .../postgresql-client_14+238_all.deb ...
Unpacking postgresql-client (14+238) ...
Setting up libpq5:amd64 (14.11-0ubuntu0.22.04.1) ...
Setting up postgresql-client-14 (14.11-0ubuntu0.22.04.1) ...
update-alternatives: using /usr/share/postgresql/14/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up postgresql-client (14+238) ...
Processing triggers for libc-bin (2.35-0ubuntu3.4) ...
/sbin/ldconfig.real: /usr/lib/wsl/lib/libcuda.so.1 is not a symbolic link

lisichkania@LAPTOP-JVR9DUC8:/mnt/c/Users/USER$ cd ~
lisichkania@LAPTOP-JVR9DUC8:~$ psql --version
psql (PostgreSQL) 14.11 (Ubuntu 14.11-0ubuntu0.22.04.1)
```
 4. Проверяем кластеры и Создаем пользователя с паролем и проверяем вход за пользователя
```
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory                Log file
16  main    5432 online postgres /var/lib/postgresql/16/main   /var/log/postgresql/postgresql-16-main.log
16  main13  5434 online postgres /var/lib/postgresql/16/main13 /var/log/postgresql/postgresql-16-main13.log
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql -p 5434
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
Type "help" for help.
postgres=> ALTER USER testpass PASSWORD 'testpass';
ALTER ROLE
postgres=> \q
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql -p 5434 -U testpass -h localhost -d postgres -W
Password:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.
postgres=> \q
```
## поставить на нем Docker Engine
* У меня докер уже был установлен ранее и теперь как я понимаю он обновляется
```
otus@otus-db-pg-vm-1:~$ curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh && rm get-docker.sh && sudo usermod -aG docker $USER
# Executing docker install script, commit: e5543d473431b782227f8908005543bb4389b8de
Warning: the "docker" command appears to already exist on this system.

If you already have Docker installed, this script can cause trouble, which is
why we're displaying this warning and provide the opportunity to cancel the
installation.

If you installed the current Docker package using this script and are using it
again to update Docker, you can safely ignore this message.

You may press Ctrl+C now to abort this script.
+ sleep 20
+ sh -c apt-get update -qq >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
+ sh -c install -m 0755 -d /etc/apt/keyrings
+ sh -c curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" | gpg --dearmor --yes -o /etc/apt/keyrings/docker.gpg
+ sh -c chmod a+r /etc/apt/keyrings/docker.gpg
+ sh -c echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu focal stable" > /etc/apt/sources.list.d/docker.list
+ sh -c apt-get update -qq >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq docker-ce docker-ce-cli containerd.io docker-compose-plugin docker-ce-rootless-extras docker-buildx-plugin >/dev/null
+ sh -c docker version
Client: Docker Engine - Community
 Version:           25.0.3
 API version:       1.44
 Go version:        go1.21.6
 Git commit:        4debf41
 Built:             Tue Feb  6 21:14:17 2024
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          25.0.3
  API version:      1.44 (minimum version 1.24)
  Go version:       go1.21.6
  Git commit:       f417435
  Built:            Tue Feb  6 21:14:17 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.28
  GitCommit:        ae07eda36dd25f8a1b98dfbf587313b99c0190bb
 runc:
  Version:          1.1.12
  GitCommit:        v1.1.12-0-g51d5e94
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

================================================================================

To run Docker as a non-privileged user, consider setting up the
Docker daemon in rootless mode for your user:

    dockerd-rootless-setuptool.sh install

Visit https://docs.docker.com/go/rootless/ to learn about rootless mode.


To run the Docker daemon as a fully privileged service, but granting non-root
users access, refer to https://docs.docker.com/go/daemon-access/

WARNING: Access to the remote API on a privileged Docker daemon is equivalent
         to root access on the host. Refer to the 'Docker daemon attack surface'
         documentation for details: https://docs.docker.com/go/attack-surface/

================================================================================
```
## Создаем docker-сеть
```
otus@otus-db-pg-vm-1:~$ sudo docker network create pg-net
b997b273f77880272d3a01499f0f3c2f333a0bda79f589cc1b1a139d2830bae7
```
## Подключаем созданную сеть к контейнеру сервера Postgres
## сделать каталог /var/lib/postgres
## развернуть контейнер с PostgreSQL 15 смонтировав в него /var/lib/postgresql
* Ранее я уже разворачивала контейнер в докере и после окончания практики удалила его, но код не сохранился и поэтому нужно делать это заново для ДЗ
```
otus@otus-db-pg-vm-1:~$ sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15
d7abb599796bb7e34e0516bbe5df369af59ab29e7d588d9c0d1f5e5ae0357a99
otus@otus-db-pg-vm-1:~$ docker container ls
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS
         NAMES
d7abb599796b   postgres:15   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-server
```
## Развернуть контейнер с клиентом postgres
```
otus@otus-db-pg-vm-1:~$ sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
Password for user postgres:
psql (15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.
```
## Подключится из контейнера с клиентом к контейнеру с сервером и сделать таблицу с парой строк
```
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1), server 15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.

postgres=# create table persons(id serial, first_name text, second_name text);
CREATE TABLE
postgres=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
(0 rows)

postgres=# insert into persons(first_name, second_name) values('ivan', 'ivanov');
INSERT 0 1
postgres=# insert into persons(first_name, second_name) values('petr', 'petrov');
INSERT 0 1
postgres=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
postgres=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
 public | persons | table | postgres
(1 row)
```
* Ради эксперимента создаем новую базу, я не сразу поняла как подключиться потом к базе, но в итоге прописала в подключении базу и создала таблицу и строки
```
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1), server 15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.

postgres=# CREATE DATABASE Domtest2;
ERROR:  database "domtest2" already exists
postgres=# CREATE DATABASE Domtest;
CREATE DATABASE
postgres=# \l
                                                List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+------------+------------+------------+-----------------+-----------------------
 domtest   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 domtest2  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 otus      | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
(6 rows)
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d domtest2
Password for user postgres:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1), server 15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.

domtest2=# create table persons(id serial, first_name text, second_name text);
CREATE TABLE
domtest2=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
(0 rows)

domtest2=# insert into persons(first_name, second_name) values('ivan', 'ivanov');
INSERT 0 1
domtest2=# insert into persons(first_name, second_name) values('petr', 'petrov');
INSERT 0 1
domtest2=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
```
## Подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера
```
lisichkania@LAPTOP-JVR9DUC8:~$ psql -p 5432 -U postgres -h 158.160.67.175 -d postgres -W
Password:
psql (14.11 (Ubuntu 14.11-0ubuntu0.22.04.1), server 15.6 (Debian 15.6-1.pgdg120+2))
WARNING: psql major version 14, server major version 15.
         Some psql features might not work.
Type "help" for help.

postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 domtest   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 domtest2  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 otus      | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(6 rows)

postgres=# \q
```
## Удалить контейнер с сервером
```
otus@otus-db-pg-vm-1:~$ sudo docker stop d7abb599796b
d7abb599796b
otus@otus-db-pg-vm-1:~$ sudo docker rm d7abb599796b
d7abb599796b
otus@otus-db-pg-vm-1:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
## Cоздать его заново
```
otus@otus-db-pg-vm-1:~$ sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
psql: error: could not translate host name "pg-server" to address: Temporary failure in name resolution
```
* Выдает ошибку. Заново подключаем сеть и пробуем снова
```
otus@otus-db-pg-vm-1:~$ sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15
f291faff0a7268853df64875766bd876bee712d30fc33b8efed3dac059c09e97
otus@otus-db-pg-vm-1:~$ sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
Password for user postgres:
psql (15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.
```
## Подключится снова из контейнера с клиентом к контейнеру с сервером
```
otus@otus-db-pg-vm-1:~$ psql -h localhost -U postgres -d postgres
Password for user postgres:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1), server 15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.
```
## Проверить, что данные остались на месте
```
postgres=# \l
                                                List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+------------+------------+------------+-----------------+-----------------------
 domtest   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 domtest2  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 otus      | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 |            | libc            | =c/postgres          +
           |          |          |            |            |            |                 | postgres=CTc/postgres
(6 rows)
postgres=# \q
otus@otus-db-pg-vm-1:~$  psql -h localhost -U postgres -d domtest2
Password for user postgres:
psql (16.2 (Ubuntu 16.2-1.pgdg20.04+1), server 15.6 (Debian 15.6-1.pgdg120+2))
Type "help" for help.

domtest2=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
 public | persons | table | postgres
(1 row)

domtest2=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)

domtest2=# \q
```
* И в конце удалила контейнер

# Журналы 
## Настройте выполнение контрольной точки раз в 30 секунд.
```
postgres=# alter system set checkpoint_timeout = '30s';
ALTER SYSTEM
postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
postgres=# select pg_current_wal_lsn();
 pg_current_wal_lsn
--------------------
 0/1076B7D8
(1 row)
```
## 10 минут c помощью утилиты pgbench подавайте нагрузку.
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
100000 of 100000 tuples (100%) done (elapsed 0.33 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 0.92 s (drop tables 0.00 s, create tables 0.01 s, client-side generate 0.39 s, vacuum 0.04 s, primary keys 0.48 s).
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c10 -P 60 -T 600 -U postgres postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 60.0 s, 647.9 tps, lat 15.426 ms stddev 10.788, 0 failed
progress: 120.0 s, 637.5 tps, lat 15.687 ms stddev 10.828, 0 failed
progress: 180.0 s, 645.0 tps, lat 15.501 ms stddev 9.937, 0 failed
progress: 240.0 s, 638.9 tps, lat 15.650 ms stddev 9.997, 0 failed
progress: 300.0 s, 638.9 tps, lat 15.653 ms stddev 10.068, 0 failed
progress: 360.0 s, 639.9 tps, lat 15.624 ms stddev 10.208, 0 failed
progress: 420.0 s, 647.5 tps, lat 15.444 ms stddev 9.991, 0 failed
progress: 480.0 s, 645.2 tps, lat 15.497 ms stddev 9.952, 0 failed
progress: 540.0 s, 646.6 tps, lat 15.466 ms stddev 9.923, 0 failed
progress: 600.0 s, 644.4 tps, lat 15.518 ms stddev 10.153, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
maximum number of tries: 1
duration: 600 s
number of transactions actually processed: 385924
number of failed transactions: 0 (0.000%)
latency average = 15.546 ms
latency stddev = 10.190 ms
initial connection time = 18.383 ms
tps = 643.196670 (without initial connection time)
```
## Измерьте, какой объем журнальных файлов был сгенерирован за это время. 
```
postgres=# select pg_current_wal_insert_lsn();
 pg_current_wal_insert_lsn
---------------------------
 0/1FB0E1C8
(1 row)
postgres=# select '0/1FB0E1C8'::pg_lsn - '0/1076B7D8'::pg_lsn as bytes;
   bytes
-----------
 255470064
(1 row)
```
Оцените, какой объем приходится в среднем на одну контрольную точку.
*Контрольная точка делалась каждые 30 секунд, значит за 10 минут должно было создаться 20 контрольных точек. Значит по 12773503 байта на каждую, т.е примерно 12MB.*
## Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. 
```
postgres=# SELECT * FROM pg_stat_bgwriter \gx
-[ RECORD 1 ]---------+------------------------------
checkpoints_timed     | 47
checkpoints_req       | 1
checkpoint_write_time | 598874
checkpoint_sync_time  | 541
buffers_checkpoint    | 43799
buffers_clean         | 0
maxwritten_clean      | 0
buffers_backend       | 4341
buffers_backend_fsync | 0
buffers_alloc         | 5190
stats_reset           | 2024-03-10 00:13:43.904323+00
otus@otus-db-pg-vm-1:~$ sudo tail -n 100 /var/log/postgresql/postgresql-15-main.log | grep checkpoint
2024-03-10 00:18:36.052 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:19:03.108 UTC [40473] LOG:  checkpoint complete: wrote 1707 buffers (10.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.962 s, sync=0.064 s, total=27.057 s; sync files=55, longest=0.020 s, average=0.002 s; distance=12825 kB, estimate=12825 kB
2024-03-10 00:19:06.111 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:19:33.058 UTC [40473] LOG:  checkpoint complete: wrote 2030 buffers (12.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.868 s, sync=0.031 s, total=26.947 s; sync files=16, longest=0.017 s, average=0.002 s; distance=22525 kB, estimate=22525 kB
2024-03-10 00:19:36.061 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:20:03.146 UTC [40473] LOG:  checkpoint complete: wrote 1934 buffers (11.8%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.972 s, sync=0.062 s, total=27.085 s; sync files=12, longest=0.025 s, average=0.006 s; distance=24176 kB, estimate=24176 kB
2024-03-10 00:20:06.149 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:20:33.090 UTC [40473] LOG:  checkpoint complete: wrote 2120 buffers (12.9%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.866 s, sync=0.018 s, total=26.941 s; sync files=20, longest=0.010 s, average=0.001 s; distance=23798 kB, estimate=24139 kB
2024-03-10 00:20:36.093 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:21:03.109 UTC [40473] LOG:  checkpoint complete: wrote 2060 buffers (12.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.971 s, sync=0.004 s, total=27.016 s; sync files=12, longest=0.004 s, average=0.001 s; distance=24820 kB, estimate=24820 kB
2024-03-10 00:21:06.112 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:21:33.054 UTC [40473] LOG:  checkpoint complete: wrote 2111 buffers (12.9%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.866 s, sync=0.022 s, total=26.943 s; sync files=18, longest=0.011 s, average=0.002 s; distance=24408 kB, estimate=24779 kB
2024-03-10 00:21:36.057 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:22:03.108 UTC [40473] LOG:  checkpoint complete: wrote 2061 buffers (12.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.964 s, sync=0.025 s, total=27.052 s; sync files=14, longest=0.016 s, average=0.002 s; distance=24203 kB, estimate=24721 kB
2024-03-10 00:22:06.112 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:22:33.074 UTC [40473] LOG:  checkpoint complete: wrote 2093 buffers (12.8%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.861 s, sync=0.026 s, total=26.963 s; sync files=14, longest=0.010 s, average=0.002 s; distance=23729 kB, estimate=24622 kB
2024-03-10 00:22:36.076 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:23:03.133 UTC [40473] LOG:  checkpoint complete: wrote 2052 buffers (12.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.975 s, sync=0.016 s, total=27.057 s; sync files=13, longest=0.009 s, average=0.002 s; distance=23456 kB, estimate=24506 kB
2024-03-10 00:23:06.136 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:23:33.101 UTC [40473] LOG:  checkpoint complete: wrote 2105 buffers (12.8%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.885 s, sync=0.027 s, total=26.966 s; sync files=15, longest=0.010 s, average=0.002 s; distance=23525 kB, estimate=24407 kB
2024-03-10 00:23:36.104 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:24:03.063 UTC [40473] LOG:  checkpoint complete: wrote 2166 buffers (13.2%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.872 s, sync=0.051 s, total=26.959 s; sync files=15, longest=0.026 s, average=0.004 s; distance=23547 kB, estimate=24321 kB
2024-03-10 00:24:06.066 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:24:33.102 UTC [40473] LOG:  checkpoint complete: wrote 1991 buffers (12.2%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.967 s, sync=0.018 s, total=27.037 s; sync files=11, longest=0.011 s, average=0.002 s; distance=23263 kB, estimate=24216 kB
2024-03-10 00:24:36.105 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:25:03.047 UTC [40473] LOG:  checkpoint complete: wrote 2046 buffers (12.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.876 s, sync=0.021 s, total=26.942 s; sync files=14, longest=0.012 s, average=0.002 s; distance=23357 kB, estimate=24130 kB
2024-03-10 00:25:06.048 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:25:33.074 UTC [40473] LOG:  checkpoint complete: wrote 2121 buffers (12.9%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.973 s, sync=0.022 s, total=27.027 s; sync files=15, longest=0.009 s, average=0.002 s; distance=23131 kB, estimate=24030 kB
2024-03-10 00:25:36.077 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:26:03.110 UTC [40473] LOG:  checkpoint complete: wrote 2029 buffers (12.4%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.973 s, sync=0.011 s, total=27.033 s; sync files=11, longest=0.011 s, average=0.001 s; distance=23451 kB, estimate=23972 kB
2024-03-10 00:26:06.113 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:26:33.067 UTC [40473] LOG:  checkpoint complete: wrote 2076 buffers (12.7%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.867 s, sync=0.026 s, total=26.954 s; sync files=13, longest=0.015 s, average=0.002 s; distance=23253 kB, estimate=23900 kB
2024-03-10 00:26:36.068 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:27:03.121 UTC [40473] LOG:  checkpoint complete: wrote 2022 buffers (12.3%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.972 s, sync=0.017 s, total=27.054 s; sync files=12, longest=0.017 s, average=0.002 s; distance=23161 kB, estimate=23826 kB
2024-03-10 00:27:06.124 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:27:33.079 UTC [40473] LOG:  checkpoint complete: wrote 2313 buffers (14.1%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.877 s, sync=0.027 s, total=26.955 s; sync files=15, longest=0.013 s, average=0.002 s; distance=23222 kB, estimate=23766 kB
2024-03-10 00:27:36.080 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:28:03.134 UTC [40473] LOG:  checkpoint complete: wrote 2016 buffers (12.3%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.976 s, sync=0.015 s, total=27.055 s; sync files=12, longest=0.015 s, average=0.002 s; distance=23154 kB, estimate=23704 kB
2024-03-10 00:28:06.136 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:28:33.067 UTC [40473] LOG:  checkpoint complete: wrote 2058 buffers (12.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.869 s, sync=0.018 s, total=26.932 s; sync files=12, longest=0.010 s, average=0.002 s; distance=23051 kB, estimate=23639 kB
2024-03-10 00:28:36.070 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:29:03.079 UTC [40473] LOG:  checkpoint complete: wrote 1988 buffers (12.1%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.975 s, sync=0.003 s, total=27.010 s; sync files=12, longest=0.003 s, average=0.001 s; distance=23107 kB, estimate=23586 kB
2024-03-10 00:29:36.112 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:30:03.095 UTC [40473] LOG:  checkpoint complete: wrote 709 buffers (4.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.963 s, sync=0.006 s, total=26.984 s; sync files=15, longest=0.004 s, average=0.001 s; distance=14194 kB, estimate=22647 kB
2024-03-10 00:58:07.480 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:58:34.120 UTC [40473] LOG:  checkpoint complete: wrote 1694 buffers (10.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.557 s, sync=0.061 s, total=26.640 s; sync files=47, longest=0.014 s, average=0.002 s; distance=12831 kB, estimate=21665 kB
2024-03-10 00:58:37.123 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:59:04.060 UTC [40473] LOG:  checkpoint complete: wrote 2077 buffers (12.7%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.871 s, sync=0.018 s, total=26.937 s; sync files=25, longest=0.009 s, average=0.001 s; distance=20257 kB, estimate=21524 kB
2024-03-10 00:59:07.063 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 00:59:34.103 UTC [40473] LOG:  checkpoint complete: wrote 1873 buffers (11.4%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.966 s, sync=0.022 s, total=27.040 s; sync files=11, longest=0.009 s, average=0.002 s; distance=24169 kB, estimate=24169 kB
2024-03-10 00:59:37.106 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:00:04.040 UTC [40473] LOG:  checkpoint complete: wrote 2177 buffers (13.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.866 s, sync=0.020 s, total=26.935 s; sync files=17, longest=0.011 s, average=0.002 s; distance=24093 kB, estimate=24162 kB
2024-03-10 01:00:07.043 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:00:34.090 UTC [40473] LOG:  checkpoint complete: wrote 2017 buffers (12.3%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.976 s, sync=0.019 s, total=27.048 s; sync files=8, longest=0.008 s, average=0.003 s; distance=24242 kB, estimate=24242 kB
2024-03-10 01:00:37.093 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:01:04.130 UTC [40473] LOG:  checkpoint complete: wrote 2029 buffers (12.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.969 s, sync=0.024 s, total=27.037 s; sync files=12, longest=0.010 s, average=0.002 s; distance=24858 kB, estimate=24858 kB
2024-03-10 01:01:07.132 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:01:34.064 UTC [40473] LOG:  checkpoint complete: wrote 2129 buffers (13.0%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.866 s, sync=0.023 s, total=26.933 s; sync files=13, longest=0.008 s, average=0.002 s; distance=23985 kB, estimate=24770 kB
2024-03-10 01:01:37.067 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:02:04.110 UTC [40473] LOG:  checkpoint complete: wrote 2035 buffers (12.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.978 s, sync=0.019 s, total=27.044 s; sync files=11, longest=0.007 s, average=0.002 s; distance=23982 kB, estimate=24692 kB
2024-03-10 01:02:07.113 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:02:34.062 UTC [40473] LOG:  checkpoint complete: wrote 2108 buffers (12.9%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.871 s, sync=0.025 s, total=26.949 s; sync files=15, longest=0.009 s, average=0.002 s; distance=23540 kB, estimate=24576 kB
2024-03-10 01:02:37.065 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:03:04.110 UTC [40473] LOG:  checkpoint complete: wrote 2024 buffers (12.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.978 s, sync=0.019 s, total=27.046 s; sync files=11, longest=0.008 s, average=0.002 s; distance=23760 kB, estimate=24495 kB
2024-03-10 01:03:07.112 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:03:34.151 UTC [40473] LOG:  checkpoint complete: wrote 2102 buffers (12.8%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.965 s, sync=0.038 s, total=27.040 s; sync files=15, longest=0.017 s, average=0.003 s; distance=23593 kB, estimate=24405 kB
2024-03-10 01:03:37.152 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:04:04.122 UTC [40473] LOG:  checkpoint complete: wrote 2022 buffers (12.3%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.871 s, sync=0.028 s, total=26.971 s; sync files=11, longest=0.017 s, average=0.003 s; distance=23737 kB, estimate=24338 kB
2024-03-10 01:04:07.125 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:04:34.074 UTC [40473] LOG:  checkpoint complete: wrote 2083 buffers (12.7%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.866 s, sync=0.042 s, total=26.949 s; sync files=15, longest=0.024 s, average=0.003 s; distance=23351 kB, estimate=24239 kB
2024-03-10 01:04:37.077 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:05:04.130 UTC [40473] LOG:  checkpoint complete: wrote 2040 buffers (12.5%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.965 s, sync=0.027 s, total=27.053 s; sync files=11, longest=0.014 s, average=0.003 s; distance=23346 kB, estimate=24150 kB
2024-03-10 01:05:07.133 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:05:34.057 UTC [40473] LOG:  checkpoint complete: wrote 2076 buffers (12.7%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.866 s, sync=0.018 s, total=26.924 s; sync files=13, longest=0.017 s, average=0.002 s; distance=22970 kB, estimate=24032 kB
2024-03-10 01:05:37.060 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:06:04.102 UTC [40473] LOG:  checkpoint complete: wrote 2028 buffers (12.4%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.969 s, sync=0.019 s, total=27.043 s; sync files=12, longest=0.014 s, average=0.002 s; distance=23271 kB, estimate=23956 kB
2024-03-10 01:06:07.104 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:06:34.141 UTC [40473] LOG:  checkpoint complete: wrote 2290 buffers (14.0%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.967 s, sync=0.025 s, total=27.038 s; sync files=15, longest=0.015 s, average=0.002 s; distance=23173 kB, estimate=23878 kB
2024-03-10 01:06:37.144 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:07:04.094 UTC [40473] LOG:  checkpoint complete: wrote 2010 buffers (12.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.880 s, sync=0.021 s, total=26.950 s; sync files=12, longest=0.016 s, average=0.002 s; distance=23227 kB, estimate=23813 kB
2024-03-10 01:07:07.097 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:07:34.151 UTC [40473] LOG:  checkpoint complete: wrote 2060 buffers (12.6%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.968 s, sync=0.036 s, total=27.055 s; sync files=14, longest=0.020 s, average=0.003 s; distance=23006 kB, estimate=23732 kB
2024-03-10 01:07:37.154 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:08:04.120 UTC [40473] LOG:  checkpoint complete: wrote 1991 buffers (12.2%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.894 s, sync=0.022 s, total=26.966 s; sync files=11, longest=0.012 s, average=0.002 s; distance=23012 kB, estimate=23660 kB
2024-03-10 01:08:07.123 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:08:34.043 UTC [40473] LOG:  checkpoint complete: wrote 2299 buffers (14.0%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.879 s, sync=0.009 s, total=26.920 s; sync files=13, longest=0.004 s, average=0.001 s; distance=23138 kB, estimate=23608 kB
2024-03-10 01:08:37.044 UTC [40473] LOG:  checkpoint starting: time
2024-03-10 01:09:04.103 UTC [40473] LOG:  checkpoint complete: wrote 1886 buffers (11.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=27.047 s, sync=0.002 s, total=27.060 s; sync files=13, longest=0.002 s, average=0.001 s; distance=18120 kB, estimate=23059 kB
```
Почему так произошло?

*После старта записи контрольной точки выполнение происходит только через 27 секунд, срабатывает checkpoint_completion_target*
```
postgres=# show checkpoint_completion_target;
 checkpoint_completion_target
------------------------------
 0.9
(1 row)
```

*0,9*30=27 секунд - период времени, на который растягивается запись «грязных» буферов во время контрольной точки.*

*Так же была создана контрольная точка по запросу PostgreSQL. Такие точки СУБД делает тогда когда считает, что в буферном кэше накопилось слишком много грязных страниц и их нужно срочно записать, что бы не потерять.*
## Сравните tps в синхронном/асинхронном режиме утилитой pgbench. 
```
postgres=# show synchronous_commit;
 synchronous_commit
--------------------
 on
(1 row)

postgres=# ALTER SYSTEM SET synchronous_commit = off;
ALTER SYSTEM
postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo pg_ctlcluster 15 main restart
otus@otus-db-pg-vm-1:~$ sudo -u postgres psql
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# show synchronous_commit;
 synchronous_commit
--------------------
 off
(1 row)

postgres=# \q
otus@otus-db-pg-vm-1:~$ sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -i postgres
dropping old tables...
creating tables...
generating data (client-side)...
100000 of 100000 tuples (100%) done (elapsed 0.07 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 0.30 s (drop tables 0.01 s, create tables 0.00 s, client-side generate 0.16 s, vacuum 0.03 s, primary keys 0.10 s).
postgres@otus-db-pg-vm-1:/home/otus$ pgbench -c10 -P 60 -T 600 -U postgres postgres
pgbench (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
starting vacuum...end.
progress: 60.0 s, 3223.6 tps, lat 3.100 ms stddev 2.287, 0 failed
progress: 120.0 s, 3153.7 tps, lat 3.170 ms stddev 0.985, 0 failed
progress: 180.0 s, 3276.7 tps, lat 3.051 ms stddev 0.954, 0 failed
progress: 240.0 s, 3329.0 tps, lat 3.003 ms stddev 0.945, 0 failed
progress: 300.0 s, 3234.4 tps, lat 3.091 ms stddev 0.979, 0 failed
progress: 360.0 s, 3251.1 tps, lat 3.075 ms stddev 0.968, 0 failed
progress: 420.0 s, 3136.1 tps, lat 3.188 ms stddev 0.981, 0 failed
progress: 480.0 s, 3236.1 tps, lat 3.089 ms stddev 0.961, 0 failed
progress: 540.0 s, 3282.7 tps, lat 3.046 ms stddev 0.970, 0 failed
progress: 600.0 s, 3279.8 tps, lat 3.048 ms stddev 0.963, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
maximum number of tries: 1
duration: 600 s
number of transactions actually processed: 1944193
number of failed transactions: 0 (0.000%)
latency average = 3.085 ms
latency stddev = 1.169 ms
initial connection time = 18.929 ms
tps = 3240.309440 (without initial connection time)
```
Объясните полученный результат.

*3240 против 643, асинхронный гораздо производительнее, так как серверу не надо ждать пока записи из WAL сохранятся на диске, чтобы сообщить клиенту об успешном завершении операции. Получается, что накладные расходы не влияют на результат*
## Создайте новый кластер с включенной контрольной суммой страниц.
```
otus@otus-db-pg-vm-1:~$ sudo pg_createcluster 15 check -- --data-checksums
Creating new PostgreSQL cluster 15/check ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/check --auth-local peer --auth-host scram-sha-256 --no-instructions --data-checksums
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are enabled.

fixing permissions on existing directory /var/lib/postgresql/15/check ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Ver Cluster Port Status Owner    Data directory               Log file
15  check   5433 down   postgres /var/lib/postgresql/15/check /var/log/postgresql/postgresql-15-check.log
otus@otus-db-pg-vm-1:~$ pg_ctlcluster 15 check start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@15-check
Error: You must run this program as the cluster owner (postgres) or root
otus@otus-db-pg-vm-1:~$ sudo systemctl start postgresql@15-check
otus@otus-db-pg-vm-1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  check   5433 online postgres /var/lib/postgresql/15/check /var/log/postgresql/postgresql-15-check.log
15  main    5432 down   postgres /var/lib/postgresql/15/main  /var/log/postgresql/postgresql-15-main.log
```
### Создайте таблицу. Вставьте несколько значений. Выключите кластер. 
```
postgres=# CREATE TABLE test(i int);
CREATE TABLE
postgres=# insert into test select s.id from generate_series(1, 100) as s(id);
INSERT 0 100
postgres=# select * from test limit 10;
 i
----
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
(10 rows)

postgres=# \q
postgres@otus-db-pg-vm-1:/home/otus$ exit
exit
otus@otus-db-pg-vm-1:~$ sudo systemctl stop postgresql@15-check
```
### Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. 
```
otus@otus-db-pg-vm-1:~$ sudo sed -i 's/@/@123@/' /var/lib/postgresql/15/check/base/5/16391
otus@otus-db-pg-vm-1:~$ sudo systemctl start postgresql@15-check
otus@otus-db-pg-vm-1:~$  sudo su postgres
postgres@otus-db-pg-vm-1:/home/otus$  psql -p 5433
could not change directory to "/home/otus": Permission denied
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

postgres=# select * from test;
WARNING:  page verification failed, calculated checksum 30144 but expected 25751
ERROR:  invalid page in block 0 of relation base/5/16391
```
Что и почему произошло? как проигнорировать ошибку и продолжить работу?

*В середине файла дописали текст, запустили кластер и получили, что данные прочитать не удалось, потому что были обнаружены ошибки контрольных сумм файла таблицы. Но если мы всё-таки хотим извлечь эти данные, то есть параметр ignore_checksum_failure. Когда мы его включаем, у нас выдаётся предупреждение, что контрольная сумма данных не совпадает, но запрос исполняется.*
```
postgres=# set ignore_checksum_failure = on;
SET
postgres=# show ignore_checksum_failure;
 ignore_checksum_failure
-------------------------
 on
(1 row)

postgres=# select * from test;
WARNING:  page verification failed, calculated checksum 30144 but expected 25751
 i
---
(0 rows)
```

*Часто приложения используют только оперативные данные. PostgreSQL не обращается к старым страницам данных. И если в них есть повреждения, то мы можем узнать об этом слишком поздно, когда в резервных копиях тоже будут содержаться они.*

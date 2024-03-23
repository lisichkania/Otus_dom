# Виды индексов. Работа с индексами и оптимизация запросов 
## 1. Создать индекс к какой-либо из таблиц вашей БД

*Cоздаём таблицу с тремя полями. Первое поле содержит числа от 1 до 100000, и по нему создан индекс. Второе поле содержит различные ASCII-символы, кроме непечатных. Третье поле содержит логическое значение, истинное примерно для 1% строк, и ложное для остальных. Строки вставлены в таблицу в случайном порядке.*
```
postgres=# create table t(a integer, b text, c boolean);
CREATE TABLE
postgres=# insert into t(a,b,c)
  select s.id, chr((32+random()*94)::integer), random() < 0.01
  from generate_series(1,500000) as s(id)
  order by random();
INSERT 0 500000
postgres=# create index on t(a);
CREATE INDEX
postgres=# analyze t;
ANALYZE
```
## 2. Прислать текстом результат команды explain, в которой используется данный индекс

*Попробуем выбрать значение по условию «a = 1».*
```
postgres=# explain (costs off) select * from t where a = 1;
          QUERY PLAN
-------------------------------
 Index Scan using t_a_idx on t
   Index Cond: (a = 1)
(2 rows)
```
*В данном случае оптимизатор принял решение использовать индексное сканирование (Index Scan). При индексном просмотре метод доступа возвращает значения TID по одному, до тех пор, пока подходящие строки не закончатся. Механизм индексирования по очереди обращается к тем страницам таблицы, на которые указывают TID, получает версию строки, проверяет ее видимость в соответствии с правилами многоверсионности, и возвращает полученные данные.*

*При выборке по диапазону значений он так же предлагает использовать индексное сканирование*
```
postgres=# explain (costs off) select * from t where a <= 100;
          QUERY PLAN
-------------------------------
 Index Scan using t_a_idx on t
   Index Cond: (a <= 100)
(2 rows)
```

*Если условия наложены на несколько полей таблицы, и эти поля проиндексированы, сканирование битовой карты позволяет (если оптимизатор сочтет это выгодным) использовать несколько индексов одновременно.*
```
postgres=# create index on t(b);
CREATE INDEX
postgres=# analyze t;
ANALYZE
postgres=# explain (costs off) select * from t where a <= 100 and b = 'a';
                    QUERY PLAN
--------------------------------------------------
 Bitmap Heap Scan on t
   Recheck Cond: ((a <= 100) AND (b = 'a'::text))
   ->  BitmapAnd
         ->  Bitmap Index Scan on t_a_idx
               Index Cond: (a <= 100)
         ->  Bitmap Index Scan on t_b_idx
               Index Cond: (b = 'a'::text)
(7 rows)
```

*Здесь узел BitmapAnd объединяет две битовые карты с помощью битовой операции «и». Сканирование по битовой карте позволяет избежать повторных обращений к одной и той же странице данных.*

*Для полноты картины следует сказать, что при неселективном условии оптимизатор предпочтет использованию индекса последовательное сканирование таблицы целиком.*

```
postgres=# explain (costs off) select * from t where a <= 70000;
       QUERY PLAN
------------------------
 Seq Scan on t
   Filter: (a <= 70000)
(2 rows)
```
## 3. Реализовать индекс для полнотекстового поиска

*Для эксперимента попробуем сделать полнотекстовый поиск загруженным из интернета данным по объявлениям с АВИТО. Создали роль, базу, таблицу с JSONB полем и загрузили туда данные. Проверяем наличие таблицы и смотрим количество в ней строк*
```
postgres=# CREATE ROLE otus WITH CREATEDB LOGIN;
CREATE ROLE
postgres=# \q
otus@otus-db-pg-vm-1:~$ createdb fts
otus@otus-db-pg-vm-1:~$ psql fts
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

fts=> CREATE TABLE ads (json jsonb);
CREATE TABLE
fts=> \q
otus@otus-db-pg-vm-1:~$ wget http://komar.in/files/ads.pgdump
--2024-03-23 15:21:34--  http://komar.in/files/ads.pgdump
Resolving komar.in (komar.in)... 172.67.131.113, 104.21.4.9, 2606:4700:3034::ac43:8371, ...
Connecting to komar.in (komar.in)|172.67.131.113|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 244985782 (234M) [application/octet-stream]
Saving to: ‘ads.pgdump.1’

ads.pgdump.1                  100%[=================================================>] 233.64M  54.9MB/s    in 4.4s

2024-03-23 15:21:39 (52.9 MB/s) - ‘ads.pgdump.1’ saved [244985782/244985782]

otus@otus-db-pg-vm-1:~$ pg_restore -d fts --data-only < ads.pgdump
otus@otus-db-pg-vm-1:~$ psql fts
psql (15.6 (Ubuntu 15.6-1.pgdg22.04+1))
Type "help" for help.

fts=> \dt
       List of relations
 Schema | Name | Type  | Owner
--------+------+-------+-------
 public | ads  | table | otus
(1 row)

fts=> \d ads
               Table "public.ads"
 Column | Type  | Collation | Nullable | Default
--------+-------+-----------+----------+---------
 json   | jsonb |           |          |

fts=> SELECT count(*) FROM ads;
 count
--------
 500000
(1 row)
```

*Пробуем построить план запроса по поиску продажи телефонов (вывод телефона и заголовка объявления) с analyze и выводом времени запуска и времени выполнения на каждом узле*
```
fts=> explain (ANALYZE, timing) SELECT json->>'phone', json->>'title'
FROM ads
WHERE to_tsvector('russian', json->>'title') @@ plainto_tsquery('russian', 'продаю & телефон');
                                                       QUERY PLAN
------------------------------------------------------------------------------------------------------------------------
 Gather  (cost=1000.00..142605.56 rows=12 width=64) (actual time=51.204..4805.182 rows=45 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on ads  (cost=0.00..141604.36 rows=5 width=64) (actual time=464.052..4750.130 rows=15 loops=3)
         Filter: (to_tsvector('russian'::regconfig, (json ->> 'title'::text)) @@ '''прода'' & ''телефон'''::tsquery)
         Rows Removed by Filter: 166652
 Planning Time: 0.073 ms
 JIT:
   Functions: 12
   Options: Inlining false, Optimization false, Expressions true, Deforming true
   Timing: Generation 1.045 ms, Inlining 0.000 ms, Optimization 8.705 ms, Emission 12.879 ms, Total 22.630 ms
 Execution Time: 4805.478 ms
(12 rows)
```
*План запроса содержит параллельное последовательное сканирование. Как я пониманию он строит поиск на параллельном сканировании в два потока, отбирает фильтром данные и потом выкидывает то, что не подошло по второму параметру поиска. Время выполнения данного поиска будет составлять 4805.478 ms*

*Созданём GIN индекс для JSONB поля. Выполняем тот же explain*
```
fts=> CREATE INDEX index_on_ads_title_using_gin
ON ads
USING gin
(to_tsvector('russian', json->>'title'));
CREATE INDEX
fts=> explain (ANALYZE, timing) SELECT json->>'phone', json->>'title'
FROM ads
WHERE to_tsvector('russian', json->>'title') @@ plainto_tsquery('russian', 'продаю & телефон');

QUERY PLAN

---------------------------------------------------------------------------------------------------------------------------------------
 Bitmap Heap Scan on ads  (cost=7.10..22.34 rows=12 width=64) (actual time=0.252..0.685 rows=45 loops=1)
   Recheck Cond: (to_tsvector('russian'::regconfig, (json ->> 'title'::text)) @@ '''прода'' & ''телефон'''::tsquery)
   Heap Blocks: exact=45
   ->  Bitmap Index Scan on index_on_ads_title_using_gin  (cost=0.00..7.09 rows=12 width=0) (actual time=0.229..0.229 rows=45 loops=1)
         Index Cond: (to_tsvector('russian'::regconfig, (json ->> 'title'::text)) @@ '''прода'' & ''телефон'''::tsquery)
 Planning Time: 0.097 ms
 Execution Time: 0.703 ms
(7 rows)
```

*Сначала (на нижнем уровне) идет Bitmap Index Scan, а затем – Bitmap Heap Scan. Bitmap Index Scan создаст битовую карту, где каждой странице нашей таблицы соответствует один бит. Все эти биты будут установлены в 0. Затем, Bitmap Index Scan установит некоторые биты в 1, в зависимости от того, на какой странице таблицы может находиться строка, которую нужно вернуть. После того как это будет сделано – то есть когда все страницы, на которых находятся строки, которые нужно вернуть, будут «помечены» – эта битовая карта перейдет на уровень выше, к узлу Bitmap Heap Scan, который читает их в более последовательной манере. Что гораздо быстрее - 0.703 ms.*

*Создаем другую таблицу avito и заполняем её данными с телефоном и заголовком объявлений из нашей ads*
```
fts=> create table avito (phone Varchar(20), title text);
CREATE TABLE
fts=> INSERT INTO avito (phone, title)
select
(json->> 'phone')::Varchar(20),
(json->> 'title')::text
from ads;
INSERT 0 1000000
fts=> SELECT phone, title
FROM avito limit 10;
      phone      |                      title
-----------------+-------------------------------------------------
 8 996 445-48-89 | Трактор Т-25, плуг, тележка, косилка, бульдозер
 8 981 551-89-39 | Самосвал камаз
 8 912 892-82-56 | Продам экскаватор samsung volvo
 8 912 404-72-98 | Комбайн
 8 918 800-41-60 | Камаз 55111
 8 800 333-91-15 | Chevrolet Orlando 1.8 AT, 2013, минивэн
 8 800 333-91-15 | KIA Soul 1.6 AT, 2010, хетчбэк
 8 800 333-91-15 | Mazda CX-7 2.3 AT, 2012, внедорожник
 8 800 333-91-15 | Citroen C4 Picasso 1.6 AT, 2011, минивэн
 8 800 333-91-15 | Honda Pilot 3.5 AT, 2009, внедорожник
(10 rows)
```

*Построим план запроса на вывод телефона и заголовка объявлений о продаже телефона по ключевым словам*
```
fts=> EXPLAIN (ANALYZE, timing) SELECT phone, title FROM avito WHERE to_tsvector('russian', title) @@ plainto_tsquery('russian', 'продаю & телефон');

 QUERY PLAN

---------------------------------------------------------------------------------------------------------------------------
 Gather  (cost=1000.00..117742.50 rows=25 width=59) (actual time=401.926..2589.106 rows=45 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on avito  (cost=0.00..116740.00 rows=10 width=59) (actual time=258.365..2488.451 rows=15 loops=3)
         Filter: (to_tsvector('russian'::regconfig, title) @@ '''прода'' & ''телефон'''::tsquery)
         Rows Removed by Filter: 333318
 Planning Time: 21.448 ms
 JIT:
   Functions: 6
   Options: Inlining false, Optimization false, Expressions true, Deforming true
   Timing: Generation 0.686 ms, Inlining 0.000 ms, Optimization 0.619 ms, Emission 376.672 ms, Total 377.976 ms
 Execution Time: 2611.465 ms
(12 rows)
```

*План запроса содержит параллельное последовательное сканирование. Время вывода 2611.465 ms*

*Сделаем добавление столбца tsvector с автоматическим заполнением для полнотекстового поиска и создадим план запроса по нему*
```
fts=> ALTER TABLE avito ADD COLUMN title_tsvector TSVECTOR GENERATED ALWAYS
AS (to_tsvector('russian', title)) STORED;
ALTER TABLE
fts=> EXPLAIN (ANALYZE, timing) SELECT phone, title FROM avito WHERE title_tsvector @@
to_tsquery('russian', 'продаю & телефон');
                                                      QUERY PLAN
----------------------------------------------------------------------------------------------------------------------
 Gather  (cost=1000.00..17024.50 rows=20 width=59) (actual time=1.697..183.021 rows=45 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on avito  (cost=0.00..16022.50 rows=8 width=59) (actual time=2.399..144.078 rows=15 loops=3)
         Filter: (title_tsvector @@ '''прода'' & ''телефон'''::tsquery)
         Rows Removed by Filter: 333318
 Planning Time: 0.084 ms
 Execution Time: 183.045 ms
(8 rows)
```

*План запроса всё так же содержит параллельное последовательное сканирование. Но время вывода уже 183.045 ms*

*Создадим GIN индекс для столбца tsvector и повторим построение плана запроса*
```
fts=> CREATE INDEX idx_articles_title_tsvector ON avito USING gin
(title_tsvector);
CREATE INDEX
fts=> EXPLAIN (ANALYZE, timing) SELECT phone, title FROM avito WHERE title_tsvector @@
to_tsquery('russian', 'продаю & телефон');

QUERY PLAN

--------------------------------------------------------------------------------------------------------------------------------------
 Bitmap Heap Scan on avito  (cost=7.19..32.51 rows=25 width=59) (actual time=0.232..0.292 rows=45 loops=1)
   Recheck Cond: (title_tsvector @@ '''прода'' & ''телефон'''::tsquery)
   Heap Blocks: exact=42
   ->  Bitmap Index Scan on idx_articles_title_tsvector  (cost=0.00..7.19 rows=25 width=0) (actual time=0.223..0.223 rows=45 loops=1)
         Index Cond: (title_tsvector @@ '''прода'' & ''телефон'''::tsquery)
 Planning Time: 0.190 ms
 Execution Time: 0.308 ms
(7 rows)
```

*Здесь так же Bitmap Index Scan и Bitmap Heap Scan в паре. Но уже гораздо быстрее - 0.308.*

## 4. Реализовать индекс на часть таблицы или индекс на поле с функцией
### Индекс на часть таблицы

*Можно построить обычный индекс по столбцу «c»*
```
postgres=# create index on t(c);
CREATE INDEX
postgres=# analyze t;
ANALYZE
postgres=# explain (costs off) select * from t where c;
          QUERY PLAN
-------------------------------
 Index Scan using t_c_idx on t
   Index Cond: (c = true)
(2 rows)

postgres=# explain (costs off) select * from t where not c;
    QUERY PLAN
-------------------
 Seq Scan on t
   Filter: (NOT c)
(2 rows)
postgres=# select relpages from pg_class where relname='t_c_idx';
 relpages
----------
       87
(1 row)
```

*Поскольку столбец «c» имеет значение true только для одного процента строк, 99% индекса просто никогда не используются. О чём и говорилось в лекции. При этом индекс занимает 87 страниц.*

*В этом случае можно построить частичный индекс. Он будет занимать всего 2 страницы. В некоторых случаях разница в объеме и производительности может быть весьма существенной. В первом случае он использует уже частичный индекс для построения плана запроса. Во втором так же Seq Scan для сканирования таблицы целиком.*
```
postgres=# create index on t(c) where c;
CREATE INDEX
postgres=# analyze t;
ANALYZE
postgres=# select relpages from pg_class where relname='t_c_idx1';
 relpages
----------
        2
(1 row)
postgres=# explain (costs off) select * from t where c;
           QUERY PLAN
--------------------------------
 Index Scan using t_c_idx1 on t
(1 row)

postgres=# explain (costs off) select * from t where not c;
    QUERY PLAN
-------------------
 Seq Scan on t
   Filter: (NOT c)
(2 rows)
```
### Индекс на поле с функцией

*В данном примере индекс не будет использоваться, поскольку вместо самого имени поля используется выражение с ним.*
```
postgres=# explain (costs off) select * from t where lower(b) = 'a';
                QUERY PLAN                
------------------------------------------
 Seq Scan on t
   Filter: (lower((b)::text) = 'a'::text)
(2 rows)
```

*Функциональный индекс создается не по полю таблицы, а по произвольному выражению; оптимизатор будет принимать во внимание такой индекс для условий вида «индексированное-выражение оператор выражение». Если вычисление индексируемого выражения — затратная операция, то и обновление индекса будет требовать значительных вычислительных ресурсов.*
```
postgres=# create index on t(lower(b));
CREATE INDEX
postgres=# analyze t;
ANALYZE
postgres=# explain (costs off) select * from t where lower(b) = 'a';
                     QUERY PLAN                    
----------------------------------------------------
 Bitmap Heap Scan on t
   Recheck Cond: (lower((b)::text) = 'a'::text)
   ->  Bitmap Index Scan on t_lower_idx
         Index Cond: (lower((b)::text) = 'a'::text)
(4 rows)
```
## 5. Создать индекс на несколько полей

*Оптимизатор скорее всего предпочтет такой индекс объединению битовых карт, поскольку здесь мы сразу получаем нужные TID без каких-либо вспомогательных действий*
```
postgres=# create index on t(a,b);
CREATE INDEX
postgres=# analyze t;
ANALYZE
postgres=# explain (costs off) select * from t where a <= 100 and b = 'a';
                   QUERY PLAN
------------------------------------------------
 Index Scan using t_a_b_idx on t
   Index Cond: ((a <= 100) AND (b = 'a'::text))
(2 rows)
```

*Многоколоночный индекс может использоваться и для ускорения выборки по условию на часть полей — начиная с первого. Как правило, если на первое поле не наложено условие, индекс использоваться не будет. Но в некоторых случаях оптимизатор может счесть, что это все-таки выгоднее последовательного сканирования.*
```
postgres=# explain (costs off) select * from t where a <= 100;
              QUERY PLAN              
--------------------------------------
 Bitmap Heap Scan on t
   Recheck Cond: (a <= 100)
   ->  Bitmap Index Scan on t_a_b_idx
         Index Cond: (a <= 100)
(4 rows)
```

# Сбор и использование статистики 
## К работе приложить структуру таблиц, для которых выполнялись соединения
*У меня в базе есть таблица заявок АСУП она используется как help desk с пользовательским интерфейсом на базе Access, табличная часть хранится на Microsoft SQL Server. Соответственно пользователи оставляют заявку определенного типа и в опеределенном модуле, которые так же написаны на базе Access. В таблицу записывается код, по которому в Типе заявок и в списке модулей АСУП можно посмотреть какого типа заявки были и к каким программам они относятся соответственно. Ну и вывести статистику обращений по тем или инным проблемам. Переименовывать колонки проблематично, поэтому я просто вгрузила из access эти таблицы в postgres через odbc соединение, доступа к SQL серверу у меня из дома нет, закрытый контур, взяла чуток данных для практики.*
```
Table "public.Заявки АСУП"
        Column        |           Type           | Collation | Nullable | Default | Storage  | Compression | Stats target | Description
----------------------+--------------------------+-----------+----------+---------+----------+-------------+--------------+-------------
 Код                  | integer                  |           |          |         | plain    |             |
  |
 Модуль               | integer                  |           |          |         | plain    |             |
  |
 Внёс                 | character varying(20)    |           |          |         | extended |             |
  |
 Отдел                | integer                  |           |          |         | plain    |             |
  |
 Заявитель            | character varying(255)   |           |          |         | extended |             |
  |
 Тема обращения       | integer                  |           |          |         | plain    |             |
  |
 Тип                  | integer                  |           |          |         | plain    |             |
  |
 Телефон              | character varying(11)    |           |          |         | extended |             |
  |
 Описание             | text                     |           |          |         | extended |             |
  |
 Решение              | character varying(255)   |           |          |         | extended |             |
  |
 Скриншот             | character varying(255)   |           |          |         | extended |             |
  |
 СЗ                   | character varying(255)   |           |          |         | extended |             |
  |
```

```
Table "public.Тип заявок"
     Column     |          Type          | Collation | Nullable | Default | Storage  | Compression | Stats target | Description
----------------+------------------------+-----------+----------+---------+----------+-------------+--------------+-------------
 Код            | integer                |           |          |         | plain    |             |              |
 Тема обращения | character varying(255) |           |          |         | extended |             |              |
 Тип заявки     | character varying(255) |           |          |         | extended |             |              |
 Код типа       | integer                |           |          |         | plain    |             |              |
```

```
Table "public.Справочник модулей АСУП"
  Column   |          Type          | Collation | Nullable | Default | Storage  | Compression | Stats target | Description
-----------+------------------------+-----------+----------+---------+----------+-------------+--------------+-------------
 Код       | integer                |           |          |         | plain    |             |              |
 Модуль    | character varying(255) |           |          |         | extended |             |              |
 Файл      | character varying(255) |           |          |         | extended |             |              |
 Программа | character varying(255) |           |          |         | extended |             |              |
```

## Реализовать прямое соединение двух или более таблиц
*Прямое соединение с использованием JOIN двух таблиц*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Join  (cost=11.57..195.72 rows=231 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.634 ms
```

*Прямое соединение с использованием JOIN трёх таблиц*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код"
JOIN "Справочник модулей АСУП" mz ON z."Модуль" = mz."Код";
                                         QUERY PLAN
---------------------------------------------------------------------------------------------
 Hash Join  (cost=22.70..209.98 rows=227 width=3533)
   Hash Cond: (z."Модуль" = mz."Код")
   ->  Hash Join  (cost=11.57..195.72 rows=231 width=1981)
         Hash Cond: (z."Тема обращения" = tz."Код")
         ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
         ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
               ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
   ->  Hash  (cost=10.50..10.50 rows=50 width=1552)
         ->  Seq Scan on "Справочник модулей АСУП" mz  (cost=0.00..10.50 rows=50 width=1552)
(9 rows)

Time: 2.251 ms
```

*Прямое соединение без явного указания JOIN (подразумевается INNER JOIN)*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z, "Тип заявок" tz
WHERE z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Join  (cost=11.57..195.72 rows=231 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.551 ms
```

## Реализовать левостороннее (или правостороннее) соединение двух или более таблиц
*LEFT JOIN для соединения таблиц с возвратом всех записей из левой таблицы*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
LEFT JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Left Join  (cost=11.57..195.72 rows=2679 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.625 ms
```

*RIGHT JOIN для соединения таблиц с возвратом всех записей из правой таблицы*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
RIGHT JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Right Join  (cost=11.57..195.72 rows=231 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.623 ms
```

*LEFT JOIN с условием возврата записей, для которых нет соответствий в правой таблице*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
LEFT JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код"
WHERE tz."Код" IS NULL;
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Anti Join  (cost=11.57..215.14 rows=2448 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.520 ms
```

*RIGHT JOIN с условием возврата записей, для которых нет соответствий в левой таблице*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
RIGHT JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код"
WHERE z."Код" IS NULL;
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Right Join  (cost=11.57..195.72 rows=1 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   Filter: (z."Код" IS NULL)
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(6 rows)

Time: 0.631 ms
```
## Реализовать кросс соединение двух или более таблиц
*CROSS JOIN для создания декартова произведения таблиц*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
CROSS JOIN "Тип заявок" tz;
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Nested Loop  (cost=0.00..2526.79 rows=187530 width=1981)
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Materialize  (cost=0.00..11.05 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(4 rows)
```

*Использование CROSS JOIN с условием, эквивалентное INNER JOIN*
```
postgres=#  EXPLAIN
SELECT *
FROM "Заявки АСУП" z
CROSS JOIN "Тип заявок" tz
WHERE z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Join  (cost=11.57..195.72 rows=231 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.613 ms
```
## Реализовать полное соединение двух или более таблиц
*FULL JOIN для соединения двух таблиц с возвратом всех записей из обеих*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
FULL JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код";
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Full Join  (cost=11.57..195.72 rows=2679 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(5 rows)

Time: 0.588 ms
```

*FULL JOIN для соединения трёх таблиц с возвратом всех записей*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
FULL JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код"
FULL JOIN "Справочник модулей АСУП" mz ON z."Модуль" = mz."Код";
                                         QUERY PLAN
---------------------------------------------------------------------------------------------
 Hash Full Join  (cost=22.70..243.20 rows=2679 width=3533)
   Hash Cond: (z."Модуль" = mz."Код")
   ->  Hash Full Join  (cost=11.57..195.72 rows=2679 width=1981)
         Hash Cond: (z."Тема обращения" = tz."Код")
         ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
         ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
               ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
   ->  Hash  (cost=10.50..10.50 rows=50 width=1552)
         ->  Seq Scan on "Справочник модулей АСУП" mz  (cost=0.00..10.50 rows=50 width=1552)
(9 rows)

Time: 0.788 ms
```

*FULL JOIN с условием возврата записей, для которых нет соответствий в одной из таблиц*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
FULL JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код"
WHERE z."Код" IS NULL OR tz."Код" IS NULL;
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Hash Full Join  (cost=11.57..195.72 rows=13 width=1981)
   Hash Cond: (z."Тема обращения" = tz."Код")
   Filter: ((z."Код" IS NULL) OR (tz."Код" IS NULL))
   ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(6 rows)

Time: 0.544 ms
```
## Реализовать запрос, в котором будут использованы разные типы соединений

*Соединяем JOIN с одной таблицей, но при этом LEFT JOIN с другой таблицей, чтобы увидеть все заявки по программам с темами и без них*
```
postgres=# EXPLAIN
SELECT *
FROM "Заявки АСУП" z
JOIN "Справочник модулей АСУП" mz ON z."Модуль" = mz."Код"
LEFT JOIN "Тип заявок" tz ON z."Тема обращения" = tz."Код";
                                            QUERY PLAN
---------------------------------------------------------------------------------------------------
 Hash Left Join  (cost=22.70..242.98 rows=2631 width=3533)
   Hash Cond: (z."Тема обращения" = tz."Код")
   ->  Hash Join  (cost=11.12..219.27 rows=2631 width=2493)
         Hash Cond: (z."Модуль" = mz."Код")
         ->  Seq Scan on "Заявки АСУП" z  (cost=0.00..171.79 rows=2679 width=941)
         ->  Hash  (cost=10.50..10.50 rows=50 width=1552)
               ->  Seq Scan on "Справочник модулей АСУП" mz  (cost=0.00..10.50 rows=50 width=1552)
   ->  Hash  (cost=10.70..10.70 rows=70 width=1040)
         ->  Seq Scan on "Тип заявок" tz  (cost=0.00..10.70 rows=70 width=1040)
(9 rows)
```
## *3 своих метрики на основе показанных представлений


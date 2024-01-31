# Postgres_2.0
<a id="contents"></a>
[Домашнее задание №1 (Первичная настройка ОС и PostgreSQL БД)](#1)

[Домашнее задание №2 (Коннектинг к PostgreSQL. Права пользователей)](#2)
<a id="1">
## Домашнее задание №1 (Первичная настройка ОС и PostgreSQL БД)
Конфигурация ВМ:
 - Ubuntu 20.04.6 LTS
 - 8 CPU
 - 32 ГБ оперативки
 - 250 ГБ ssd

Установлен Postgres 15.

Генерация данных:
`pgbench -i -s 6000 postgres`

Тестируем на дефолтных настройках Postgres.

mode simple: `pgbench -c N -j 8 -T 1800 postgres`, где с - кол-во клиентов. Число имитируемых клиентов, то есть число одновременных сеансов базы данных.

| Метрики / Тест | 5 клиентов | 50 клиентов | 100 клиентов | 
| ----------- | ----------- | ----------- | ----------- |
| CPU    | 30-35% | 50-75% | 60-80% |
| ОЗУ    | 1.4 ГБ | 1.4 ГБ  | 1.4 ГБ |
| latency average    | 2.391 ms  | 10.897 ms  | 18.689 ms |
| tps    | 2091  | 4588   | 5350   |




mode extended: `pgbench -M extended -c N -j 8 -T 1800`, где с - кол-во клиентов. Число имитируемых клиентов, то есть число одновременных сеансов базы данных.

| Метрики / Тест | 5 клиентов | 50 клиентов | 100 клиентов | 
| ----------- | ----------- | ----------- | ----------- |
| CPU    | 25-30% | 60-80% | 70-85% |
| ОЗУ    | 1.4 ГБ | 1.4 ГБ  | 1.4 ГБ |
| latency average    | 2.256 ms  | 10.024 ms  | 22.463 ms |
| tps    | 2216  | 4988   | 4451   |

# Postgres_2.0
<a id="contents"></a>
[Домашнее задание №1 (Первичная настройка ОС и PostgreSQL БД)](#1)

[Домашнее задание №2 (Коннектинг к PostgreSQL. Права полþзователā)](#2)
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

Тестируем на дефолтных настройках Postgres: 
`pgbench -c 5 -j 8 -T 1800 postgres`


| Метрики / Тест | 5 клиентов | 50 клиентов | 100 клиентов | 
| ----------- | ----------- | ----------- | ----------- |
| Загрузка CPU    | Ячейка 2   | Ячейка 2   | Ячейка 2   |
| latency average    | Ячейка 4   | Ячейка 4   | Ячейка 4   |
| tps    | Ячейка 4   | Ячейка 4   | Ячейка 4   |

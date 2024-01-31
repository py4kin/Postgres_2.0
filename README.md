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
 - Загрузка CPU: 30 - 35%
 - latency average = 2.391 ms
 - tps = 2091.285125

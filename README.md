# Postgres_2.0
<a id="contents"></a>
[Домашнее задание №1 (Первичная настройка ОС и PostgreSQL БД)](#1)

[Домашнее задание №2 (Коннектинг к PostgreSQL. Права пользователей)](#2)

Конфигурация ВМ:
 - Ubuntu 20.04.6 LTS
 - 8 CPU
 - 32 ГБ оперативки
 - 250 ГБ ssd

Установлен Postgres 15.

Генерация данных:
`pgbench -i -s 6000 postgres`
<a id="1">
### Домашнее задание №1 (Первичная настройка ОС и PostgreSQL БД)
### Тестируем на дефолтных настройках Postgres.

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
| tps    | 2216  | 4988   | 4451  |

#### При дефолтных значених Postgres оперативка не привышала 1.4 ГБ. При увеличение кол-во клиентов увеличивались потребление CPU и кол-во tps. В последнем прогоне в mode extended кол-во tps просело.

Измениили настройки по умолчанию (кибертек):
 - shared_buffers
 - work_mem
 - maintenance_work_mem
 - huge_pages
 - и другие

### Тестируем на измененных настройках Postgres.

mode simple: `pgbench -c N -j 8 -T 1800 postgres`

| Метрики / Тест | 5 клиентов | 50 клиентов | 100 клиентов | 
| ----------- | ----------- | ----------- | ----------- |
| CPU    | 30-40% | 75-82% | 88-92% |
| ОЗУ    | 1.64 ГБ | 2.6 ГБ  | 3.05 ГБ |
| latency average    | 2.717 ms  | 10.525 ms  | 20.729 ms |
| tps    | 1839  | 4750   | 5850   |

mode extended: `pgbench -M extended -c N -j 8 -T 1800 postgres`

| Метрики / Тест | 5 клиентов | 50 клиентов | 100 клиентов | 
| ----------- | ----------- | ----------- | ----------- |
| CPU    | 35-40% | 85-90% | 87-92% |
| ОЗУ    | 1.43 ГБ | 2.23 ГБ  | 2.77 ГБ |
| latency average    | 2.682 ms  | 10.632 ms  | 21.131 ms |
| tps    | 1864  | 4702   | 4685   |

#### При измененных значених Postgres оперативка стала расти до нужного уровня. Потребление CPU стало более плавным и немного выросло в утилизации.

### Увеличиваем max_connections=200 чтобы попытаться загрузить CPU на 100%.
mode simple: `pgbench -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 80-90% |
| ОЗУ    | 4.88 ГБ |
| latency average    | 39.864 ms |
| tps    | 4991 |

mode extended: `pgbench -M extended -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 90-93% |
| ОЗУ    | 4,84 ГБ |
| latency average    | 36.947 ms |
| tps    | 5386 |

#### При увеличение кол-во max_connections=200 и при тестах с кол-во клиентом=200 утилизация процессора в 100% не ушла, зато начала расти оперативка. Кол-во tps соответственно подросло.

### Отключим синхронный коммит:  
`synchronous_commit = off`  
mode simple: `pgbench -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 94-97% |
| ОЗУ    | 4.88 |
| latency average    | 30.196 ms |
| tps    | 6590 |

mode extended: `pgbench -M extended -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 94-98% |
| ОЗУ    | 4.93 |
| latency average    | 33.287 ms |
| tps    | 5978 |

### Выключаем синхронную запись на диск:  
`fsync=off`  
mode simple: `pgbench -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 90-95% |
| ОЗУ    | 4.94 |
| latency average    | 288.387 ms |
| tps    | 6313 |

### Вообще отключаем синхронную запись на диск:
`full_page_writes=off`  
mode simple: `pgbench -c 200 -j 8 -T 1800 postgres`

| Метрики / Тест | 200 клиентов |
| ----------- | ----------- |
| CPU    | 92-97% |
| ОЗУ    | 4.81 |
| latency average    | 26.773 ms |
| tps    | 7432 |

## Вывод:
 - Не заметил разницы между simple и extended modes. Где-то хуже, где-то лучше.
 - При увеличение числа клиентов растет утилизация CPU. Если кол-во клентов равно числу max_connections начинается утилизация оперативки.
 - Отключение свойства надежности (D) помогает выиграть в кол-ве tps, но это черевато если сервер завершит работу аварийно.

### Дополнительные исследования:  
При выставление параметра `shared_buffers=0` Postgres не запускатеся.
При выставление параметра `shared_buffers` в минимальное значение, например `shared_buffers=1` при нагрузке Postgres выдает ошибку:  
`pgbench: error: client 64 script 0 aborted in command 8 query 0: ERROR:  no unpinned buffers available`

[Оглавление](#contents)

<a id="2">
 
### Домашнее задание №2 (Коннектинг к PostgreSQL. Права пользователей)
Описание ВМ в шапке.  
Настройки ПГ из первого задания, взатые с кибертека,за исключением: max_connections = 1000  

### Тестируем Ванильный ПГ:
`pgbench -c 777 -j N -T 600 postgres`, где j - число потоков.

| Метрики / Тест | j=1 | j=8 |
| ----------- | ----------- | ----------- |
| CPU    | 95% | 95-96% |
| ОЗУ    | 13.5 | 13,5 |
| latency average  | 219.657 ms | 230.689 ms |
| tps    | 3537 | 3368 |

#### Закачали БД thai на 60 ГБ.
`pgbench -c 777 -j N -T 600 -f ~/workload.sql -U postgres thai`, где j - число потоков.
| Метрики / Тест | j=1 | j=8 |
| ----------- | ----------- | ----------- |
| CPU    | 95-98% | 100% |
| ОЗУ    | 7.5 | 7,5 |
| latency average  | 22.030 ms | 22.928 ms |
| tps    | 35270 | 33888 |

График CPU 4 прогонов соответсвенно:
![Схема](/homework_№2/ванильный_пг_cpu.png)  
График транзакций 4 прогонов соответсвенно:
![Схема](/homework_№2/ванильный_пг_transactions.png)  

График connections 4 прогонов соответсвенно:
![Схема](/homework_№2/ванильный_пг_connections.png)  

График idle sessions 4 прогонов соответсвенно:
![Схема](/homework_№2/ванильный_пг_idle_sessions.png)  

### Увеличение числа потоков у pgbench не дает никакого разультата.

#### Устанавливаем pgbouncer.

`pgbench -c 777 -j 1 -T 600 -U postgres -p 6432 -h 127.0.0.1 postgres`.  
Через две минуты, за это время прошло 19 коннекторов, pgbench начал выдавать ошибки.
```
pgbench: error: client 20 script 0 aborted in command 4 query 0: FATAL:  query_wait_timeout
server closed the connection unexpectedly
        This probably means the server terminated abnormally
        before or while processing the request.
```
Увеличил в `pgbouncer`: `query_wait_timeout = 600` и `max_client_conn = 1000`

`pgbench -c 777 -j 1 -T 600 -U postgres -p 6432 -h 127.0.0.1 postgres`

| Метрики / Тест | Тест |
| ----------- | ----------- |
| CPU    | 78% |
| latency average  | 352.991 ms |
| tps    | 2201 |

`pgbench -c 777 -j 1 -T 600 -f ~/workload.sql -U postgres -p 6432 -h 127.0.0.1 thai`

| Метрики / Тест | Тест |
| ----------- | ----------- |
| CPU    | 75% |
| latency average  | 45.086 ms |
| tps    | 17233 |

График CPU 2-ух прогонов через pgbouncer:
![Схема](/homework_№2/pgbounser_default_cpu.png)

График транзакций 2-ух прогонов через pgbouncer:
![Схема](/homework_№2/pgbounser_default_transactions.png)

График idle 2-ух прогонов через pgbouncer:
![Схема](/homework_№2/pgbounser_default_idle_sessions.png)

#### Число tps упало, назрузка на CPU упала. Обратим внимание на последний график idle_sessions. Верхняя граница графика "работает" ровно на отметки в 20 штук. В pgbouncer есть параметр default_pool_size, который равен по умолчанию 20.
Увеличил в `pgbouncer`: `default_pool_size=300`  
'pgbench -c 777 -j 1 -T 600 -f ~/workload.sql -U postgres -p 6432 -h 127.0.0.1 thai'

| Метрики / Тест | Тест |
| ----------- | ----------- |
| CPU    | 95% |
| latency average  | 35.904 ms |
| tps    | 21641 |

График CPU прогоина через `pgbouncer default_pool_size=300`:
![Схема](/homework_№2/pgbounser_tun_cpu.png)
График Connections прогоина через `pgbouncer default_pool_size=300`:
![Схема](/homework_№2/pgbounser_tun_connections.png)
График transactions прогоина через `pgbouncer default_pool_size=300`:
![Схема](/homework_№2/pgbounser_tun_transactions.png)
График edli sessions прогоина через `pgbouncer default_pool_size=300`:
![Схема](/homework_№2/pgbounser_tun_idle_sessions.png)
</a>

#### При использование pgbouncer дает плюсы

[Оглавление](#contents)

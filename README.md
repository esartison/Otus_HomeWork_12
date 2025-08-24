# Домашнее задание Сартисона Евгения №12

Решил выбрать следующий вариант, чтобы разобраться с CockroachDB

🛠 Вариант 1: CockroachDB в облаке
Разверни CockroachDB в облаке
Используй датасет 10 Гб+
Сравни производительность запросов с однопользовательским инстансом PostgreSQL
Опиши процесс, решения и возникшие сложности


## **(1) Подготовка к задаче**

Решил создать 2 абсолютно одинаковые VM в Yandex Cloud и установить обе базы с настройками по умолчанию.


**CockroachDB**
<img width="692" height="803" alt="image" src="https://github.com/user-attachments/assets/5093bdaf-69ff-4f29-bc7a-9ffe89523683" />


**PostgreSQL**
<img width="720" height="822" alt="image" src="https://github.com/user-attachments/assets/fe005783-9953-433a-bbf0-27b21865459e" />



## **(2) Установка CockroachDB **

Пошел по иструкции из [Как установить кластер CockroachDB на Ubuntu 24.04](https://itshaman.ru/articles/4879/kak-ustanovit-klaster-cockroachdb-na-ubuntu-2404)

Установил Cockroachdb
<img width="1441" height="752" alt="image" src="https://github.com/user-attachments/assets/75be0c48-6cb9-47bf-afca-d77339e70575" />

Проверка подключения
<img width="1463" height="586" alt="image" src="https://github.com/user-attachments/assets/2c1ef1c5-6fee-40ae-a060-98d80e4f7d27" />

создать базу mydb
<img width="1384" height="536" alt="image" src="https://github.com/user-attachments/assets/4a0855b8-10fa-4f4f-b8ed-fcf61bbbe581" />

Создать тестовый набор данных
```
root@localhost:26257/defaultdb> use mydb;
SET
Time: 6ms total (execution 6ms / network 0ms)

root@localhost:26257/mydb> CREATE TABLE foo (
                        ->     id INT PRIMARY KEY,
                        ->     name VARCHAR(255),
                        -> data VARCHAR(4000)
                        -> );
CREATE TABLE
Time: 115ms total (execution 115ms / network 0ms)

INSERT INTO foo(id, name, data) SELECT i, 'name'||i, random() FROM generate_series(1,1000000) i;

root@localhost:26257/mydb> SELECT sum(range_size_mb) FROM [SHOW RANGES FROM TABLE foo  WITH DETAILS];
            sum
---------------------------
  14118.38162900000000000
(1 row)

Time: 1.823s total (execution 1.812s / network 0.011s)
```
загрузил таблицу размером 14Gb.

root@localhost:26257/mydb> select max(id) from foo;
     max
-------------
  100000000
(1 row)

Time: 3ms total (execution 3ms / network 0ms)


## **(2) Установка PostgreSQL **
Установил Postgres и создал базу
```
pgdb001:установка Postgres 
sudo apt install postgresql
fixing permissions on existing directory /var/lib/postgresql/16/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Setting up postgresql (16+257build1.1) ...
Processing triggers for man-db (2.12.0-4build2) ...
Processing triggers for libc-bin (2.39-0ubuntu8.5) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```
<img width="1290" height="263" alt="image" src="https://github.com/user-attachments/assets/8d92b0a0-246a-4bb3-8930-722b44cd572b" />




Создать тестовый набор данных
```
mydb=# CREATE TABLE foo (
mydb(#     id INT PRIMARY KEY,
mydb(#     name VARCHAR(255),
mydb(# data VARCHAR(4000)
mydb(# );
CREATE TABLE
mydb=#



```
загрузил таблицу размером 14Gb.



🗂 Формат сдачи

Репозиторий с инструкциями, скриптами и конфигурациями (если есть).
Скриншоты, логи или видео с тестированием отказоустойчивости и производительности.
Краткий отчёт с выводами и рекомендациями.

Критерии оценки:
Развёрнут мульти-мастер кластер в доступной среде (российское облако или локально).
Проведено тестирование с данными объёмом не менее 10 ГБ.
Представлен отчёт с описанием развертывания, проблем и результатов тестов.

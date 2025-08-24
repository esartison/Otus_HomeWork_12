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
  11367.38162900000000000
(1 row)

Time: 1.823s total (execution 1.812s / network 0.011s)
```
загрузил таблицу размером 11Gb.




## **(2) Установка PostgreSQL **

```
pgdb001:регистрация репозитория postgresql.org
sudo apt install -y postgresql-common sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh

pgdb001:установка Postgres 17
sudo apt install postgresql-17

```

🗂 Формат сдачи

Репозиторий с инструкциями, скриптами и конфигурациями (если есть).
Скриншоты, логи или видео с тестированием отказоустойчивости и производительности.
Краткий отчёт с выводами и рекомендациями.

Критерии оценки:
Развёрнут мульти-мастер кластер в доступной среде (российское облако или локально).
Проведено тестирование с данными объёмом не менее 10 ГБ.
Представлен отчёт с описанием развертывания, проблем и результатов тестов.


(a) Установка Clickhouse
```
apt-get install -y apt-transport-https ca-certificates dirmngr
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 8919F6BD2B48D754
echo "deb https://packages.clickhouse.com/deb stable main" | sudo tee /etc/apt/sources.list.d/clickhouse.list
apt update
apt-get install -y clickhouse-server clickhouse-client
```

(б) разрешаем удаленные подключения к нашему серверу:
```
student:~$ sudo diff /etc/clickhouse-server/config.xml /etc/clickhouse-server/config.xml_bkp
269c269
<     <listen_host>0.0.0.0</listen_host> 
---
>     <!-- <listen_host>0.0.0.0</listen_host> -->
```

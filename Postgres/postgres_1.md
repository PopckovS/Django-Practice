Что такое `Peer`
---

Входим в postgres как суперпользователь

    sudo -u postgres psql

Меняем пароль для пользователя

    ALTER USER postgres PASSWORD '<new-password>';

Меняем настройки доступа в файле с настройками для постгресса, он 
находится по пути
`/etc/postgresql/<postgres_version>/main/pg_hba.conf`

Меняем доступ с `peer`

    local   all         all                  peer

на доступ типа `md5`

    local   all         all                  md5

Перезагружаем сервис постгресса

     sudo service postgresql restart


---
Утилита `psql` для работы с СУБД `PostgreSQL`
---

При установке `PostgreSQL` по дефолту с ним устанавливается утилита `psql`
которая предоставляет доступ к управлению СУБД



---
Сервис `Postgresql` 
---
Обратиться к сервису `Postgresql` в терминале Linux 


    sudo service postgresql status
    sudo service postgresql start
    sudo service postgresql stop
    sudo service postgresql restart

---
Подключение к `PostgresQL` через URL 
--
К БД можно подключаться через URL 

    # Шаблон
    postgres://<пользователь>:<пароль>@<хост>:<порт>/<название БД>

    # Пример
    postgres://postgres:postgrespassword@postgres:5432/db_name


---
Утилита `psql`
---

> Войти в БД под указанным пользователем

    psql -U <user_name>
    psql -U postgres

---

Посмотреть все Базы Данных что есть в наличии

    \l

    # Вывод
       Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
    -----------+----------+----------+------------+------------+-----------------------
     postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
     template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
     template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres

---

Подключиться к определенной БД, и все последующие команды по работе будут применяться
к этой БД, сделать это можно 2 командами:

    # первая
    \connect <DBNAME>;
    \connect postgres;

    # вторая
    \c <DBNAME>;
    \c postgres;

---

Посмотреть список всех таблиц что есть в БД

    \dt


    # Выводим таблицы
          List of relations
     Schema |   Name   | Type  |  Owner   
    --------+----------+-------+----------
     public | profiles | table | postgres
    (1 row)


    # Если таблиц не обнаружено то поулчим вывод
    No relations found.

---
Утилита `pg_dump`
---

[ Почитать про `pg_dump` ](https://postgrespro.ru/docs/postgresql/11/app-pgdump)

`pg_dump` - утилита для создания дампов СУБД `Postgres`, можно делать как
дамп локальной Бд, так и удаленной.

Утилита `pg_dump` находится по пути `/usr/bin/pg_dump`


Дамп только схемы БД

> pg_dump --host <хост> --port <порт> --username <пользователь> --blobs --verbose --encoding utf-8 --schema-only --file "путь к файлу" "название бд"


Дамп всей БД

> pg_dump --host <хост> --port <порт> --username <пользователь> --blobs --verbose --encoding utf-8 --file "путь к файлу" "название бд"


Пример запроса

>pg_dump --host 192.168.4.197 --port 5432 --username postgres --blobs --verbose --encoding utf-8 --file "/home/sergio/Документы/Проекты/bd_name/bd_name" "bd_name"


---
Полезные запросы к `Postgres` 
---
Многие полезные команды SQL требуется сделать через терминал

- Создание БД

    >    CREATE DATABASE db_name;

- Изменить пароль для пользователя

    >    ALTER USER user_name WITH ENCRYPTED PASSWORD '123';

- Переименовать БД, сделать это можно если нет активных подключений к ней

    > ALTER DATABASE old_name_db RENAME TO new_name_db;
    
    Если есть активные подключения к БД то получим
      
    >WARNING:  You need to manually restart any running background workers after this command.
    > ERROR:  database "old_name_db" is being accessed by other users
    > DETAIL:  There are 2 other sessions using the database.
    
    Если все удачно получим сообщение
    > ALTER DATABASE

  

---
Расширение `TimescaleDB`
---

[Почитать про это](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-timescaledb-on-ubuntu-18-04-ru)

`timescaledb` - база данных с открытым исходным кодом, 
оптимизированная для хранения данных временного ряда. 
Она реализуется как расширение PostgreSQL и сочетает в себе
удобство реляционных баз данных и быстродействие баз данных NoSQL. 
Таким образом, она позволяет использовать PostgreSQL 
для хранения бизнес-данных и данных временного ряда в одном месте.

С начала устанавливает репозиторий в саму ОС

```
sudo add-apt-repository ppa:timescale/timescaledb-ppa

sudo apt update

sudo apt install timescaledb-postgresql-10
```

---

Для того что бы `timescaledb` можно было установить в postgreSQL
как расширение, потребуется отредактировать файл с настройками 
`postgresql.conf`

Проходим по пути, где находится файл настроек для конкретной версии 
`postgresql`

```
/etc/postgresql/10/main/postgresql.conf
```

Редактируем файл

```
sudo nano postgresql.conf
```

Меняем настройки

```
# заменяем эту настройку
shared_preload_libraries = ''             # (change requires restart)

# указывая timescaledb
shared_preload_libraries = 'timescaledb'  # (change requires restart)
```

Далее перезагружаем сервис `postgresql`

```
sudo service postgresql restart
```

После этого `timescaledb` станет доступным в списке расширений постгресса

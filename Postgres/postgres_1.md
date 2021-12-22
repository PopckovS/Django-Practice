Утилита `psql` для работы с СУБД `PostgreSQL`
---
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

    postgres://<пользователь>:<пароль>@<хост>:<порт>/<название БД>

Пример

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









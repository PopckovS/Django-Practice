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

  

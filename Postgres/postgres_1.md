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

  

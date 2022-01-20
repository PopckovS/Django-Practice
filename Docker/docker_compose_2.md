Примеры запуска сервисов с `docker-compose`
---
---

Запуск СУБД `Postgres` в `docker-compose`
---
Создаем файл `docker-compose.yml` и запускаем `postgres` как сервис.

`Postgres` хранит данные в виде файлов, когда мы запускаем контейнер,
все данные будут сохраняться в этом контейнере, но в случае удаления
контейнера, все данные будут удалены вместе с контейнером.

Чтобы использовать `Postgres` только как программу, но сами данные
сохранять даже после удаления контейнера, с помощью специальной
опции `volumes` пробросим создаваемые файлы из контейнера в 
основную операционную систему.

**Опция volumes**

- `/docker_data/postgresql_01` - первый параметр указывает абсолютный 
путь основной ОС, сюда будут помещены данные СУБД `Postgres` которые
создаются внутри контейнера.

- `/var/lib/postgresql/data` - второй параметр, указывает путь внутри 
контейнера, откуда будут взяты данные для перемещения в основную ОС.

Нам требуется как то обращаться к СУБД внутри контейнера, для этого 
с помощью опции `ports` пробросим порт изнутри контейнера в основную
ОС, `9001:5432`, `5432` - это стандартный порт внутри контейнера, 
который будет доступен по порту `9001` в основной ОС.

При первом запуске контейнера, требуется указать переменные окружения,
`POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB` для указания какой
пользователь и c каким паролем будет создан, также БД будет создана тоже
при первом запуске.

Содержимое файла `docker-compose.yml`
```yaml
version: '3.5'  # Версия используемого Docker-compose
services:       # Указываем сервисы, каждый сервис это отдельный контейнер

  # Сервис(контейнер) тут будет запущена СУБД postgres 
  postgresql_01:
    image: postgres # образ что будет запущен в контейнере
    container_name: postgresql_01 # название контейнера
    restart: always # перезагружать контейнер или нет ?

    # Сама программа postgres запущена в контейнере, но данные будем хранить 
    # в основной ОС, для этого пробрасываем данные <Основная ОС>:<В контейнере>
    volumes:
      - /docker_data/postgresql_01:/var/lib/postgresql/data

    # Переменные окружения с которыми будет запущен контейнер, для первого 
    # запуска СУБД задаем пользователя и пароль
    environment:
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: 777
      POSTGRES_DB: "microservices"
    ports:
      - 9001:5432
```

---
Запуск `Django` и `Postgres` в `docker-compose`
---

Имеем следующую структуру проекта:
``` 
service_1/
    docker-compose.yml 
    
    main/
        .env   # переменные окружения для Django проекта

    postgres/
        data/  # тут будут сохранены данные БД
        .env   # переменные окружения для БД Postgres
```


Содержимое файла `docker-compose.yml`
```yaml
version: '3.5'
services:

# Сервис №1 
# Django приложения
  main_django:
    container_name: main_django
    restart: always
    
    # Мы используем либо image для указания уже самого образа,
    # либо используем build для создания этого образа    
    build:
      context: ./main  # указываем путь к файлам проекта
      dockerfile: Dockerfile # указываем файл для сборки, это Dockerfile  
      network: host  # даем контейнеру тот же список разрешенных адресов, 
      # что есть и у хоста, если этого не сделать то из контейнера 
      # не получиться обращаться к внешним адресам в сети, и не получиться
      # установить зависимости.      
      
    # образ был собран, когда контейнер запускается с ним
    # и выполняется эта команда, для запуска сервера Django    
    command: python manage.py runserver 0.0.0.0:8000
    
    # объединяем пути директорий между хостом и контейнером 
    volumes:
      - ./main:/usr/src/app
    
    # для запуска Django проекта указываем файл с переменными окружения
    # его можно было указывать в Dockerfile но можно и в docker-compose      
    env_file:
      - ./main/.env
    
    # пробрасываем порты
    ports:
      - 9000:8000
    
    # запускать Django после запуска БД Postgres    
    depends_on:
      - main_postgres


  # Сервис №2
  # Postgres для основного приложения
  main_postgres:
    image: postgres   # образ Postgres 
    container_name: main_postgres # название для контейнера
    restart: always
    
    # куда будем сохранять данные БД
    volumes: 
       - ./postgres/data:/var/lib/postgresql/data
    
    # файл с переменными окружения для запука БД     
    env_file:
      - ./postgres/.env
        
    # проброска портов, чтобы к БД можно было подключиться с основного
    # хоста, чтобы работать с БД через DBeawer
    ports:
      - 9001:5432
```


---
Запуск `MetaBase` + `Postgres` в `docker-compose`
---

`MetaBase` - Это BI инструмент, визуализирует данные из БД в удобном 
виде, для работы требуется 3 вещи:

- Образ самого `MetaBase`
- БД для хранения мета данных о настройках и запросах самого `MetaBase`,
я использую `Postgres`
- БД к которой `MetaBase` будет подключаться, чтобы черпать из нее данные
для отображения, я использую `Postgres`

> <span style="color:red"> ОЧЕНЬ ВАЖНО ! </span>
> 
> БД из которых `MetaBase` может черпать данные может быть несколько,
> их можно указывать в самом `MetaBase` во время непосредственной работы,
> но ! У нас есть 2 варианта подключения БД `Postgres` для хранения мета 
> ?данных самого `MetaBase` мы можем развернуть отдельный контейнер для 
> `Postgres` в котором и будем хранить данные самого `MetaBase` и подключение
> к этой `Postgres` будет указано в переменных окружения с которыми будет 
> запущен сам контейнер `MetaBase` таким образом в этой БД `Postgres` и будут 
> созданы системные таблицы самого `MetaBase` а все дальнейшие подключаемые
> БД будут источниками данных.
> 
> Но, если на момент запуска `MetaBase` не указывать ему той БД которая будет 
> использована для хранения данных самого `MetaBase`, то в этом случае,
> первая же БД которая будет указана как источник данных для `MetaBase`, 
> станет и той БД в которой `MetaBase` сам создаст свои системные таблицы,
> и таким образом эта БД будет хранить и свои таблицы с данными и заимеет 
> системные таблицы самого `MetaBase`
>
> Лучше всего запускать один контейнер для `MetaBase` и один контейнер для
> той БД что будет хранить его данные, так и поступим. 

[Почитать про переменные окружения для запуска MetaBase](https://www.metabase.com/docs/latest/operations-guide/environment-variables.html)

```yaml

  # Metabase приложение
  metabase-app:
    image: metabase/metabase # Официальное изображение Metabase
    restart: always
    
    # Metabase внутри запускается на 3000 порту,
    # прокидываем в хост машину на 9002 порт      
    ports:
      - "9002:3000" 
      
    # Эти переменные окружения, задают ту БД в которой
    # будут храниться мета данные самого Metabase     
    environment:
      MB_DB_TYPE: postgres
      MB_DB_DBNAME: metabase
      MB_DB_PORT: 5432
      MB_DB_USER: postgres
      MB_DB_PASS: 777
      MB_DB_HOST: postgres_metabase # ХОСТ это название контейнера в 
      # в котором запускается Postgres 
    
    # Запускать после запуска контейнера с БД для Metabase   
    depends_on:
      - postgres_metabase
    links:
      - postgres_metabase


  # Postgres для Metabase приложения
  postgres_metabase:
    image: postgres
    container_name: postgres_metabase
    restart: always
    
    # Для хранения данных Postgres между уничтожениями контейнеров     
    volumes:
       - ./postgres_metabase/data:/var/lib/postgresql/data
    
    # Дефолтный порт Postgres 5432 прокидываем в хост машину, 
    # что бы можно было подключиться скажем их DBeaver и посмотреть.        
    ports:
      - "9003:5432"
    
    # Указывая переменные окружения Postgres при первом запуске,
    # именно этими настройками будет создан пользователь с паролем и БД
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: 777
      POSTGRES_DB: metabase
```

Таким образом запускаем 2 контейнера с общей сетью, через общую
сеть происходит общение между контейнерами, а БД которые `Metabase`
использует как источники данных, подключаются уже в самом `Metabase`
в его административной части, тут мы только запускаем контейнер БД
для хранения мета данных самого `Metabase`

---
Запуск `Django` + `Django Celery` + `Redis` в `docker-compose`
---

Нам требуется запустить проект на `Django` и `Celery` для асинхронности,
`Redis` как бэк энд результатов.

Создадим 3 сервиса, один для `Django` один для `Celery` который будет 
собираться с того же проекта, что и с `Django`, `Redis` можно запустить
из официального образа который мы возьмем с `Docker Hub` 

```yaml
version: '3.5'
services:

  # Сервер на Django
  project_django:
    build:
      context: ./project
      dockerfile: DockerFileDjango
      network: host
    container_name: project_django
    restart: always
    ports:
      - "8000:8000"
    depends_on:
      - project_redis
    env_file:
      - ./.env

  # Celery для Django
  project_celery:
    container_name: project_celery
    build:
      context: ./project
      dockerfile: DockerFileCelery
      network: host
    depends_on:
      - project_django
      - project_redis
    env_file:
      - ./.env

  # Redis для Django + Celery
  project_redis:
    image: redis:alpine
```

В папке `project` храним наш `Django` проект со всеми приложениями и файлом
`DockerFileDjango`

Файл `DockerFileDjango` служит для сборки образа `Django` проекта:

```dockerfile
FROM python:3.8

ENV PYTHONUNBUFFERED 1

WORKDIR /usr/src/app

COPY ./requirements.txt .

RUN pip install -r requirements.txt

COPY . .

RUN chmod +x ./docker-entrypoint.sh
ENTRYPOINT [ "./docker-entrypoint.sh" ]
```

Этот файл `DockerFileDjango` запускает файл с командами запуска контейнера
`docker-entrypoint.sh` в котором исполняются следующие команды с запуском
фикстур и запуском сервера.

```dockerfile
#!/bin/bash

echo "Применяем миграции"
python manage.py migrate

echo "Запускаем фикстуры"
python manage.py loaddata fixtures/*

echo "Запуск сервера"
python manage.py runserver 0.0.0.0:8000
```

Все эти шаги позволяют нам запустить контейнер с `Django` проектом, 
`Celery` запускается на основе того же проекта, но требует иные команды 
 запуска контейнера, по этому мы имеем 2 файла для запуска контейнера,
файл `DockerFileDjango` служит для запуска `Djnago` проекта.
Файл `DockerFileCelery` хранится в том же проекте, но служит для запуска
`Celery` проекта.



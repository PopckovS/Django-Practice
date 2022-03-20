Backend разработка.
---
Это сборник различной полезной информации и решения практических задача по
`Backend` разработке, от `Devops` до создания `Django` проектов.

---
Теория
---

1. [ Архитектура ]( Theory/1.md )
    - Монолитная и Микросервисная архитектура


2. [ Паттерны ]( Theory/2.md )
    - `Singleton`


---
Администрирование `Linux` `Devops`
---

**[ Сборник всех команд ](Devops/1.md)**
 

1. **[ Команды Linux ](Devops/2.md)**  
   - Сеть `iptables` и утилита `ufw`


2. [ Механизм **CI / CD** ](Devops/Lesson_CI_CD.md)


---
СУБД `PostgresQL` и его администрирование
---

1. **[ Часть №1 ](Postgres/postgres_1.md)**
    - Утилита `psql`, `pg_dump`
    - Взаимодействие с СУБД `Postgres` через терминал
    - Расширение `TimescaleDB`


2. **[ Часть №2 ](Postgres/postgres_2.md)**

---
Сервер `Gunicorn` /  `Nginx` + `Django` 
---

1. **[ Сервер `Gunicorn` ](Server/gunicorn_1.md)**
    - Запуск сайта на сервере `Gunicorn`


2. **[ Сервер `Nginx` ](Server/nginx_1.md)**
    - Запуск сайта на сервере `Nginx`

---
Docker, Docker-compose
---

[ Удобный сборник команд ](Docker/docker_commands.md)

1. **[Docker : ](Docker/docker_1.md)**
    - Образы и контейнеры `Images`, `Container`
    - Файл `Dockerfile`
    - Просмотр логов


2. **[ №1 Docker-Compose : ](Docker/docker_compose_1.md)** 
    - Файл `docker-compose.yml`
    - Как работает `volumes`


3. **[ №2 Docker-Compose : ](Docker/docker_compose_2.md)**
    - Порядок запуска контейнеров 


4. **[ Примеры запуска сервисов в Docker-Compose ](Docker/docker_compose_3.md)**
    - `Postgres` 
    - `Django` + `Postgres` 
    - `MetaBase` + `Postgres`
    - `Django` + `Django Celery` + `Redis` 
    - `Nginx` + `Vue`

---
GIT и Git-flow
---
[ Удобный сборник команд `git` ](git/commands.md)

1. [ Система контроля версий `GIT` ](git/git_1.md)
    - `git rebase`
    - Конфликты
    - Тэги `git tag`
    - Система ведения репозиториев `Git-flow`
    
---
Работа с `GraphQL` и `Hasura`
---

1. **[ Часть №1 :](hasura/GraphQl_1.md)**
    - Что такое `GraphQL`, `Hasura` в докере
    - Типы запросов `query`, `mutation`, `subscription`
    - Отношения `object` и `array`


---
Фронт `Vue.js`
---

1. **[ Часть №1 :](Vue/vue_1.md)**
    - `Vue.js` установка, настройка, начало.

---
Web
---

1. **[ Часть №1 :](Web/web_1.md)**
    - `OSI`, `HTTP`, `HTTPS`

---
Настройка Django
---

1. [ Установка и начало ](Start/start_1.md)
    - Установка `Django`. Создание и подключение приложений


2. [ Часть №1. Безопасность настроек. Переменные окружения ](Start/venv_1.md)
   - Переменные окружения и оболочки в `Linux` 
   - Хранение настроек в переменных окружения и модуль `os.environ`
   

3. [ Часть №2. Безопасность настроек, модуль `django-environ` ](Start/venv_2.md)
   - Модуль `django-environ` его методы и примеры использования.


4. [ Настройки проекта `Django`. Файл `settings.py`](Start/settings.md)


---
Основное Django
---
[Сборник различных команд Django](Django/django_commands.md)


1. [ Request / Response ](Django/request_1.md)
    

2. [ Django Utils ](Django/django_utils_1.md)
   

3. [ Middleware ](Django/mid_1.md)
   
---
Модели, Запросы, Миграции
---
1. **Модели**
      - [ Установка. Создание Моделей ](Models/connect.md)
        - Настройка подключения к `Postgres` и расширение `PostGis`
        - Создание моделей

      - [ №1 Модели ](Models/Models_1.md)
        - Поля моделей и их опции
        - Поля для связей и поля для `GEO`
        - Ссылка модели на саму себя

      - [ №2 Модели ](Models/Models_4.md)
        - Настройки `MEDIA_ROOT`, `MEDIA_URl` для загрузки файлов
        - Класс модели `Meta`
        - Транзакции 
        
      - [ №3 Модели, Инспектор, Схемы, Множество БД. ](Models/Models_5.md)
        - Автоматическая генерация моделей, инспектор баз данных `inspectdb`
        - Работа с разными схемами, множественное подключение к разным БД
        - Маршрутизаторы баз данных


2. **Запросы ORM**

      - [ №1 ORM ](Models/orm_0.md)
        - Что такое `QuerySet` и его методы
        
      - [ №2 ORM ](Models/orm_1.md)
        - Методы: 
        - `save`, `create`, `all`, `get`, `filter`, `exclude`, `update`, `bulk_create`
        - Ограничения `LIMIT` и `OFFSET`
        - Приставки для фильтрации
        - Примеры запросов

      - [ №3 ORM ](Models/orm_2.md)
        - Выполнение прямых `SQL` запросов к БД
        - Агрегации `Count, Sum, Avg, Max, Min` 
        - Примеры запросов `ORM` :
          - Получение записей из среза по времени за множество дат 
          

3. **Миграции**
      - [ №1 Миграции ](Models/migrate_1.md)
        - Создание, применение и откат миграций, фейковые миграции.
        - Просмотр кода `SQL` что будет сделан миграцией
        - Чистый `SQL` в миграциях `migrations.RunSQL`
        
      - [ №2 Миграции ](Models/migrate_2.md) 
        - Ошибки при использовании миграций
        
      - [ Фикстуры ](Models/fixtures.md)
        - Выгрузка данных из БД `dumpdata`
        - Загрузка данных в БД `loaddata`
        
---
Админка
---

1. **[ Часть №1 ](admin/admin_1.md)** 
   - Регистрация моделей в админ панели
   - Управление видом моделей в админке 

     
2. **[ Часть №2 ](admin/admin_2.md)** 
    - Методы класса админки

       
3. **[ Часть №3 ](admin/admin_3.md)**
    - Флеш сообщения `messages`
    - Логирование `logging`

        
4. **[ Часть №4 ](admin/admin_4.md)**
    - Действия администратора
    - Как делать редирект на страницу админки
    - Отключение экранирования `HTML` в админке


5. **[ Часть №5 ](admin/admin_5.md)**
    - Управление порядком приложений и моделей в админке
    - `admin.site.index_template` ???

---
Формы
---

1. **[ Формы №1 ](Form/form_1.md)** 
     - Класс форм и типы полей формы
     

2. **[ Формы №2 ](Form/form_2.md)**
     - Токен `csrf` 
     

---
Валидаторы
---

1. **[ Часть №1 ](Validator/validator_1.md)**
     - Валидаторы в виде функции `ValidationError`
     - Валидация файлов на размер и формат
     - `RegexValidator`

---
Регистрация и Аутентификация в `Django`
---

1. **[ Введение ](Auth/auth_1.md)**
     - Пользователи их создание и смена паролей 
     - Создание суперпользователей
     - `login` `logout` `authenticate` - ?
     

2. **[ Дефолтная регистрация и авторизация в Django ](Auth/auth_2.md)**
     - Регистрация пользователей через форму `UserCreationForm`
     - Авторизация пользователей через форму `UserCreationForm`


3. **[ Модуль OAuth ](Auth/auth_3.md)**


---
Представления `html`, `css`, `js` 
---

1. **[ Статические файлы и переопределения шаблонов ](View/view_1.md)**
   - Пути `url` к представлениям. Шаблонизатор `Jinja2`.
   - Фильтры, `url` в шаблонах. Страницы ошибок и редиректы
   
[//]: # (2. **[ Часть №1 ]&#40;View/view_2.md&#41;**)
[//]: # (   - Статические файлы)

---
Асинхронность
---

1. **Django + Celery**
   - [ Часть №1 ](async/celery_1.md) (Установка)
   - [ Часть №2 ](async/celery_2.md) (Начало работы)
   - [ Часть №3 ](async/celery_3.md) (Flower логер сообщений для Celery)


2. **[ Django_rq ](async/django_rq_1.md)**
   - Что такое `Django_rq` 
   - Функции коллбэки `on_success`, `on_failure`
   - Как отлаживать асинхронный код в `django-rq`

---
Django Rest Framework `DRF`, `SSO`
---

1. Django Rest Framework `DRF`
      - [ Основы `DRF` ](DRF/drf_1.md) 
      - [ API Фильтрация, Поиск, Сортировка ](DRF/drf_2.md)
      - [ Аутентификация `OAuth` ](DRF/drf_3.md) 
      - [ Документирование `Swagger` ](DRF/drf_4.md) 
      

2.  [ Django Rest Framework SSO ](DRF-SSO/drf-sso_1.md)
      - Что такое `JWT` токены
      - Что такое `DRF-SSO`

---
Тестирование
---
 
1. **Unit Test**
   - [ Часть №1 ](Django/UnitTest_1.md)

---
Разное
---

 - [ Гео-кодирование, Модуль `GeoDjango` ](GeoDjango/geo_django_1.md)
 - [ Отправка почты Email `smtplib` ](other/email.md)
 - [ Установка HTML редактора `ckeditor` ](other/ckeditor.md)
 - [ Настройка сервера `PyCharm` для Дебага ](other/debug_server.md)
 - [ Трансляция, перевод на другие языки. ](other/translation.md)




[//]: # (4. **Cookiecutter**)
[//]: # (      - [ Шаблоны Django проектов ]&#40;Django/cookiecutter_1.md&#41;)

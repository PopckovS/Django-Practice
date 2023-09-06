Backend разработка.
---
Это сборник различной полезной информации и решения практических
задача по `Backend` разработке, от `Devops` до создания `Django` приложений
и `REST API`, и разное другое.

---
Теория
---

1. **[ Архитектура ]( Theory/1.md )**
    - Монолитная и Микро сервисная архитектура


2. **[ Паттерны ]( Theory/2.md )**
    - `Singleton`


---
Администрирование `Linux` `Devops`
---

**[ Сборник горячих команд ](Devops/1.md)**
 

1. **[ Работа с Linux ](Devops/2.md)**


2. **[ Работа с сетью в Linux ](Devops/3.md)**  
   - Сеть `iptables` и утилита `ufw`


2. **[ Механизм CI / CD ](Devops/Lesson_CI_CD.md)**

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
**[ Удобный сборник команд `git` ](git/commands.md)**

1. **[ Часть №1  `GIT` ](git/git_1.md)**
    - Настройка `git config`
    - `git rebase`
    - Конфликты
    - Тэги `git tag`
    - Система ведения репозиториев `Git-flow`


2. **[ Часть №2 `GIT` ](git/git_2.md)**
    - Хранилище `git stash`


3. **[Часть №3 `GIT` ](git/git_3.md)**
    - Работа с `GIT` через `PyCharm`

---
Работа с `GraphQL` и `Hasura`
---

1. **[ Часть №1 :](hasura/GraphQl_1.md)**
    - Что такое `GraphQL`, `Hasura` в докере
    - Типы запросов `query`, `mutation`, `subscription`
    - Отношения `object` и `array`

---
Web
---

1. **[ Часть №1 :](Web/web_1.md)**
    - `OSI`, `HTTP`, `HTTPS`

---
Настройка Django
---

1. **[ Установка и начало ](Start/start_1.md)**
    - Установка `Django`. Создание и подключение приложений


2. **[ Часть №1. Безопасность настроек. Переменные окружения ](Start/venv_1.md)**
   - Переменные окружения и оболочки в `Linux` 
   - Хранение настроек в переменных окружения и модуль `os.environ`
   

3. **[ Часть №2. Безопасность настроек, модуль `django-environ` ](Start/venv_2.md)**
   - Модуль `django-environ` его методы и примеры использования.


4. **[ Настройки проекта `Django`. Файл `settings.py`](Start/settings.md)**


---
Основное Django
---
**[Сборник различных команд Django](Django/django_commands.md)**


1. **[ Request / Response ](Django/request_1.md)**
    

2. **[ Django Utils ](Django/django_utils_1.md)**
   

3. **[ Middleware ](Django/mid_1.md)**
    - Промежуточное программное обеспечение.   

---
Модели, Запросы, Миграции
---
1. **Модели**
      - **[ Установка. Создание Моделей ](Models/connect.md)**
        - Настройка подключения к `Postgres` и расширение `PostGis`
        - Создание моделей

      - **[ №1 Модели ](Models/Models_1.md)**
        - Поля моделей и их опции
        - Поля для связей и поля для `GEO`
        - Ссылка модели на саму себя

      - **[ №2 Модели ](Models/Models_4.md)**
        - Настройки `MEDIA_ROOT`, `MEDIA_URl` для загрузки файлов
        - Класс модели `Meta` моделей
        - Транзакции `transaction.atomic`

      - **[ №3 Валидация полей модели ](Models/Models_5.md)**
        - Валидация полей модели в виде функции `ValidationError`
        - Валидация файлов на размер и формат
        - `RegexValidator`
        
      - **[ №4 Модели, Инспектор БД, Схемы, Множество БД. ](Models/Models_6.md)**
        - Автоматическая генерация моделей, инспектор баз данных `inspectdb`
        - Работа с разными схемами, множественное подключение к разным БД
        
      - **[ №5 Роутинг БД ](Models/Models_7.md)**
        - Маршрутизаторы Баз Данных

      - **[ №6 Разное ](Models/Models_8.md)**
        - Сигналы наа миграции


2. **ORM самого Django**

      - **[ №1 ORM ](Models/orm_0.md)**
        - Что такое `QuerySet` и его методы
        
      - **[ №2 ORM ](Models/orm_1.md)**
        - Методы: 
        - `save`, `create`, `all`, `get`, `filter`, `exclude`, `update`, `bulk_create`
        - Ограничения `LIMIT` и `OFFSET`
        - Приставки для фильтрации
        - Примеры запросов

      - **[ №3 ORM ](Models/orm_2.md)**
        - Выполнение прямых `SQL` запросов к БД
        - Агрегации `Count, Sum, Avg, Max, Min`
        - `Q` Обьект
        - Примеры запросов `ORM` :
          - Получение записей из среза по времени за множество дат 
          

3. **Миграции**
      - **[ №1 Миграции ](Models/migrate_1.md)**
        - Создание, применение и откат миграций, фейковые миграции.
        - Просмотр кода `SQL` что будет сделан миграцией
        - Чистый `SQL` в миграциях `migrations.RunSQL`
        - Миграции и обратные миграции `reverse_sql`

      - **[ №2 Миграции ](Models/migrate_2.md)** 
        - Ошибки при использовании миграций
        
      - **[ Фикстуры ](Models/fixtures.md)**
        - Выгрузка данных из БД `dumpdata`
        - Загрузка данных в БД `loaddata`
        - Фикстуры из системных моделей
        
---
Административная панель
---

1. **[ Часть №1 ](admin/admin_1.md)** 
   - Регистрация моделей в админ панели
   - Управление видом моделей в админке 

     
2. **[ Часть №2 ](admin/admin_2.md)** 
    - Методы жизненного цикла админки:
    - `save_model`, `delete_model`, `delete_queryset`, `get_ordering`,
   `get_paginator`, `get_search_results`, `get_urls`
    
        
3. **[ Часть №4 ](admin/admin_4.md)**
    - Действия администратора
    - Как делать редирект на страницу админки
    - Отключение экранирования `HTML` в админке
    - Кастомные атрибуты административной панели


4. **[ Часть №5 ](admin/admin_5.md)**
    - Управление порядком приложений и моделей в админке
    - Управление административной панелью
    - `admin.site.index_template` ???
    
---
Формы
---

1. **[ №1 Формы ](Form/form_1.md)** 
     - Класс форм и типы полей формы
     

2. **[ №2 Формы ](Form/form_2.md)**
     - Токен `csrf` 
     

3. **[ №3 Валидация Формы ](Form/form_3.md)**
    - Создаем собственную валидацию формы

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

1. **[ `Django` + `Celery` + периодические задачи ](Celery/Celery_1.md)**
    - Установка, Настройка, периодические задачи
    

2. [ `Celery` настройка ](Celery/Celery_2.md)


3. [ `Django` + `Celery` + `Flower` менеджер асинхронных задач ](Celery/Celery_3.md)
    - Что такое `Flower`


4. **[ Django_rq ](Celery/django_rq_1.md)**
   - Что такое `Django_rq` 
   - Функции коллбэки `on_success`, `on_failure`
   - Как отлаживать асинхронный код в `django-rq`


[//]: # (1. **Django + Celery**)

[//]: # (   - **[ Часть №1 ]&#40;Celery/celery_1.md&#41;&#40;Установка&#41;**)

[//]: # (   - **[ Часть №2 ]&#40;Celery/celery_2.md&#41;&#40;Начало работы&#41;**)

[//]: # (   - **[ Часть №3 ]&#40;Celery/celery_3.md&#41;&#40;`Flower` логер сообщений для `Celery`&#41;**)


---
Django Rest Framework `DRF`, `SSO`
---

1. Django Rest Framework `DRF`
      - **[ Основы `DRF` ](DRF/drf_1.md)**
      - **[ API Фильтрация, Поиск, Сортировка ](DRF/drf_2.md)**
      - **[ Аутентификация `OAuth` ](DRF/drf_3.md)**
      - **[ Документирование `Swagger` ](DRF/drf_4.md)**
      - **[ Права доступа `Permissions` ](DRF/permissions_5.md)**
      

2.  **[ Django Rest Framework SSO ](DRF-SSO/drf-sso_1.md)**
      - Что такое `JWT` токены
      - Что такое `DRF-SSO`
      
---
Разное `Django`
---
- **[ Флеш сообщения `messages` ](admin/admin_3.md)**
- **[ Гео-кодирование, Модуль `GeoDjango` ](GeoDjango/geo_django_1.md)**
- **[ Отправка почты Email `smtplib` ](other/email.md)**
- **[ Установка HTML редактора `ckeditor` ](other/ckeditor.md)**
- **[ Настройка сервера `PyCharm` для Дебага ](other/debug_server.md)**
- **[ Трансляция, перевод на другие языки. ](other/translation.md)**
- **[ Удобная работа с временными файлами `tempfile` ](other/tempfile.md)**
- **[ Удобная работа архивами `zip` ](other/zip.md)**
- **[ Работа с файлами ](other/file.md)**

---
Тестирование
---
 
1. **Unit Test**
   - **[ Часть №1 ](Django/UnitTest_1.md)**


[//]: # (4. **Cookiecutter**)
[//]: # (      - [ Шаблоны Django проектов ]&#40;Django/cookiecutter_1.md&#41;)




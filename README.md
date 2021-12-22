# Django-Practice

[comment]: <> (   - [ Часть №10 ]&#40;admin/admin_10.md&#41;)

[comment]: <> (     - Действия администратора `actions`)

[comment]: <> (     - Метод `get_urls`)

[comment]: <> (     - Пользовательские мтоды в `list_display`)

[comment]: <> (     - Как отключить экранирование `HTML`)

[comment]: <> (     - Редирект на страницу админки)


[//]: # (4. **Cookiecutter**)
[//]: # (      - [ Шаблоны Django проектов ]&#40;Django/cookiecutter_1.md&#41;)

---
Общие темы
---

1. **Разное**
 - [Сборник различных команд Django](Django/django_commands.md)
 - [Урок №2 ](Django/django_2.md) `GET`, `POST`, обработка исключений, `404`
 - [Урок №5 ](Django/django_5.md) `Templates`, `Views`, `Jinja2`, Фильтры шаблонов
 - [Урок №6 ](Django/django_6.md) Подключение статических файлов
 - [Урок №8 ](Django/django_8.md) Связи между моделями, Form

3. **Django Utils**
   - [ Часть №1 ](Django/django_utils_1.md)
   
4. **Middleware**    
   - [ Часть №1 ](Django/mid_1.md)
   
5. **Request / Response**
   - [ Часть №1 ](Django/request_1.md)

---
Администрирование `Linux` `Devops`
---

1. [ Сборник всех команд ](Devops/1.md)
 

2. [ Команды Linux ](Devops/2.md)  
   -  Сеть `iptables` и `ufw`

---
СУБД `PostgresQL`
---

1. [ Часть №1 ](Postgres/postgres_1.md)
    - Утилита `psql`
    - Управление пользователем в `Postgres` 
    - Создание БД


---
Начало работы
---

1. [ Установка и создание ](Start/start_1.md)
    - Установка `Django`
    - Создание приложения
    - Подключение приложения
 
   
2. [ Запуск сервера ](Start/server_1.md)
    - Убийство процесса на определенном порту
    - Запуск сайта на сервере самого `Django`
    - Запуск сайта на сервере `Gunicorn`
    - Запуск сайта на сервере `Nginx`


3. [Часть №1. Безопасность настроек. Переменные окружения](Start/venv_1.md)
   - Переменные окружения и оболочки в Linux 
   - Хранение настроек в переменных окружения
   - Переменные окружения и модуль `os.environ`
   

4. [Часть №2. Безопасность настроек, модуль `django-environ`](Start/venv_2.md)
   - Модуль `django-environ`
   - Примеры использования модуля `django-environ`
   - Методы модуля `django-environ`

---
Модели
---

1. **Модели**
      - [ Установка и соединение с БД ](models/django_sql_1.md)
        - Установка 
        - Соединение к `Postgres` и расширение `PostGis`
        
      - [ Модели ](models/django_models.md)
      - [ Класс Meta для моделей ](Django//django_sql_2.md)
      - [ Модели и ORM ](models/django_sql_3.md)
      - [ Поиск, приставки для фильтрации ORM ](models/django_sql_4.md) ( objects.filter )
      - [ QuerySets ](models/django_sql_5.md)
      - [ Сигналы Моделей ](models/signals_1.md)
      - [ Транзакции ](models/transactions_1.md)


2. **ORM**

      - [ №1 QuerySet ](models/qs_1.md)
        - Что такое `QuerySet`
        - Методы `QuerySet`
        
      - [ №1 ORM ](models/orm_1.md)
        - Методы: 
        - `save`, `create`, `all`, `get`, `filter`, `exclude`, `update`
        - Ограничения `LIMIT` и `OFFSET`
        - Примеры запросов

      - [ №2 ORM ](models/orm_2.md)
        - Выполнение прямых SQL запросов к БД
        - Агрегации `Count, Sum, Avg, Max, Min` 

      - [ №3 Примеры `ORM` запросов к БД ](models/orm_3.md)
        - Получение записей из среза по времени за множество дат


3. **Миграции**
      - [ №1 Миграции ](models/migrate_1.md)
        - Создание и применение миграций
        - Фейковые миграций
        - Как откатить миграцию
        - Просмотр кода `SQL` что будет сделан миграцией
        
      - [ №2 Миграции ](models/migrate_2.md) 
        - Чистый `SQL` в миграциях `migrations.RunSQL`
        
      - [ Фикстуры ](models/fixtures.md)
        - Выгрузка данных из БД `dumpdata`
        - Загрузка данных в БД `loaddata`

---
Админка
---

1. **Admin**
   - [ Часть №1 ](admin/admin_1.md) 
     - Регистрация моделей в админ панели
     - Атрибуты : 
       - `list_display`, `list_display_links`, `search_fields`, `list_filter` 
       - `list_select_related`, `date_hierarchy`, `actions_on_top`
       - `actions_on_bottom`, `exclude`, `ordering`, `paginator` 
     
   - [ Часть №2 ](admin/admin_2.md) 
     - Методы класса админки:
       - `save_model`, `get_ordering`, `delete_model`, `get_urls` 
       - `get_paginator`, `get_search_results`, `delete_queryset`
     - Методы шаблонов : 
       - `changelist_view`
       
   - [ Часть №3 ](admin/admin_3.md)
     - Флеш сообщения `messages`
     - Логирование `logging`
        
   - [ Часть №4 ](admin/admin_4.md)
     - Действия администратора
     - Как делать редирект на страницу админки
     - Отключение экранирования `HTML` в админке

   - [ Часть №5 ](admin/admin_5.md)
     - Управление порядком приложений и моделей в админке
     - `admin.site.index_template` ???

---
Формы
---

1. **Формы отправки `POST` запросов**
   - [ Формы №1 ](Form/form_1.md) 
     - Класс форм 
     - Классы для описания полей формы
     - `CharField`
     
   - [ Формы №2 ](Form/form_2.md)
     - Токен `csrf` 
     

---
Валидаторы
---

   - [ Часть №1 ](Validator/validator_1.md)
     - Валидаторы в виде функции `ValidationError`
     - Валидация файлов на размер и формат
     - Валидаторы :
     - `RegexValidator`


---
Регистрация и Аутентификация в `Django`
---

   - [ Введение ](Auth/auth_1.md)
     - Пользователи их создание и смена паролей 
     - Создание суперпользователей
     - `login` `logout` `authenticate` - ?
     

   - [ Дефолтная регистрация и авторизация в Django ](Auth/auth_2.md)
     - Регистрация пользователей через форму `UserCreationForm`
     - Авторизация пользователей через форму `UserCreationForm`


   - [ Модуль OAuth ](Auth/auth_3.md)


---
Представления `html`, `css`, `js` 
---

1. **Статические файлы и переопределения шаблонов**
   - [ Часть №1 ](view/view_1.md)
     - Статические файлы

---
Асинхронность
---

1. **Django + Celery**
   - [ Часть №1 ](async/celery_1.md) (Установка)
   - [ Часть №2 ](async/celery_2.md) (Начало работы)
   - [ Часть №3 ](async/celery_3.md) (Flower логер сообщений для Celery)


2. [ Django_rq ](async/django_rq_1.md)
   - Что такое `Django_rq` 
   - Функции коллбэки `on_success`, `on_failure`
   - Как отлаживать асинхронный код в `django-rq`

---
Работа с `GraphQL` и `Hasura`
---

1. [ Часть №1 :](hasura/GraphQl_1.md)
    - Что такое `GraphQL` и `Hasura`
    - Запуск `Hasura` локально в докере 

---
Django Rest Framework `DRF`, `SSO`
---

1. [Django Rest Framework `DRF` :](DRF)
      - [ Основы `DRF` ](DRF/drf_1.md) 
      - [ API Фильтрация, Поиск, Сортировка ](DRF/drf_2.md)
      - [ Аутентификация `OAuth` ](DRF/drf_3.md) 
      - [ Документирование `Swagger` ](DRF/drf_4.md) 
      

2.  [ Django Rest Framework SSO ](DRF-SSO/drf-sso_1.md)
      - Что такое `JWT` токены
      - Что такое `DRF-SSO`

---
Разное
---

 - [ Гео-кодирование, Модуль `GeoDjango` ](GeoDjango/geo_django_1.md)
 - [ Отправка почты Email `smtplib` ](other/email.md)
 - [ Установка HTML редактора `ckeditor` ](other/ckeditor.md)
 - [ Настройка сервера `PyCharm` для Дебага ](other/debug_server.md)


---
Тестирование
---
 
1. **Unit Test**
   - [ Часть №1 ](Django/UnitTest_1.md)

# Django-Practice

1. **Общие темы**
 - [Сборник различных команд Django](Django/django_commands.md)
 - [Урок №1 ](Django/django_1.md) ( Установка, URL )
 - [Урок №2 ](Django/django_2.md) ( GET, POST, обработка исключений, 404 )
 - [Урок №5 ](Django/django_5.md) ( Templates, Views, Jinja2, Фильтры шаблонов )
 - [Урок №6 ](Django/django_6.md) ( Подключение статических файлов )
 - [Урок №8 ](Django/django_8.md) ( Связи между моделями, Form)

2. **Безопасность настроек, модуль django-environ**
   - [ Переменные окружения и оболочки в Linux ](Django/environment_1.md)
   - [ Хранение данных в виртуальном окружении ](Django/venv_1.md)
   

3. **Django Utils**
   - [ Часть №1 ](Django/django_utils_1.md)

   
4. **Middleware**    
   - [ Часть №1 ](Django/mid_1.md)
   

5. **Request / Response**
   - [ Часть №1 ](Django/request_1.md)


---
Модели
---

1. **Модели**
      - [Взаимодействие с Моделями, БД ](models/django_sql_1.md)
        - Установка 
        - Соединение к `Postgres`
        - `Postgis`      

      - [Модели](models/django_models.md)
      - [Класс Meta для моделей](Django//django_sql_2.md)
      - [Модели и ORM](models/django_sql_3.md)
      - [Поиск, приставки для фильтрации ORM](models/django_sql_4.md) ( objects.filter )
      - [QuerySets](models/django_sql_5.md)


2. **Сигналы Моделей**
      - [ Часть №1 ](models/signals_1.md)


3. **QuerySet ORM**
      - [ Часть №1 ](models/orm_1.md)
        - Методы QuerySet
      - [ Часть №2 ](models/orm_2.md)
        - Агрегации `Count, Sum, Avg, Max, Min` 


4. **Миграции**
      - [ Миграции ](models/migrate_1.md)
        - Создание миграций
        - Запуск миграций
        - Фейковые миграций
        - Откат миграций
        - Просмотр кода SQL что будет сделан миграцией
      - [ Чистый SQL в миграциях  migrations.RunSQL ](models/migrate_2.md) 


5. **Транзакции** 
   - [ Транзакции ](models/transactions_1.md)


6. **Фикстуры**
   - [ Экспорт данных из БД `dumpdata` ](models/dumpdata_1.md)
   - [ Импорт данных из БД `loaddata` ](models/loaddata_1.md)


---
Админка
---

1. **Admin**
   - [ Часть №1 ](admin/admin_1.md) 
     - Регистрация моделей в админ панели
     - Атрибуты : 
       - `list_display` 
       - `list_display_links`
       - `search_fields` 
       - `list_filter`
       - `list_select_related`
       - `date_hierarchy`
       - `actions_on_top`
       - `actions_on_bottom`
       - `exclude`
       - `ordering`
       - `paginator` 
     
   - [ Часть №2 ](admin/admin_2.md) 
     - Методы класса админки:
       - `save_model`
       - `get_ordering`
       - `delete_model`
       - `delete_queryset`
       - `get_paginator`
       - `get_search_results`
       - `get_urls`
     - Методы шаблонов : 
       - `changelist_view`
       
   - [ Часть №3 ](admin/admin_3.md)
     - Флеш сообщения
     - Логирование
     
   - [ Часть №4 ](admin/admin_4.md)
     - Действия администратора


6. **Аутентификация и Авторизация**
   - [ Часть №1 ](Auth/auth_1.md)
     - Пользователи их создание и смена паролей 
     - разрешения и группы ??????
     - `login` `logout` `authenticate` - ??? 
     



[comment]: <> (   - [ Часть №10 ]&#40;admin/admin_10.md&#41;)

[comment]: <> (     - Действия администратора `actions`)

[comment]: <> (     - Метод `get_urls`)

[comment]: <> (     - Пользовательские мтоды в `list_display`)

[comment]: <> (     - Как отключить экранирование `HTML`)

[comment]: <> (     - Редирект на страницу админки)

---
Асинхронность
---

1. **Django + Celery**
   - [ Часть №1 ](async/celery_1.md) (Установка)
   - [ Часть №2 ](async/celery_2.md) (Начало работы)
   - [ Часть №3 ](async/celery_3.md) (Flower логер сообщений для Celery)


2. **Django_rq**
   - [ Часть №1 ](async/django_rq_1.md)

---
Тестирование
---
 
1. **Unit Test**
   - [ Часть №1 ](Django/UnitTest_1.md)


---
Сторонние Библиотеки
---

1. **Django Rest Framework (DRF) :**
      - [ Часть №1 ](DRF/drf_1.md)
      - [ Часть №2 ](DRF/drf_2.md) (API Фильтрация, Поиск, Сортировка)
      - [ Часть №3 ](DRF/drf_3.md) (Аутентификация OAuth)
      - [ Часть №4 ](DRF/drf_4.md) (Swagger)
      

2.  **DRF Unit Test**
      - [ Часть №1 ](DRF/DRFUnitTest_1.md)


3. **GeoDjango**
      - [ Часть №1 ](Django/geo_django_1.md)

   
4. **Cookiecutter**
      - [ Шаблоны Django проектов ](Django/cookiecutter_1.md)
   

---
Разное
---

 - [Отправка почты Email `smtplib`](other/email.md)
 - [Установка HTML редактора `ckeditor`](other/ckeditor.md)
 - [Настройка сервера `PyCharm` для Дебага](other/debug_server.md)

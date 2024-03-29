Установка и начало работы
---
---
Создание проекта
---
Создали виртуальное окружение, далее работаем с вирт-окружением, 
устанавливаем сам Django последней версии(будет взята последняя из 
официального репозитория):

     pip3 install django

Установка версии Django конкретной версии, если требуется установка
конкретной версии:

    pip3 install django==3.2.*

Узнать версию Django:

    python3 -m django --version

Посмотреть все доступные команды можно:

    django-admin

    # Вывод
    Available subcommands:
    [django]
        check               compilemessages
        createcachetable    dbshell
        diffsettings        dumpdata
        flush               inspectdb
        loaddata            makemessages
        makemigrations      migrate
        runserver           sendtestemail
        shell               showmigrations
        sqlflush            sqlmigrate
        sqlsequencereset    squashmigrations
        startapp            startproject
        test                testserver

Создаем новый проект django такой командой 
`django-admin startproject Название_сайта`:

    django-admin startproject coolsite

После этого будет создана новая директория `coolsite` в которой будет  
еще одна поддиректория `coolsite` и файл `manage.py`

После создания, получим следующую структуру приложения:

    coolsite/
        manage.py
        coolsite/
            __init__.py
            settings.py
            urls.py
            asgi.py
            wsgi.py

Разберем проект по файлам:

`coolsite/` - пакет с конфигурациями проекта.

`manage.py` - это точка запуска команд для работы с сайтом, это файл 
который будет выполнять работу от `django-admin`, по сути это просто 
эквивалент `bin/console` из фреймворка Symfony.

`__init__.py` - Пустой файл что указывает что данная директория, должна 
рассматриваться как пакет питона.

`settings.py` - файл конфигов, тут хранятся настройки для работы всего приложения.

`urls.py` - тут собраны `URL` пути для сайта, и методы представлений то будут на них
отрабатывать.

`asgi.py` - `ASGI` запуск собственного сервера для проекта.

`wsgi.py` - `WSGI` точка входа для совместных веб серверов, 

---
Создание приложения
---
Проект - сам сайт со множеством приложений и БД.

Приложение - отдельный модуль который имеет свою внутреннюю сеть `URL` адресов,
свои модели, миграции, виды, тесты. Каждое приложение в проекте
`Django` должно следовать определенному соглашению.

Суть философии `Django` в создании отдельно функционирующих приложений,
каждое из которых можно переиспользовать в других проектах.

Для создания нового приложения, переходим в корень сайта и создаем 
новое приложение с названием `women` это создаст новое приложение с 
одноименным названием:

    python3 manage.py startapp women

При создании нового приложения, мы получим след структуру:

    women /

        migrations /    # Хранение миграций Бд только для этого приложения
            __init__.py
        __init__.py
        admin.py        # Для создания админ панели этого приложения
        apps.py         # Хранит настройки приложения
        models.py       # Для хранения моделей
        tests.py        # Модуль для тестирвоания
        views.py        # Для хранения представлений приложения

Суть работы с приложениями в `Django` заключается в их модульности, каждое 
из приложений можно разрабатывать отдельно, и подключать в любой проект,
у каждого приложения имеется свои внутренние `URL` адреса, которые определяются
внутри каждого приложения отдельно, а при подключении приложения в проект, 
всем внутренним `URL` адресам приложения дается один корневой внешний `URL` который
определяется в ядре проекта, таким образом мы можем иметь древовидную структуру
проекта, где каждому приложения соответствует его ветка `URL` адресов, и можно 
одной записью в главном конфиге `URL` адресов менять весь корень пути приложения.

После создания приложения, требуется зарегистрировать его в самом проекте,
для этого в файле настроек `collsite/settings.py` где хранятся подключения
стандартных приложений, дополним подключением нашего приложения:

    INSTALLED_APPS = 
    [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    
        # Наши приложения
        'women.apps.WomenConfig'
    ]

Где строка `women.apps.WomenConfig` будет указывать на обращение к 
приложению `women` его файлу `apps.py` и классу что в нем находится 
`WomenConfig` этот класс ответственен за настройки нашего приложения.

Тут же помимо нашего подключенного приложения `women.apps.WomenConfig`
есть и другие, уже по дефолту установленные приложения, они нужны для
функционирования самой системы:

- `django.contrib.admin` - админка для `Django`.
- `django.contrib.staticfiles` - приложение для работы со статическими 
файлами, под статическими в `Django` считаются все `css`, `js`, `img` файлы.
- `django.contrib.contenttypes` - фреймворк типов данных.
- `django.contrib.sessions` – приложение для работы с сессиями.
- `django.contrib.messages` – приложение для работы с сообщениями.


---
Запуск сайта на тестовом сервере самого `Django`
---

Для запуска встроенного отладочного сервера самого `Django`, переходим в 
проект и запускаем сервер такой командой:

    python3 manage.py runserver

Сайт запустится на стандартном для него лок хосте, на дефолтном порту
`http://127.0.0.1:8000/` выйти и остановить сервер можно стандартной командой
`CONTROL-C`.

Сервер по дефолту запускается именно по схеме `http` ибо в процессе отладки
у нас просто нет необходимости шифровать трафик `SSL` ибо тестовый сервер только 
для отладки, в производственной среде обязательно делайте переадресацию всех 
запросов со схемы `http` на его шифрованную версию `https`.

Что бы автоматически перенаправлять со схемы `http` на `https`, установите
следующую переменную окружения:
```dotenv
SECURE_SSL_REDIRECT = True
```

Следует помнить что запуская сервер на хосте `127.0.0.1` он будет доступен
только для самого себя, для того что бы иметь возможность обращаться к хосту
с других адресов в сети, запускайте сервер на адресе `0.0.0.0`

Для изменения порта следует точно указывать порт в ручную:

    python3 manage.py runserver 4000

По дефолту проект работает с `SQLite` и при первом запуске, будет создан
файл `db.sqlite3` для работы с БД.

Также при запуске получим предупреждение о том что миграции не применены,
и предложение выполнить эти миграции для работы приложения.

    You have 18 unapplied migration(s). 
    Your project may not work properly until you apply the migrations
    for app(s): admin, auth, contenttypes, sessions.

    Run 'python manage.py migrate' to apply them.

---
Убийство процесса на определенном порту
---

Команда для убийства процесса на определенном порту, в данном случае на
порту `8000` такое иногда случается, что сервер зависает и приходится убивать 
процесс.

```
sudo kill -9 `sudo lsof -t -i:8000`
```

---
Создание первого представления для приложения и URL для него 
---
В самом приложении есть файл `views.py` в котором и будем создавать 
виды для приложения, создадим в нем функцию `index` которая принимает
параметр в виде спец.объекта класса `HttpRequest` который содержит 
информацию о запросе, и возвращаем спец объект класса `HttpResponse`
с ответом на запрос.

```python
    from django.http import HttpResponse
    from django.shortcuts import render
    
    # Отображение главной страницы
    def index(request):
        return HttpResponse("Страница приложения women")
```
Так мы создали наше первое представление, теперь для его работы, нужно 
связать его с `url` адресом.

В файле `coolsite/urls.py` добавим наш путь, в список `urlpatterns`
добавляем `path('women/', index)` где `women/` есть `URL` к которому 
привязан вид, а `index` есть указание на функцию которая отображает 
главный вид, но для работы всего этого требуется импортировать эту 
функцию `from women.views import index`

```python
# Импорт видов от приложения women
from women.views import index

urlpatterns = [
    # Админ панель сайта
    path('admin/', admin.site.urls),

    # Подключаем приложение women
    path('women/', index)
]
```

Данный подход к созданию `URL` возможен, но нарушает инкапсуляцию приложения,
используем другой подход, суть его в том чтобы все `URL` пути приложения 
были описаны внутри самого приложения, а внешне в сам проект, все эти
`URL` пути будут подключены одной строчкой, которая будет родительским
путем для всех дочерних `URL` приложения. 

Что делаем, все `url` для приложения `women` переносим из `collsite/urls.py`  
во внутренний для приложения `women/urls.py`

`collsite/urls.py` будет содержать корень для всех `url` путей приложения:

```python
urlpatterns = [
    # Админ панель сайта
    path('admin/', admin.site.urls),

    # Подключаем URL для приложения women
    path('women/', include('women.urls'))
]
```

`path('women/', include('women.urls'))` Здесь первый параметр становится
корневым `URL` приложения, а при помощи функции `include('women.urls')` 
мы подключаем пути приложения, указывая модуль и файл с путями.

Мы всегда должны использовать `include` для подключения приложения, где
`admin.site.urls` является единственным исключением, он дефолтно указывает
на админку самого`Django`.

`women/urls.py` будет содержать все внутренние `url` пути для приложения: 

```python
# Импорт видов от приложения women
from women.views import index, categories

# URL внутри приложения women
urlpatterns = [
    path('', index),
    path('categories', categories),
]
```

Как это работает, берем корень пути `women/` и соединяется с каждым
внутреннем путем, в результате получаем след пути:

    women/    # Отработает вид index
    women/categories  #Отработает вид categories

---

Паттерны в URL
---
Помимо стандартных `url` путей, можно делать их динамичными, указывая 
паттерны, которые будут передаваться в качестве параметров, 
которые можно использовать для получения данных из БД:

`URL` пути:
```python
urlpatterns = [
    path('', index),
    path('categories/<int:catID>', categories),
]
```

В виде он приходит в качестве параметра:
```python
def categories(request, catID: int):
    return HttpResponse(f"<h1>Статья категории {catID}</h1>")
```

Использовать в качестве паттерна можно следующие типы:

    str  - строка, исключая символ /
    int  - числа включая 0
    slug - все спец символы, и числа и строки "4532dfg" но только латиница
    uuid - цифры включая дефис
    path - любая строка включая /

Помимо `path()` можно использовать` re_path()` который задает регулярное 
выражение, по которому будет связан `URL` с видом.

К примеру такое регулярное выражение ищет 4 цифры после слова `archive/`
и эти 4 цифры будут переданы в вид в качестве переменной с названием `year`

```python
# URL паттерн
re_path(r'^archive/(?P<year>[0-9]{4})', archive),

# Вид что его отображает
def archive(request, year):
    return HttpResponse(f"<h1>Архив по годам {year}</h1>")
```

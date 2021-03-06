Статические файлы стилей для админ-панели
---

Для наших приложений мы используем стили и `js` код который определяем сами,
но для админки используются предопределенные стили `css` и `js` код, этот код 
по дефолту предопределен в самом `Django` 

В `Django` по пути `django/contrib/admin/` определены различные файлы для 
работы админки по дефолту, тут и миграции и шаблоны и виды и стили для
админки, принято копировать стили из админки с `css`, `js` и прочим кодом
из самого `Django` что установлен на компьютере в библиотеках, локально
на сам сайт.

То есть хранить предопределенные для админки стили, локально вместе со
стилями на самом сайте, для копирования всех этих файлов на проект, есть 
специальная команда:

```
python3 manage.py collectstatic

# Вывод
128 static files copied to '/var/www/Django-Lessons/coolsite/static'.
```

В файле настроек проекта `coolsite/settings.py` определены 2 константы,
они отвечают за то куда будут скопированы статические файлы, как для 
админки, так и для пользователей.

`STATIC_ROOT` - указывает директорию относительно корня проекта, сюда будут
скопированы статические файлы из приложений при использовании команды сборки 
статический файлов `python3 manage.py collectstatic`

```python
STATIC_URL = '/static/'
STATIC_ROOT = 'static'
```

---
Представления и пути `url` к ним
---

Методы представлений(виды) отрабатывают соответствующий им URL адрес,
создадим 4 URL пути для работы 4 представлений, для опросов пользователей.

Наши URL адреса `/polls/urls.py` :

```python
urlpatterns = [
    # URL: /polls/ Домашняя страница
    path('', views.index, name='home'),

    # URL: /polls/<id:int>/ Показать вопрос по ID
    path('<int:question_id>', views.detail, name='detail'),

    # URL: /polls/<id:int>/results/ Показать результат опроса по ID
    path('<int:question_id>/results/', views.results, name='results'),

    # URL: /polls/<id:int>/vote/ Проголосовать по ID
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Наши виды `/polls/views.py` с методами что будут отрабатывать на URL : 

```python
def index(request):
    """
    Главный метод, показывает все вопросы.
    URL: /
    """
    return HttpResponse("Показывает все вопросы")


def detail(request, question_id: int):
    """
    Показать конкретный вопрос по его id
    URL: /<question_id>/detail
    """
    return HttpResponse(
        f"Показать конкретный вопрос по его id = {question_id}"
    )


def results(request, question_id: int):
    """
    Показать результат по конкретному вопросу по его id
    URL: /<question_id>/results
    """
    response = f"Показать результат по конкретному \n " \
               f"вопросу по его id = {question_id}"
    return HttpResponse(response)


def vote(request, question_id: int):
    """
    Голосование по вопросу по его id
    URL: /<question_id>/vote
    """
    return HttpResponse(f"Голосование по вопросу {question_id}")
```

Каждое из представлений выполняет одно из двух действий, возвращает
обьект типа HttpResponse или создает исключение Http404

В процессе работы можно выполнять все остальное, как в контроллере,
читать из Бд генерировать файлы и прочее...

---

Получение данных из `models` в `views`
---

Чтобы получать данные из моделей в видах, импортируем модели в виды 
и получим из них данные при помощи специальных методов:

```python
from .models import Question, Choice


def index(request):
    """
    Главный метод, показывает все вопросы.
    URL: /
    """

    # Получить все обьекты из таблицы Question
    questions = Question.objects.all()

    # Формируем строку для отображения всех вопросов, и 
    # в цикле заполняем ее, и отдаем пользователю.
    output = ''
    for question in questions:
        output += "Текст вопроса = {question_text} " \
                  "Дата публикации = {pub_date}" \
                  "<br>".format(
            question_text=question.question_text,
            pub_date=question.pub_date
        )

    return HttpResponse("Показывает все вопросы <hr> {output}".format(
        output=output
    ))
```

Удалить все записи из таблицы `Question`

```python
    # Удалить все обьекты(записи) из таблицы Question
    output = Question.objects.all().delete()
```

Таким образом мы можем получать записи из БД, и отдавать их
пользователю, но делать это на прямую прямо в методе вида не удобно,
для этого есть шаблоны.

---
Шаблоны `templates` и метод `render`
---

Для работы шаблонов внутри приложения, требуется создать папку `templates`
внутри этого приложения, то есть внутри каждого из приложений есть своя
директория `templates`.

Когда проект будет собран в финальную версию, все шаблоны будут помещены 
в единую директорию `templates`, и если среди них есть шаблоны с одинаковыми
названиями, то будет путаница, для того чтобы этого не произошло принято,
внутри каждого из них создать еще поддиректорию с названием этого 
приложения.

Для приложения `women` это `templates/women`, для `polls` это 
`templates/polls`.

В файле настроек `collsite/settings.py` проекта есть параметр
`INSTALLED_APPS` в котором описываются все приложения, что подключаются
к проекту как дефолтные, так и созданные нами. Также в настройках есть
параметр `TEMPLATES` который отвечает за поведение проекта в отношении
шаблонов, одним из параметров является `'APP_DIRS': True` если он 
установлен в `True` то поиск всех шаблонов для проектов что перечислены
в `INSTALLED_APPS` будет происходить в их поддиректориях `templates`

```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [],
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]
```

---

Как использовать шаблоны в представлениях. 

Для того чтобы использовать шаблоны в представлениях, нам потребуется специальный
метод `render` этот метод первым аргументом принимает первый параметр 
метода вида `request`, второй параметр это путь к шаблону `html`

В файле `women/views.py` использование шаблонов:
```python
from django.shortcuts import render

def index(request):
    """Главная страница"""
    return render(request, 'polls/index.html')
```

Таким образом мы указываем что для отработки метода `index` в 
приложении `polls` надо использовать шаблон по пути 
`templates/polls/index.html` внутри приложения `polls`
а полный путь от корня сайта будет `polls/templates/polls/index.html`

Для передачи переменных в шаблонизатор, метода `render` есть еще один 
параметр `context` - это словарь с переменными.

Импортируем модель `Question` и получим из нее все записи, все записи
из таблицы будут представлены в виде набора обьектов класса `Question`

```python
from .models import Question

def index(request):
    all_questions = Question.objects.all()
    context = {
        'var_title': 'Главная страница для приложения polls',
        'latest_question_list': all_questions,
    }
    return render(request, 'polls/index.html', context=context)
```

Получение всех записей из модели, возвращает особое множество типа
`QuerySet`

```python
all_questions = Question.objects.all()
```

---
Шаблонизатор `Jinja2`
---

`Jinja2` - шаблонизатор который используется в `Django`.

Когда все записи в виде множества обьектов, попадают в шаблон, мы
можем вывести их в шаблон при помощи синтаксиса шаблонизатора.

---
`Пример 1: Цикл for`

Если мы просто передаем в шаблон текст в виде не которого списка

```python
def index(request):
    """Главная страница опросов"""
    context = {
        'title': 'Women Главная страница',
        'menu': ['О сайте', 'Добавить статью', 'Обратная связь', 'Войти']
    }
    return render(request, 'women/index.html', context=context)
```
```html
<ul>
    {% for line in menu %}
        <li>
            {{ line }}
        </li>
    {% endfor %}
</ul>

О сайте
Добавить статью
Обратная связь
Войти
```

---
`Пример 2: Цикл for и условие if`

Передаем в шаблон набор обьектов полученных из модели, каждый из
обьектов имеет свои атрибуты, вывести его атрибуты можно указывая их 
через точку, так как если бы мы вызывали атрибуты обьекта прямо в 
`python3`

```python
def index(request):
    all_questions = Question.objects.all()
    context = {
        'var_title': 'Главная страница для приложения polls',
        'all_questions': all_questions,
    }
    return render(request, 'polls/index.html', context=context)
```

```html
    <h3>{{ var_title }}</h3>

    {% if all_questions %}
        <ul>
            {% for question in all_questions %}
                <li>
                    <a href="/polls/{{ question.id }}">
                        {{ question.question_text }}
                    </a>
                </li>
            {% endfor %}
        </ul>
    {% else %}
        <p>Вопросов нету</p>
    {% endif %}

Главная страница для приложения polls

Вопрос №1
Вопрос №2
Вопрос №3
Вопрос №4
Вопрос №5
```

---
Наследование шаблонов
---
Что бы не дублировать часть шаблонов, шаблоны можно наследовать, 
создадим еще один шаблон `base.html` блок с названием 
`{% block mainmenu %}` будет вмещать в себя меню, это меню будет 
учавствовать в нескольких разных видах, по этому мы ее и выносим в
отдельный блок.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>
<body>

    {% block mainmenu %}
        <ul>
            {% for line in menu %}
                <li>{{ line }}</li>
            {% endfor %}
        </ul>
    {% endblock mainmenu %}

    
    {% block content %}
        {# Тут будет внутренний контент #}
    {% endblock content %}

</body>
</html>
```

Другой блок `{% block content %}` будет содержать уже уникальные 
данные, которые будут свои для каждого из видов.

Создадим 2 других вида, что будут наследоваться от основного шаблона.

Шаблон `index.html`

```html
{% extends 'women/base.html' %}

{% block content %}
    <h1>{{ title }}</h1>
    <p>Содержимое главной страницы</p>
{% endblock content %}

```

Шаблон `about.html`

```html
{% extends 'women/base.html' %}

{% block content %}
    <h1>{{ title }}</h1>
    <p>Содержимое страницы о сайте</p>
{% endblock content %}
```

---
Фильтры в Шаблонах
---

[Все фильтры тут](https://djbook.ru/rel3.0/ref/templates/builtins.html#ref-templates-builtins-filters)

Фильтры это своего рода функции, которые можно применять на те
данные, которые мы выводим в шаблоне, все фильтры указываются 
через прямую черту `|` к примеру таким образом :

```html
     {{p.content|linebreaks|truncatewords:50}}
```

- `safe` - обычно все данные что мы выводим из БД в виде текста 
экранируются от html тегов, этот фильтр позволяет отключить 
экранирование, и выводить данные с тегами, что полезно когда в 
админке мы вводим данные при помощи html редактора.

```html
    {{ lesson.content | safe }}
```

- `linebreaks` - устанавливает разрывы между строками, для
красивого вывода, с красивым разбиением на абзацы.

- `truncatewords:50` - обрезать строчку на указанное количество 
слов.

Специальный блок `{% autoescape on %}` который тоже указывает 
экранировать теги в тексте или нет, у него есть 2 параметра 
`on|off`

```html
{% autoescape on %}
    {{p.content}}
{% endautoescape %}
```

---
Формирование `url` в шаблонах
---

Для этого используется специальный тег такого формата :

`{% url '<URL-алрес или имя маршрута>'[параметры ссылки] %}`

Мы можем указывать `url` путь хардкодно, а можем создавать
динамические пути.
```html
 <a href="/">
    <div class="logo"></div>
 </a>
```

Создание динамического пути, мы можем указать название url 
маршрута, о самое название которое мы указываем при определении 
url

```python
urlpatterns = [
    path('', index, name='home'),
]
```

И далее по нему обращаться в маршрутах

```html
<a href="{% url 'home' %}">
    <div class="logo"></div>
</a>
```

---

Если наш url маршрут принимает некоторый параметр типа id
записи в таблице, примеру такого типа `post/<int:post_id>/`

```python
urlpatterns = [
    path('post/<int:post_id>/', show_post, name='post')
]
```

То этот параметр мы можем указывать после названия url, 
следующим образом

```html
<a href="{% url 'post' p.pk %}">
    Читать пост
</a>

post/1
post/3
post/5
```

---

Также есть и другой метод создания таких динамических ссылок,
которые привязаны к отображению не котрой модели, для этого в
самой модели создается специальный метод с предопределенным
названием `get_absolute_url`

С начала импортируем метод `from django.urls import reverse` и
реализуем метод `get_absolute_url` где первый параметр это название
маршрута `url` второй, именованный параметр указывает те самые
дополнительные параметры, что передаются вместе с маршрутом 

```python
from django.db import models
from django.urls import reverse

class Model_1(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField(blank=True)
    is_published = models.BooleanField(default=True)

    def get_absolute_url(self):
        return reverse('post', kwargs={'post_id': self.pk})
        # return reverse('post', kwargs={'post_id': self.pk,'is_published': self.is_published})
```

Этот способ сформирует такой же url путь как и превыдущий, если
нам требуется указать еще один параметр, то указываем его так:

```python

    def get_absolute_url(self):
        return reverse(
                    'post', 
                    kwargs={
                        'post_id': self.pk, 
                        'is_published': self.is_published
                    }
        )
```

Это сформирует путь такого типа `post/5/True`  или `post/5/False`.

Когда мы указываем этот путь в шаблоне, мы можем использовать этот
самый метод прямо в шаблоне, только указывая его уже как атрибут,
к примеру так `{{ p.get_absolute_url }}`

Следующие 2 метода полностью одинаковые:

```html
<p class="link-read-post">
    <a href="{% url 'post' p.pk %}">Читать пост</a>
    <a href="{{ p.get_absolute_url }}">Читать пост</a>
</p>
```

---
Подключение статических файлов `css` и `Js`
---
При подключении статических файлов типа `css` и `js`, работа с сайтом
может идти в 2 режимах, что определяется в настройках `DEBUG` тестовый
и боевой режим.

Статические файлы находятся в директориях `static` внутри каждого из
приложений, то есть у каждого приложения есть своя директория `static`
со своими статическими файлами.

```
coolsite /
    static /
        Статичекий файлы для боевого
    women /
        migrations
        templates  
        admin.py  
        apps.py  
        __init__.py  
        models.py    
        tests.py  
        urls.py  
        views.py
        static  /
            Статические файлы для отладки
    polls /
        migrations
        templates  
        admin.py  
        apps.py  
        __init__.py  
        models.py    
        tests.py  
        urls.py  
        views.py
        static / 
            Статические файлы для отладки
```

В режиме отладки, используются `static` директории что находятся внутри 
приложений, в боевом режиме используется `static` что находится в корне 
сайта.

Создает папку `static` в корне сайта, где будет находиться все статические
файлы для боевой работы сайта, можно следующей командой.
```
python3 manage.py collectstatic
```
Эта команда проходится по все внутренним `static` директориям и собирает
все статические файлы.

Для работы этой команды, потребуется определить 3 константы в файле 
настроек:

```python
# URL по которому будут доступны статические файлы, подобно тому как
# мы определяли MEDIA_URL для просмотра загруженных файлов 
STATIC_URL = '/static/'

# Сама папка static в корне сайта, куда будут помещены собранные 
# файлы, можно встретить 2 варианта
STATIC_ROOT = BASE_DIR / 'static'
# STATIC_ROOT = os.path.join(BASE_DIR, 'static')

# по дефолту сборка происходит из static внутри приложений, тут можно
# определить дополнительные пути для сборки статических файлов
STATICFILES_DIRS = []
```
---
Помещать статические файлы следует по той же структуре, что и шаблоны

```
coolsite /
    women /
        migrations  
        admin.py  
        apps.py  
        __init__.py  
        models.py 
        urls.py  
        views.py
        static  /
            women /
                css /
                    file.css
                js /
                    file.js
                img /
                    file.png
```

Когда будет выполнена сборка проекта командой 
`python3 manage.py collectstatic` то все статический файлы будут
собраны в главный `static`

---

В шаблоны что будут подключать статические файлы, следует загрузить 
специальную предопределенную переменную шаблона при помощи такой
конструкции `{% load static %}`

```html
{% load static %}
<link type="text/css" href="{% static 'women/css/styles.css' %}" rel="stylesheet" />
<link rel="shortcut icon" href="{% static 'women/img/main.ico' %}" type="image/x-icon"/>
```

С помощью такого указания пути, и происходит определение местоположения
статических файлов, в зависимости от режима отладка / боевой, переменная
`static` будет указывать либо на локальный `static`, либо на собранный
глобальный `static`

```html
href="{% static 'women/css/styles.css' %}"
```

---
Обработка `GET` и `POST` в представлениях
---
В видах, первый параметр, что мы получаем в метод это `request` он
содержит в себе информацию о запросе, включая GET, POST.

```python
def categories(request, catID: int):
    if request.GET:
        print(request.GET)

    if request.POST:
        print(request.POST)

    return HttpResponse(f"<h1>Статья категории {catID}</h1>")

    # При запросе типа 
    # http://127.0.0.1:8000/categories/34?name=Gagarin&type=pop
    # В терминале увидим вывод информации:     
    # <QueryDict: {'name': ['Gagarin'], 'type': ['pop']}> 
```

---
Обработка исключений
---
В файле с настройками `coolsite/settings.py` есть переменная, что отвечает за
настройку отладки, при параметре `True` мы находимся в режиме отладки,
при котором страница `404` отображает дополнительную информацию для 
разработчика.

```python
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True
ALLOWED_HOSTS = []
```

Чтобы переключить сайт в боевой режим, и не выводить дополнительную 
информацию, и при обращении к несуществующей странице показывать стандартную 
`404` страницу, переопределим параметр отладки, и добавим хост к которому 
будет разрешено обращаться.

```python
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = False
ALLOWED_HOSTS = ['127.0.0.1']
```

---
Переопределение страницы ошибок
---
В главном файле настроек `coolsite/settings.py` можно переопределить системную 
переменную `handler404` присвоить ей функцию, которая будет отрабатывать 
при ошибке `404`

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('women.urls'))
]
handler404 = pageNotFound
```

Саму функцию для обработки ошибок можно поместить в виды любого приложения,
такая функция должна возвращать обьект спец класса `HttpResponseNotFound`
и принимать 2 параметра, с информацией запроса и возникшей ошибкой.

```python
from django.http import HttpResponse, HttpResponseNotFound

def pageNotFound(request, exception):
    return HttpResponseNotFound("<h1>Страница не найдена, ошибка 404</h1>")
```

В случае если требуется в любом другом виде, при неком условии отобразить 
страницу `404`, то сделать это можно при помощи спец метода `Http404()`
делается это путем поднятия ошибки `raise`

```python
from django.http import HttpResponse, HttpResponseNotFound, Http404

def archive(request, year):
    if int(year) > 2021:
        raise Http404()
    return HttpResponse(f"<h1>Архив по годам {year}</h1>")
```

Аналогичным образом можно переопределять страницы ошибок, и при других 
ситуациях:

    handler500 - Ошибка сервера
    handler403 - Доступ запрещен
    handler400 - Невозможно обработать запрос

Но все эти страницы ошибок будут срабатывать, если `DEBUG = False` иначе
мы всегда будем видить страницу с отладочной информацией.

---
Создание редиректов 301, 302
---

`301` - Ресурс перемещен на постоянной основе.

`302` - Ресурс перемещен на временной основе.

Импортируем специальную функцию `redirect()` которой указываем куда делать 
перенаправление, при определенных обстоятельствах, в дефолтном 
использовании `redirect()` пользует перенаправление с кодом `302`

```python
from django.shortcuts import render, redirect

def archive(request, year):
    if int(year) > 2021:
        return redirect('/')

    return HttpResponse(f"<h1>Архив по годам {year}</h1>")

    # Вывод в терминале
    # "GET /archive/2025 HTTP/1.1" 302 0
```

Если передать в функцию именной параметр, `permanent=True` то 
перенаправление будет постоянным.

```python
from django.shortcuts import render, redirect

def archive(request, year):
    if int(year) > 2021:
        return redirect('/', permanent=True)

    return HttpResponse(f"<h1>Архив по годам {year}</h1>")

    # Вывод в терминале
    # "GET /archive/2025 HTTP/1.1" 301 0
```

Когда мы явно указываем редирект на страницу, можно использовать не только
хардкорные пути, но и алиасы, которые хорошо использовать в случае если
путь может поменяться, а сам алиас останется

```python
return redirect('/', permanent=True)
```

Заменяем на `home`

```python
return redirect('home', permanent=True)
```

Задать такой алиас для определенного пути, можно в самих маршрутах
`women/urls.py`

```python
# URL внутри приложения women
urlpatterns = [
    path('', index, name='home'),

    path('categories/<int:catID>', categories),

    re_path(r'^archive/(?P<year>[0-9]{4})', archive),
]
```

Теперь обращаясь к этому пути по его алиасу `home` будет отрабатывать 
маршрут `women/`
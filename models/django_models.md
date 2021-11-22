Описание Моделей
---
---

Приставки в выборке данных из Модели
---

[Почитать про приставки тут](https://djangodoc.ru/3.1/ref/models/querysets/#field-lookups)

Приставки к полям, по которым ведется поиск записей в 
таблице, используются для уточнения запроса, у таких приставок
используется `__` двойное подчеркивание перед приставкой. 

Приставка `__lt`
---

`__lt` - приставка эквивалентная применению `<` (меньше)

```python
Entry.objects.filter(pub_date__lt='2006-01-01')
```
Превращается в следующий SQL запрос
```sql
SELECT * FROM blog_entry WHERE pub_date < '2006-01-01';
```

---

Приставка `__lte`
---
`__lte` - приставка эквивалентная применению `<=` 
(меньше, равно)

```python
Entry.objects.filter(pub_date__lte='2006-01-01')
```
Превращается в следующий SQL запрос
```sql
SELECT * FROM blog_entry WHERE pub_date <= '2006-01-01';
```

---

Приставка `__exact`
---
`__exact` - поиск с точным совпадение

```python
Entry.objects.get(headline__exact="Cat bites dog")
```
Превратится в 
```sql
SELECT ... WHERE headline = 'Cat bites dog';
```

Если вы не указали тип поиска, то есть если ваш аргумент
ключевого слова не содержит двойного подчеркивания, 
предполагается, что это тип поиска exact

Например, следующие два оператора эквивалентны:

```python
Blog.objects.get(id__exact=14)  # Explicit form
Blog.objects.get(id=14)         # __exact is implied
```

---

Приставка `__iexact`
---

`__iexact` - Соответствие без учета регистра. Итак, запрос:

```python
Blog.objects.get(name__iexact="beatles blog")
```

То есть подойдут все следующие записи:

    "Beatles Blog"
    "beatles blog" 
    "BeAtlES blOG"

---

Приставка `contains`
---

`contains` - Проверка на сдерживание с учетом регистра. 

Например:

```python
Entry.objects.get(headline__contains='Lennon')
```

Примерно переводится на этот SQL:

```sql
SELECT ... WHERE headline LIKE '%Lennon%';
```

Найдет `Today Lennon honored` но не найдет `today lennon honored`

---

Приставка `__icontains`
---

`__icontains` - тоже что и `contains` но не чувствительный к 
регистру

---

Приставка `__gt`
---

`__gt` - Больше чем, эквивалентно `>`

```python
Entry.objects.filter(id__gt=4)
```
Эквивалент SQL:
```sql
SELECT ... WHERE id > 4;
```

---

Приставка `__gte`
---

`__gte` - Больше чем равно, эквивалентно `>=`

```python
Entry.objects.filter(id__gte=4)
```
Эквивалент SQL:
```sql
SELECT ... WHERE id >= 4;
```

---

Приставка `__startswith`
---

`__startswith` - Начинается с (с учетом регистра).

```python
Entry.objects.filter(headline__startswith='Lennon')
```

Эквивалент SQL:

```sql
SELECT ... WHERE headline LIKE 'Lennon%';
```

---

Приставка __istartswith`
---

`__istartswith` - Тоже самое что и `__istartswith` но 
(без учета регистра).

---

Приставка `__endswith`
---

`__endswith` - Заканчивается на (с учетом регистра)

---

Приставка `__iendswith`

`__iendswith` - Заканчивается на (без учета регистра).

---

Константы `MEDIA_ROOT` `MEDIA_URl`
---

В модели мы можем определить поле типа `models.ImageField` для 
автоматической загрузки изображения, в папку по указанному пути, вот 
как это работает.

Если в поле модели мы указываем такой путь:

```python
    photo = models.ImageField(upload_to="Изображение")
```

То когда мы сделаем загрузку изображения на сайт, в корне сайта будет 
автоматически создана директория с названием `Изображение` и в нее 
будет помещен загруженный файл, и корневой каталог будет выглядеть так:

```
coolsite /
    coolsite  
    db.sqlite3  
    manage.py  
    polls  
    static  
    women  
    Изображение /
        Screenshot_from_2021-07-13_19-10-38.png
```

Это очень удобно, но есть одна проблема, если у м еня есть несколько 
моделей, и у каждой из моделей будут свое поле для загрузки изображений,
 мы будем получать новую директорию для каждой из моделей, к примеру так:

```python
class Model_1(models.Model):
    photo = models.ImageField(upload_to="photos/%Y/%m/%d/")
    
class Model_2(models.Model):
    photo = models.ImageField(upload_to="Изображение")
```

То мы получим следующую директорию:

```
coolsite /
    coolsite  
    db.sqlite3  
    manage.py  
    polls  
    static  
    women  
    photos / 
        2021 /
            08 /
                20 /
                    bass.png
    Изображение /
        Screenshot_from_2021-07-13_19-10-38.png
```

В файле `coolsite/settings.py` мы можем определить 2 константы: 

```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URl = '/media/'
```

Что нам это дает:

`os.path.join()` - соединяет строки вместе, при помощи специального
разделителя, который используется именно в текущей ОС, и возвращает
эту строку.

`BASE_DIR` - предопределенная константа, содержит в себе полный путь 
от корня всей ОС, до корня сайта.

`MEDIA_ROOT = os.path.join(BASE_DIR, 'media')` - эта константа будет
содержать полный путь от корня ОС, до папки `media` в корне сайта.

`MEDIA_ROOT` - когда эта константа определена, то при загрузки файлов
на сайт, будет создана директория `media` и в ней уже и будет созданы
директоии что были указаны в поле моделей.

И теперь наша структура будет выглядеть так:

```
coolsite / 
    coolsite  
    db.sqlite3  
    manage.py  
    polls  
    static  
    women
    media /
        photos / 
            2021 /
                08 /
                    20 /
                        bass.png
        Изображения / 
            2021 /
                08 /
                    20 /
                        Screenshot_from_2021-07-13_19-10-38.png
```

Таким образом структура загрузки изображений будет формироваться 
следующим образом `MEDIA_ROOT + models.ImageField()`

После того как мы создали модель с полем загрузки изображения, и 
подключили его модель в админку, а админке мы можем добавить запись
в БД, и загрузить картинку, однако открыть ее мы не сможем, для этого
потребуется сделать ряд изменений.

В файле `coolsite/settings.py` есть наши настройки, тут `MEDIA_ROOT` 
как мф уже выяснили, отвечает за директорию загрузки файлов.
```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URl = '/media/'
```

Другая же константа `MEDIA_URl` отвечает за то, по какому URL мы сможем
обращаться к этому файлу, устанавливаем ее в значение `/media/`

В файле `coolsite/urls.py` добавляем следующую запись с импортами нужных
модулей.

```python
"""coolsite URL Configuration"""
from django.contrib import admin
from django.urls import path, include
from women.views import *

# Импорт для работы с статическими файлами
from django.conf.urls.static import static
from coolsite import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('women', include('women.urls')),
    path('polls', include('polls.urls'))
]

# Если мы в режиме DEBUG=True то добавляем специальный URL путь
# к статическим загруженным файлам в /media/
if settings.DEBUG:
    urlpatterns += static(
        settings.MEDIA_URL, document_root=settings.MEDIA_ROOT
    )
```

После этого все скаченные изображения будут доступны для просмотра, в
том числе и из админки.
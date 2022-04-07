Настройки `MEDIA_ROOT` и `MEDIA_URl`
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

`MEDIA_ROOT` - когда эта константа определена, то при загрузке файлов
на сайт, будет создана директория `media` и в ней уже и будет созданы
директории, что были указаны в поле моделей.

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


---
Установка `Pillow`
---
При использовании поля `models.ImageField` требуется библиотека `Pillow`
иначе будет ошибка такого рода:

> ERRORS:
> women.Women.photo: (fields.E210) Cannot use 
> ImageField because Pillow is not installed.
>        HINT: Get Pillow at https://pypi.org/project/Pillow/
>         or run command "python -m pip install Pillow".

Установка Pillow

> pip3 install Pillow


---
Класс `Meta` для моделей
---

Для каждой модели можно описать свой класс `class Meta` который 
будет задавать опции для модели, к примеру следующим образом:

```python
from django.db import models

class name_model(models.Model):
    
    id = models.IntegerField('Уникальный ID', primary_key=True)
    value = models.IntegerField('Значение', null=True)

    class Meta:
        managed = False
        db_table = 'name_for_table_in_db'
```

---
Атрибуты класса `Meta`
---

[Про атрибуты класса `Meta` почитать тут ](https://djangodoc.ru/3.1/ref/models/options/)

- `managed`
   > Указывает, отслеживать создание миграции или нет, по дефолту
   > находится в значении `True` что значит отслеживать для управления миграций.


- `db_table` 
   > Название для таблицы, по этому имени в БД при применении
   > миграции, будет создана таблица.
   >
   > По дефолту в Django название таблицы на основании модели создается путем,
   > обьединения названия приложения с названием класса модели, указывая 
   > атрибут **db_table** мы переопределяем это название, и с этим именем и будет 
   > создана таблица в БД.

- `ordering` 
   > Список с именами полей модели, по которому будет происходить 
   > сортировка списка объектов когда мы их получаем
   >
   > ```python
   > ordering = ['pub_date']
   >```

- `verbose_name`  
   > Название для модели в ед. числе, если этого параметра не
   > установлено, то Django устанавливает название беря имя модели, и переводя
   > его в нижний регистр.
   >
   >```python
   >verbose_name = "pizza"
   >```

- `verbose_name_plural` 
   > Название для модели во множ. числе, если этот 
   > параметр не установлен, то берется имя от `verbose_name` и добавляется
   > буква `s`
   >
   >```python
   >verbose_name_plural = "stories"
   >```

---
`Signals` - сигналы моделей
---
В Django присутствует такой механизм как `диспетчер сигналов`.
Сигналы оповещают о том что происходит некоторое событие в
некоторой части приложения.

Есть ряд типов сигналов:

- `post_save`, `pre_save`
   > Срабатывает когда сохраняют модель, именно сохраняют, через метод
   > `model.save()` а не через метод `model.objects.create()` 

 
- `pre_init`, `post_init` 
   >Срабатывает на создание обьекта 
   > через метод create(), а не через save() к примеру так `Category.objects.create()`


- `post_delete`, `pre_delete` 
   > Срабатывает когда удаляют обьект модель

 
- `request_finished`, `request_started` 
   >Срабатывает когда отправляют HTTP запрос

Суть в том что мы регистрируем функцию, которая будет вызываться
автоматически при одном их произошедших событий, то есть создает функцию
коолбэк которая будет отрабатывает


Есть 2 способа которым можно зарегистрировать сигналы:

- Использовать `Signal.connect()` для регистрации функции.
- Использовать декоратор `receiver`

Если используем декоратор `@receiver` то передаем в параметры, 
класс функции сигнала, и модель к которой будет привязан сигнал.

Сама функция сигнала принимает 2 параметра, `sender` это сам класс
модели, и `instance` это созданный обьект модели, на который сигнал
и среагировал.

Пример сигнала срабатывающего после создания/обновления записи
в модели `ImagesModel` в файле `models.py`
```python
from django.dispatch import receiver, Signal
from django.db.models.signals import post_save, pre_delete, pre_save

@receiver(post_save, sender=ImagesModel)
def on_change_post(sender, instance, **kwargs):
    """Сигнал post_save - Создаем мини версию изображение"""
    if instance.big_image and os.path.isfile(instance.big_image.path):
        create_small_img(instance, size=500)
```

Когда исполняется сохранение обьекта модели, начала отрабатывает 
сигнал `pre_save` потом метод сохранения самой модели `save` и 
после сигнал `pre_save`. Но все эти 3 операции происходят не по
отдельности, а представляют собой один блок, и само физическое
сохранение модели, записью в БД на жесткий диск, происходит в 
самом конце после метода `pre_save`

---
Как избежать рекурсии при сохранении модели в сигналах ?
---

Представим такую ситуацию, нам надо сохранить модель и после этого
сделать что-нибудь с сохраненными данными, и снова сохранить модель,
сделать это мы можем в методе `post_save` но ! Если мы в методе 
`post_save` вызовем метод `objModel.save()` то это вызовет 
рекурсию.

Избежать этого можно 2 способами:

1. Вместо метода `save()` использовать метод `update()` этот метод
не вызывает метода `save()` в работу на обновление всей записи, а
только обновляет одно единственное поле, и это не вызовет рекурсии.

Пример:
```python
@receiver(post_save, sender=Images)
def on_change_post(sender, instance, **kwargs):
    Images.objects.filter(pk=instance.pk).update(title='Новое название')
```


2. Второй способ это просто присвоить нужному полю модели, нужное
значение, и все ! Это работает по скольку само сохранение в БД
происходит после метода `post_save` 

Пример:
```python

@receiver(post_save, sender=Images)
def on_change_post(sender, instance, **kwargs):
    instance.title = 'Новое название'
```

---
Транзакции БД с помощью `Django` 
---
`Django` предоставляет набор методов для управления обработкой транзакций
баз данных, транзакции доступны из модуля `django.db.transaction`

Суть транзакций в БД, заключается в атомарности ряда `SQL` запросов, если
просто некоторое количество операций `SQL` объединяются в набор, и все эти
`SQL` запросы исполняются в оперативной памяти, с таблицами, что были скопированы
в эту память, если все запросы исполнились без проблем, то все изменения
из оперативной памяти переносятся на жесткий диск, в реальную память.

Обернув функцию декоратором `@atomic` все взаимодействия с БД что 
будут происходить в методе `save_multiple_objects` будут происходить в 
одной транзакции.

```python
from django.db import transaction

@transaction.atomic
def save_multiple_objects(obj_list):
   for o in obj_list:
     o.save()
```

Все запросы к БД, что исполняются в этом представлении, будут выполнены в 
одной транзакции. То же касается всех вложенных функций если они имеют взаимодействие
с `SQL`

---
Атрибут `_meta` модели
---
У моделей есть защищенный атрибут `_meta`

`_meta.get_fields()` - возвращает список с объектами полей модели

```python
self._meta.get_fields()

# Вывод
# 0 = {AutoField} creating.MenModel.id
# 1 = {MultiSelectField} creating.MenModel.men_sex
# 2 = {MultiSelectField} creating.MenModel.men_age
# 3 = {MultiSelectField} creating.MenModel.men_income
# ...
```

Используя это мы можем итерироваться по этому списку и получить 
все имена полей модели.

Через атрибут `_meta` получаем список с названиями всех полей модели
в виде строк.
```python
model_field_names = [field.name for field in self._meta.get_fields()]

# Вывод
# {str} id
# {str} men_sex
# {str} men_age
# {str} men_income
```

`self.__dict__` - закрытый атрибут модели, представляет собой словарь
с атрибутами объекта.

Получаем словарь с названиями полей и выбранными значениями
```python
fields_and_values = {field.name: self.__dict__.get(field.name) for field in self._meta.get_fields()}

# Вывод
# {
#     'id': None,
#     'description': 'Введенное название',
#     'men_sex': [1],
#     'men_age': [1, 2],
#     'home_region': [18],
# }
```

Таким образом через атрибут `_meta` можно получить все поля и введенные
в эти поля значения пользователем.

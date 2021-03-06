Автоматическая генерация моделей
---

В случае если мы не разрабатываем приложение с нуля, работаем с уже существующей БД,
нам потребуется работать с моделями этой БД, но при создании модели, чтобы иметь 
возможность работать с ней, нам потребуется сделать миграцию и применить ее, но как 
быть когда таблица уже существует ? 

Как можно поступить:
- Создать модель, создать миграцию на нее и применить ложную миграцию, в таком случае
`Django` пометит миграцию как примененную, но в реальности ничего не сделает, так мы 
получим возможность работать с моделью, и миграция будет применена

Что делать если таблица для которой мы хотим сделать модель, имеет множество связей и
полей, на такой случай у `Django` есть возможность генерировать модели автоматически на 
основе уже существующей таблицы в БД, для этого есть специальная утилита `inspectdb`

По скольку это утилита, ее результат мы записываем с помощью операторы перенаправления
потока, записываем результат в файл :

```
python manage.py inspectdb > models.py
```

При таком создании модели, `Django` автоматически устанавливает параметр `managed = False`
в классе `Meta`, помним что это означает не отслеживать изменения в таблице, это 
значит что `Django` не управляет жизненным циклом данной таблицы и не отслеживает 
какие-либо изменения в ее структуре которые могли бы произойти, просто меняем этот
параметр на `True` или удаляем его ибо `True` это дефолтное значение.

```python
   class Meta:
       managed = False
```
---

Далее с такой моделью можно работать как и с любой другой, генерировать миграцию и 
применять ее как фейковую, ибо таблица уже существует и нам всего лишь требуется 
что бы в таблице миграций появилась отметка о применении миграции.

Инспектор баз данных полезен когда мы интегрируем проект `Django` к уже существующей
БД, а не создаем проект с самого начала.

---
Работа с разными схемами в одной БД
---

Помним что в СУБД `Postgres` есть деление на схемы, схема это просто пространство имен,
когда мы создаем таблицы в БД, она создается в пространстве имен схемы, и обращаться
мы можем к определенной таблице что находится в определенной схеме, таким образом мы
имеем возможность разбивать наши таблицы по участкам, скажем разделять таблицы по 
тематике.

По дефолту, каждая БД при создании определяет схему с названием `public` и все таблицы
будут создаваться именно там, если не указано иное.

При создании таблиц, название таблицы берется либо из названия модели нижнем регистре,
либо мы можем сами указать название под которым будет создана таблица, для этого
в классе `Meta` указываем атрибут `db_table` к примеру так :

```python
class Meta:
    db_table = 'my_model'
```

Возникает вопрос, как работать с другой схемой, если мы имеем несколько схем в БД,
как указать что наша модель должна работать с другой схемой, сделать это можно
следующим указанием:

```python
class Meta:
    db_table = 'schema_second\".\"my_model'
```

---

Представим себе ситуацию, у нас есть уже работающая БД, в которой определено несколько
рабочих схем, наша задача создать проект на `Django` и интегрировать эту уже 
существующую работающую БД в наше приложение.

Само приложение `Django` даже без создания наших моделей, уже имеет свои собственные
миграции, и перед началом работы с проектом эти миграции требуется накатить, по 
дефолту все миграции накатываются в главную схему `public`, но по скольку нам 
желательно отделить уже существующие таблицы от таблиц самого `Django`, и 
накатывать миграции туда, а не в ту схему, что уже имеет свои таблицы, сделать это
можно при помощи указания специальной опции при создании подключения к БД.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'db',
        'USER': 'postgres',
        'PASSWORD': 123,
        'HOST': 'localhost',
        'PORT': 5432,
        'OPTIONS': {
            'options': '-c search_path=schema_second'
        }
    }
}
```
Теперь все миграции будут литься в ту схему которую мы указали в параметрах 
подключения.

Так же для взаимодействия с уже существующими таблицами, нам потребуется модель для 
этой таблицы, сделаем ее с помощью `inspectdb`, далее у сгенерированных моделей 
указываем с какой схемой им требуется работать, таким образом мы можем иметь 
множество моделей и каждая из них работает со своей таблицей в своей схеме, а все
миграции будут литься в ту схему, которую мы указали при подключении, то есть ту 
схему в которой и находятся наши таблицы для приложения `Django`

---
Модели и загрузка файлов
---

Загрузка файлов в модели `Django`, для `Django` файлы `doc`, `docx`, `xlsx` или
архивы `zip`, `rar` все загружаются одинаково через класс `FileField`

```python
from django.db import models
from django.core.validators import FileExtensionValidator

class DocModel(models.Model):
    """Модель для хранения архива zip"""
    name = models.CharField(verbose_name='Название', null=True)
    date_create = models.DateTimeField(verbose_name='Дата создания', auto_now_add=True)
    
    zip_arch = models.FileField(
        default=None, null=True, blank=True,
        verbose_name='Zip архив',
        upload_to='docs',
        validators=[
            FileExtensionValidator(
                message=f"Разрешенный формат : zip",
                allowed_extensions='zip'
            ),
        ]
    )
```

---
Замечание : 
> Имея 2 поля `date_create` и `zip_arch` одно для хранения дата/время создания
> записи, другое для хранения файла, для поля времени создания мы 
> указываем опцию `auto_now_add=True` для автоматического задания времени создания
> записи, эта опция работает не на уровне приложения, а на уровне самой БД
> 
> В ситуации когда мы загружаем файл все работает нормально, но в случае если файл 
> мы генерируем в самом приложении, и если нам требуется отмечать время создания этого 
> файла, и отражать эту метку времени в названии самого файла, то мы можем задать 
> метку времени в самом приложении, и сделать его частью названия файла, но в таком 
> случае эта метка времени будет отличаться от метки времени в самой БД.
---

При создании записи и загрузки файла через форму или админку, все идет по одному 
сценарию и обрабатывается самим `Django`, но в случае если мы создаем файл или архив 
в самом приложении, то порядок действий будет немного иным.

Поле `FileField`, дает нам несколько методов для взаимодействия с файлом, для 
сохранения файла созданного в самом приложении, мы можем использовать метод
`save` самого объекта класса`FileField` что будет в атрибуте `zip_arch`

Все что нам потребуется это название для сохранения файла, это его имя и путь к 
файлу.

При сохранении файла таким способом автоматически вызывается процесс сохранения
всей сущности, так что если использовать такой способ сохранения в самом методе
`save` то это вызовет рекурсию, для того что бы это предотвратить и файл сохранялся
вместе со всей сущностью без рекурсии, то для этого моно указать специальную
опцию  `save=False`

Вот пример
```python
from django.core.files import File
from django.db import models

class DocBasketModel(models.Model):
   
    ...
    
    def save(self, *args, **kwargs):
        
        # сохраняем файл
        self.zip_arch.save(
            name=zip_name,
            content=File(open(zip_path, "rb")),
            save=False
        )
        
        # сохраняем сущность
        super(DocBasketModel, self).save(*args, **kwargs)
```

---
Работа с многими соединениями БД
---

У `Django` есть возможность работать с несколькими БД, одновременно. 

В файле настроек `settings.py` константа `DATABASES` может определять несколько 
подключений к разным БД, когда мы используем какой либо `ORM` запрос, или используем
миграции `migrate`, то по дефолту все эти действия происходят с главным соединением
`default` если не указано иное соединение.

Раздел для подключения `default` можно оставлять пустым, в таком случае вам придется
указывать используемое соединение каждый раз.

Пример : 

```python
DATABASES = {
     'default': {
        'NAME': 'dj_db_one',
        'ENGINE': 'django.db.backends.postgresql',
        'HOST': 'localhost',
        'USER': 'postgres',
        'PASSWORD': '123'
    },
    'two': {
        'NAME': 'dj_db_two',
        'ENGINE': 'django.db.backends.postgresql',
        'HOST': 'localhost',
        'USER': 'postgres',
        'PASSWORD': '123'
    },
    'three': {
        'NAME': 'dj_db_three',
        'ENGINE': 'django.db.backends.postgresql',
        'HOST': 'localhost',
        'USER': 'postgres',
        'PASSWORD': '123'
    }
}
```

---

Использование миграции к определенной БД можно сделать передав специальную опцию 
в команду миграции.

```
python3 manage.py migrate --database=two
```

---
Выбор `using` базы данных в запросах `ORM`
---

Имея множество БД и модели каждая из которых привязана к своей БД, мы можем сами
контролировать в какой БД будет исполняться запрос `ORM`

Для этого используется метод `using` в объекте модели при совершении запроса, в нем
просто сказываем название соединения с БД.

```python
# Соединение через default
MyModel.objects.all()

# То же соединение через default
MyModel.objects.using('default').all()

# Подключение к другой БД
MyModel.objects.using('two').all()
```

---
Выбор базы данных для сохранения `save` 
---

Для сохранения модели методом `save()` просто указываем нужное соединение аргументом
метода.

```python
MyModel.save(using='two')
```

---
Перемещение объекта из одной БД в другую
---

Имея возможность работать с несколькими Бд одновременно, мы получаем возможность
перемещать создавать и удалять объекты из одних БД в другие БД.

Мы можем сохранять одинаковые объекты в разных БД, создадим новый обьект, в момент 
его создания, обьект не имеет первичного ключа, при сохранении такого обьета в 
первой БД, приведет к появлению у нее первичного ключа и уже при сохранении такого
обьекта в другой БД, происходит один из 2 вариантов:

1) В БД `two` такого первичного ключа не существует, и в таком случае новая запись
с таким ключем будет создан.
2) Запись с таким ключем уже существует в БД `two` и тогда старая запись будет 
перезаписана новой.

```python

# Создание полностью нового обьекта
obj = MyModel(name='Новая запись')

# Сохранение объекта в 2 БД сразу
# obj имеет атрибута pk = None
obj.save(using='default')

# Объекту obj установлен первичный ключ либо перезапишет,
# либо создаст новую запись в зависимости от данных в таблице
obj.save(using='two')
```

Избежать такой проблемы можно с помощью очистки у объекта атрибута с первичным 
ключем

```python
obj = MyModel(name='Новая запись')

obj.save(using='default')
obj.pk = None
obj.save(using='two')
```

---
Выбор базы данных для удаления `delete` 
---

По дефолту когда мы получаем объект из БД, то удаление будет происходить из той же БД,
то есть если мы указываем получить объект из БД, что не `default` и после удаляем этот
объект, то удалится он именно из той Бд из которой и был получен.

```python
# Получен из БД two и от туда же и удален
obj = MyModel.objects.using('two').get(pk=5)
obj.delete()
```

Можно явно указать из какой БД следует удалить обьект, сделать это можно таким же
способом как и сохранение, с помощью указания опции `using='название подключения'` 

```python
obj = MyModel.ojects.get(pk=15)
obj.delete(using='two')
```

---
Методы `bulk_update / bulk_create` с разными бд. 
---

Методы `bulk_update` и `bulk_create` позволяют массово создавать и обновлять наборы
записей в БД, но тут есть одна тонкость, для подобных методов указание используемого 
подключения к БД следует указывать не в конце `ORM` запроса, а в середине.

Дело в том что `QuerySet` проводят ленивые вычисления, когда вы делаете `ORM` запрос
на получения данных, и указываете соединение к БД в конце `ORM` запроса, то 
`QuerySet` не вычисляется сразу, с начала он генерирует `SQL` запрос, и устанавливает
соединение и только после этого реально делает обращение к БД, но методы
`bulk_update` и `bulk_create` не ленивы и производят выполнение запросов сразу,
так что указав соединение к Бд в конце `ORM` запроса, реальный запрос будет выполнен
не дойдя до указания этого соединения, так что для правильной работы таких методов,
требуется указывать соединение к БД в середине запроса, иначе будет взято соединение
типа `default` где вашей таблицы может и не быть, и вы получите ошибку, ваша 
таблица попросту не будет найдена.

```python
# хорошо
MyModel.objects.using('not_default_connect_db').bulk_update()

# плохо
MyModel.objects.bulk_update().using('not_default_connect_db')
```

Пример правильного обновления.

```python

# набор объектов модели
update_queries = [obj_1, obj_2,  obj_3]

# массовое обновление объектов моделей в бд
MyModel.objects.using('not_default_connect_db').bulk_update(
            update_queries,
            fields=['name', 'title', 'count']
        )
```


---
Модели из разных БД в админке `Django`
---

Что бы администрировать модели из разных БД в административной панели `Django`
следует переопределить в классе админки атрибут `using`

```python
from django.contrib import admin
from .models import MyModel

@admin.register(MyModel)
class MyModelAdmin(admin.ModelAdmin):
    
    # указываем соединение БД откуда брать модель
    using = 'two'
```

Поля моделей
---
Каждое поле модели является классом, что поставляется из стандартной 
библиотеки `django.db.models`

---
Поле `AutoField`
---

Когда создается модель, поле `id` с автоинкрементом и связанной с ним
последовательностью создается автоматически, по этому нет нужны создавать
поле `id`, однако это все же можно сделать

```python
    id = models.AutoField(auto_created=True, primary_key=True)
```

---
Поле `CharField`
---
Строковое поле, для строк малого и большого размера.

Атрибуты:

- `max_length` - указывает максимальное количество символов для этого поля.
- `verbose_name` - название алиас которое будет использоваться в админке
- `unique` - `bool` обеспечить уникальность записи в таблице 

Пример: 
```python
name = models.CharField(verbose_name='Название', max_length=100)
```

---
Поле `TextField`
---
Большое текстовое поле.

Пример
```python
description = models.TextField(verbose_name='Описание')
```


---
Поле `BooleanField`
---
Поле истина/ложь.

```python
    is_active = models.BooleanField(default=True)
```

---
Поле `DateField`
---
Дата, представленная в Python экземпляром `datetime.date`.

Атрибуты:

- `auto_now` - автоматически вставляет текущую дату, когда объект сохраняется. 
стоит это использовать что бы отслеживать дату последнего изменения записи в БД.
Данный метод срабатывает при использовании метода сохранения самой модели `Model.save()`.
Но он не сработает при обновлении ряда записей методом `QuerySet.update()` 

- `auto_now_add` - вставляет текущую дату в момент создания записи.


---
Поле `DateTimeField`
---
Дата и время, представленные в моделях `Django` как экземпляры клсса 
`datetime.datetime`.

Существует 3 основных цели хранения даты/времени:
- просто дата/время
- дата последнего обновления записи в БД
- дата создания записи

№1 хранение даты и времени
  
    just_data = models.DateField(verbose_name='Прсото дата и время', blank=True, null=True)

№2 хранение даты последнего обновления даты/времени

    date_registration = models.DateTimeField(verbose_name='Дата регистрации', auto_now_add=True)

№3 хранение даты/времени создания 

    date_update = models.DateTimeField(verbose_name='Дата обновления', auto_now=True, editable=False)

---
Поле `TimeField`
---
Время, представленное в Python экземпляром `datetime.time`. 

Атрибуты:
- Имеет такие же атрибуты как и тип поля `DateField`

---
Поле `IntegerField`
---
Целое число.


---
Поле `FloatField`
---
Поле с плавающей точкой

    num = models.FloatField(verbose_name='Стоимость (руб.)', blank=True, null=True)


---
Поле `JSONField`
---
Хранит строку формата `json`

```python
    json_field = models.JSONField(verbose_name='Настройки для шаблона')
```

---
Поле `PositiveIntegerField`
---
Целое число, может быть только положительным.

---
Поле `FileField`
---

Пример хранения файла `zip`

```python
from django.db import models
from django.core.validators import FileExtensionValidator

class ZipModel(models.Models):

    ...

    zip_reports = models.FileField(
        default=None, 
        null=True, 
        blank=True,
        verbose_name='Zip архив',
        upload_to=settings.DIR_ZIP,
        validators=[
            FileExtensionValidator(
                message=f"Разрешенный формат : zip",
                allowed_extensions='zip'
            ),
        ]
    )

```

В `Django` происходит хранение как самого файла скаченного по указанному пути 
так и сохранения записи в БД, однако при удалении записи из админки, происходит
удаление только из БД, но сам файл не даляется физически, эта работа остается на
пользователя, сделать это можно с помощтю системы сигналов, создадим сигнал для
модели что будет удалять файл после удаления записи из БД.

```python
from django.db.models.signals import post_delete
from django.dispatch import receiver

@receiver(post_delete, sender=DocBasketModel)
def post_delete_basket(sender, instance, **kwargs):
    """Удаляем связанный с записью zip архив"""
    if os.path.isfile(instance.zip_reports.path):
        os.remove(instance.zip_reports.path)
```


---
Поле `ImageField`
---

В отличие от других сохраняемых статических файлов, при указании сохраняемых изображений
можно использовать паттерны `upload_to="photos/%Y/%m/%d/"` что облегчит поиск.

```python
    image = models.ImageField(upload_to="photos/%Y/%m/%d/", blank=True, verbose_name='Фото')
```
    
---
Поле `UUIDField`
---
Поле для хранения универсально уникальных идентификаторов.
Использует класс Python `UUID`.

Пример
```python
import uuid
from django.db import models

class MyUUIDModel(models.Model):
    uniq_uuid = models.UUIDField( 
                          default=uuid.uuid4, 
                          editable=False
    )
```

---
Поле `EmailField`
---
Обычное поле `CharField` которое автоматически использует валидатор
`EmailValidator` для проверки данных на содержание валидного `email`

---
Создание связей между моделями
---
[Почитать про связи моделей тут](https://djbook.ru/rel3.0/topics/db/models.html#relationships) 

При создании связей между таблицами, мы можем использовать 3 класса
они следующие:


`models.ForeignKey()` - для связей `M:1`

`models.ManyToManyField()` - для связей `M:M`

`models.OneToOneField()` - для связей `1:1`

---
Поле `ForeignKey` связь `M:1`
---

Примером создания таких связей служат 2 модели, категории уроков, 
и сами уроки, у модели что реализует одну запись принадлежащую
какой то из категорий, мы реализуем поле класса `ForeignKey()`

```python
class python_section(models.Model):
    """Разделы"""
    title = models.CharField(max_length=255, verbose_name="Заголовок")
    description = models.TextField(blank=True, verbose_name="Описание")

class python_lesson(models.Model):
    """Уроки по разделам"""
    python_section = models.ForeignKey(
                                python_section, 
                                on_delete=models.CASCADE, 
                                verbose_name="Родительский раздел"
                                )
    title = models.CharField(max_length=255)
    content = RichTextUploadingField(config_name='default', blank=True)
```

Модель категории называется `Первичной` а модель, что является ее
подчиненной(зависящей от первичной), называют `Вторичной`

При создании такой связи, первый аргумент указывает на `Первичную`
модель, от которой будет зависеть наша запись, второй параметр
указывает как себя вести при удалении родительского раздела,
скажем тоже удалять все подчиненные записи.

```python
python_section = models.ForeignKey(
                                python_section, 
                                on_delete=models.CASCADE
                                )
```

Есть одно правило, когда мы создаем поле для, связь во `Вторичной`
таблице, то к этому полю в конце, дается приставка `_id` таким 
образом, если мы создаем поле для связи `python_section` то в 
таблице оно будет выглядеть как `python_section_id` и так же к 
нему можно и обращаться как к полю класса.

```python
lesson = python_lesson.objects.get(pk=1)
lesson.python_section_id
```


---
Атрибуты полей модели
---

- `on_delete` 
при удалении связанной записи

Как определять ограничения при удалении родительского раздела:
`on_delete = models.CASCADE` - При удалении записи из `первичной` 
модели, удалить все записи из `вторичной` модели.


- `on_delete = models.PROTECT` - Запретить удалять записи из 
`первичной` модели если она используется во `вторичной` модели,
то есть если есть хотя бы одна запись во `вторичной` модели 
удалять из `первичной` модели эту запись нельзя.


- `on_delete = models.SET_NULL` - Если из `первичной` модели удалили
запись к которой была привязана `вторичная` модель, то во
вторичной модели это поле связи, принимает значение `NULL`


- `on_delete = models.SET_DEFAULT` - тоже что и метод `SET_NULL` но 
вместо значения `NULL` ставится дефолтное значение, которое 
определено в параметре `default`


- `on_delete = models.SET()` - То же самое, но устанавливается 
пользовательское значение.


- `on_delete = models.DO_NOTHING()` - Ничего не делать, при удалении
записи в `первичной` модели, во вторичной с этим полем, ничего не 
произойдет, и запись в поле останется висеть, и указывать в пустоту.

---
Атрибут `db_index`
---

Индексы, указывая в полем модели параметр `db_index=True` мы делаем
поле индексированным, и поиск по нему будет вестись быстрее.

```python
name = models.CharField(max_length=100, db_index=True)
```

---
Полезный прием
---
Нам требуется исключить из работы некоторые поля модели, мы можем
создать статический метод в котором определим кортеж с названиями полей
которые хотим исключить.

```python
class MyModel(models.Model):
    ...

    @staticmethod
    def get_exclude_filter_fields():
        """Названия полей, которые мы исключаем из фильтрации"""
        return ('id', 'description', 'create_date', 'update_date', )
```

Далее мы можем вызывать этот метод и проверять не находится ли поле
в списке запрещенных
```python
if field_name not in self.get_exclude_filter_fields():
    pass
```

---
Ссылка поля модели на саму себя
---

Обычно при создании поля со связью 1:M мы связываем 2 разных таблицы,
но что если значение нового поля должно высчитываться из других значений 
этого же поля ? 

Нам требуется создать связь поля модели на поля этой же модели, сделать это
можно создав обычную связь один ко многим `models.ForeignKey` с указанием в 
качестве таблицы связи `self` что означает ссылаться на саму себя.

Когда мы создаем поле для связи 1:M то у таблицы на которую мы ссылаемся 
создается связанное поле `related_name` тут же ссылаясь на саму себя мы 
должны указать `+`

```python
parent_photo = models.ForeignKey(
    'self', null=True, blank=True, 
     related_name='+',
     on_delete=models.SET_NULL, 
     verbose_name='Поле с сылкой на самих себя'
)
```

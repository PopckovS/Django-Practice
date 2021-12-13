Методы ORM
---

[//]: # (проверка существования данных в кверисете)
[//]: # (QuerySet.exists&#40;&#41;)


---
Примеры запросов ORM
---
Послед создания модели данных, мы получаем доступ к API, которая 
предоставляет CRUD операции к модели.

Класс модели представляет собой таблицу базы данных, а экземпляр этого
класса это конкретная запись в таблице.

Будем работать с 3 следующими моделями:

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    number_of_comments = models.IntegerField()
    number_of_pingbacks = models.IntegerField()
    rating = models.IntegerField()
```

---

**1. Создание новой записи методом `save()`**

Чтобы создать новую запись, создадим обьект модели, передадим
в обьект данные для именованных атрибутов, для сохранения записи
в таблице, вызываем метод `save()`.

```python
from .models import Blog

# Создание нового экземпляра модели, новой записи в таблице 
blog = Blog(name='Новый Блог №1', tagline='Все новости блога №1')
blog.save()
```
`save()` - Вызывает метод `INSERT` и записывает данные в таблицу,
до тех пор пока метод не вызван, обращения к Бд не будет.

Данный способ создает запись за 2 шага, есть другой метод
`create()` для создания записи за 1 шаг.

---

**2. Создание новой записи методом `create()`**

Другой способ создания новой записи, в один этап, это 
метод `create()` этот метод создает обьект модели, и тут же 
сохраняет его в таблице.

```python
blog = Blog.objects.create(name='Новый Блог №1', tagline='Все новости блога №1')
```

---

**3. Обновление записи с `ForeignKey`**

Если создаем запись модели `Entry` у которой одно из полей по 
связи 1:M связанно с моделью `Blog` то в это самое поле, которое 
отвечает за связь во `вторичной` таблицы поместим сущьность
`первичной` таблицы

```python
 from blog.models import Blog, Entry

 # Получаем запись модели Entry 
 entry = Entry.objects.get(pk=1)
 
 # Получаем запись модели Blog
 cheese_blog = Blog.objects.get(name="Cheddar Talk")
 
 # В поле blog что связывает между собой таблицы помещяем сущьность
 entry.blog = cheese_blog
 entry.save()
```

---

**4. Обновление записи с `ManyToManyField`**

Есть обьект модели `Entry` у которого есть поле `authors`
оно связано связью M : M моделью `Author` для того чтобы 
добавить обьект модели `Author` в поле `authors` требуется
использовать специальный метод `add()` 

Создаем запись `joe` модели `Author` и при помощи метода 
`add()` добавляем в `entry` что является обьектом модели
`Entry`

```python
from blog.models import Author

joe = Author.objects.create(name="Joe")
entry.authors.add(joe)
```

Это добавление одного обьекта в для связи, для добавления 
нескольких обьектов, просто передаем их все в тот же метод
`add()`

```python
from blog.models import Author

john = Author.objects.create(name="John")
paul = Author.objects.create(name="Paul")
george = Author.objects.create(name="George")
ringo = Author.objects.create(name="Ringo")

entry.authors.add(john, paul, george, ringo)
```

---

**5. Менеджер для доступа к записям таблицы `objects`**

`Managers` - Менеджер класса модели, доступный через 
атрибут `.objects` вызывается только у класса, и предоставляет
доступ к записям таблицы. 

`Managers` доступны только через классы модели, а не из
экземпляров модели, чтобы обеспечить разделение между 
операциями «уровня таблицы» и операциями «уровня записи».

Тут будет ошибка !

```python
b = Blog(name='Foo', tagline='Bar')
print(b.objects) 

# AttributeError: "Manager isn't accessible via Blog instances."
```

---

**6. Получение всех объектов методом `all()`**

Получение всех записей из таблицы возможно с помощью метода
`all()` используя менеджер класса модели.

Получить все существующие записи из таблицы

```python
all_entries = Entry.objects.all()
```

---

**7. Получение одного обьекта методом `get()`**

`get()` - Метод предназначенный для получения одной, и только
одной записи из таблицы, если записей больше чем одна то 
будет вызвано исключение типа `MultipleObjectsReturned`

Если запись найдена не будет, то будет вызвано исключение 
типа `DoesNotExist`

Получаем одну запись из таблицы, где первичное поле (чаще id)
это 1

```python
one_entry = Entry.objects.get(pk=1)
```

Оба исключения что одно `MultipleObjectsReturned` что другое
`DoesNotExist` хранятся в виде атрибута модели, так что если
хотя бы одно из исключений появляется, то будет отображаться как
`Entry.DoesNotExist` и `Entry.MultipleObjectsReturned`

---

**8. Использование фильтров `filter()`**

`filter()` - Возвращает новые `QuerySet` содержащие объекты,
соответствующие заданным параметрам поиска.

Получим категорию где `id = 3` и по нему найдем все записи в
модели `Women` которые по полю `cat` привязаны к 3-й категории.
```python
category_1 = category.objects.get(pk=3)
womens = Women.objects.filter(cat=cat_1)
```

Также функции поиска `all()` и `filter()` можно комбинировать 
между собой, к примеру можно с начала выбрать все что есть,
и тут же применить фильтр на полученное множество.

```python
category_1 = category.objects.get(pk=1)
womens = Women.objects.all().filter(cat=cat_1)
```

Получить все записи за 2006 год

```python
Entry.objects.filter(pub_date__year=2006)
```

Результатом уточнения `QuerySet` является `QuerySet`,
поэтому уточнения можно объединить в цепочку, то есть 
можно сколько угодно уточнять нашу фильтрацию, к примеру 
следующим образом : 

```python
Entry.objects
    .filter(headline__startswith = 'What')
    .exclude(pub_date__gte = datetime.date.today())
    .filter(pub_date__gte = datetime.date(2005, 1, 30))
```

---

**9. Использование фильтров `exclude()`**

`exclude()` - Возвращает новые `QuerySet` содержащие объекты,
которые не соответствуют заданным параметрам поиска.

Данный метод возвращает все что не удовлетворяет условию.

Получить все записи кроме записей за 2006

```python
Entry.objects.exclude(pub_date__year=2006)
```

---

**10. Ограничение полученных данных `LIMIT` и `OFFSET`**

Для получения определенного количества данных, эквивалентного 
применению `LIMIT` можно делать нарезку массивов.

Например, получаем все записи и из них, изымаем первые 
5 объектов, `но отрицательные индексы не поддерживаются !`

```python
Entry.objects.all()[:5]
```

---

**11. Обновление всех записей методом `update()`**

`update` - Метод обновляет поля в записи, этот метод существует 
только для набора значений, то есть для `QuerySet` то есть 
только для набора таких значений, для конкретного экземпляра
модели такой способ обновления не подойдет.

Получаем все записи из таблицы, и все эти записи обновляем разом.

```python
w_list = Women.objects.all()
w_list.update(cat_id=1)
```

---

**12. Обновление одной записи**

Получаем запись с `id = 2` и заменяем его поле `cat_id` с 1
на 2, экземпляр модели отражает запись в таблице, и изменение
атрибута в модели изменяет соответствующее поле в записи 
таблице, а далее просто сохраняем методом `save()` для 
внесения изменений.

```python
women_list = Women.objects.get(pk=2)
women_list.cat_id = 1
women_list.save()
```

ORM
---

[//]: # (проверка существования данных в кверисете)
[//]: # (QuerySet.exists&#40;&#41;)

ORM (Object-Relation Mapping) - объектно-реляционное отображение, суть 
данной системы заключается в представлении таблиц БД, в качестве классов
программного кода, и инкапсуляции самого кода от конкретной СУБД, это 
своего рода прослойка между кодом и таблицами, для одинаковой работы с
любой СУБД.

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

Чтобы создать новую запись, создадим объект модели, передадим
в объект данные для именованных атрибутов, для сохранения записи
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
метод `create()` этот метод создает объект модели, и тут же 
сохраняет его в таблице.

```python
blog = Blog.objects.create(name='Новый Блог №1', tagline='Все новости блога №1')
```

---

**3. Обновление записи с `ForeignKey`**

Если создаем запись модели `Entry` у которой одно из полей по 
связи 1:M связанно с моделью `Blog` то в это самое поле, которое 
отвечает за связь во `вторичной` таблицы поместим сущность
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

Есть объект модели `Entry` у которого есть поле `authors`
оно связано связью M : M моделью `Author` для того чтобы 
добавить объект модели `Author` в поле `authors` требуется
использовать специальный метод `add()` 

Создаем запись `joe` модели `Author` и при помощи метода 
`add()` добавляем в `entry` что является объектом модели
`Entry`

```python
from blog.models import Author

joe = Author.objects.create(name="Joe")
entry.authors.add(joe)
```

Это добавление одного объекта в для связи, для добавления 
нескольких объектов, просто передаем их все в тот же метод
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

---
Создание записей группой `bulk_create()`
---

**Первый способ.**

Мы можем создавать записи обычным способом, через метод `save()`

```python
category = Category(name='Название') 
category.save()
```
Этот метод подходит, когда нам требуется сделать некоторое промежуточное
действие между созданием объекта модели, и ее реальным сохранением 
в БД, при этом подходе, сработают сигналы `pre_save`, `post_save`

---

**Второй способ.**

Мы можем создавать объекты сразу же, при помощи ORM метода 
`objects.create()`

```python
Category.objects.create(name='Название') 
```

Данный способ работает быстрее, и если требуется только создать один
объект модели, то он хорошо подходит для этого, но он реагирует 
только на сигналы `pre_init`, `post_init`

---

**Третий способ.**

Создание объектов целой группой, сразу одним SQL запросом, это возможно 
используя ORM метод `objects.bulk_create()`

```python
    category_objects = [
        Category(name='Категория-1'),     
        Category(name='Категория-2'),     
        Category(name='Категория-3'),     
    ]

   Category.objects.bulk_create(category_objects)
```

`objects.bulk_create()` - метод принимает список из созданных объектов
модели, но еще не сохраненных, после чего вместе скопом, создает их все 
одним SQL запросом, это позволяет сократить время на обращение к БД.

Этот метод хорош когда требуется создавать сразу группу объектов, но вся 
группа объектов должна быть создана до момента передачи в `bulk_create()`

---

`ignore_conflicts` - параметр который можно передать в `bulk_create`,
он указывает как поступать в ситуациях когда, в метод передано несколько
одинаковых объектов, `True` означает игнорировать повторяющиеся элементы,
и не выводить ошибки.

```python
result = Category.objects.bulk_create(
    category_objects, 
    ignore_conflicts=True
)
```

`result` - возвращаемое значение, будет содержать набор созданных 
объектов, в том числе с ID созданных объектов, но при использовании
аргумента `ignore_conflicts` полученный набор объектов не будет иметь 
атрибутов ID.

---
Приставки в выборки данных менеджером ORM
---

[Почитать про приставки тут](https://djangodoc.ru/3.1/ref/models/querysets/#field-lookups)

Приставки к полям, по которым ведется поиск записей в 
таблице, используются для уточнения запроса, у таких приставок
используется `__` двойное подчеркивание перед приставкой. 

---
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


---
Уникальность Моделей
---

Определив полю модели специальный параметр `unique = True`, это значит
что данное поле, а записи должно быть уникальным для всей таблицы.

```python
class Category(models.Model):
    name = models.CharField(
        verbose_name='Категория', 
        max_length=60, 
        unique=True
    )
```

---
**Обновление всех записей по определенному полю**

Обновлять можно при помощи метода `update` который возвращает 
количество измененных полей.

```python
from .models import category

womens = Women.objects.all()
result = womens.update(cat_id=1)
print(result)

# Вывод
#  2
```

---
Как получать данные из модели `<Model>.objects`
---
Для получения данных из модели в видах, и передачи тих данных в шаблон 
для отображения, импортируем этот класс модели в вид, и получим записи
через создание объектов модели, и вызова специальных методов.

```python
from .models import Question

def index(request):
    all_questions = Question.objects.all()
    context = {'all_questions': all_questions}
    return render(request, 'polls/index.html', context=context)
```

Через специальный атрибут `objects` мы имеем доступ к методам, что
взаимодействуют с ORM, и возвращают результаты SQL запросов.

У каждого полученного объекта модели, по атрибутам самой модели, также 
присутствует еще одно поля, это поле `pk`.

Атрибут `pk` - содержит поле с первичным ключом, этот ключ может иметь 
нестандартное название, отличное от `id` и какое бы не было его название
оно хранится в поле `pk`

`<Model>.objects.all()` - получить все объекты, все записи из таблицы,
что есть в БД.

`<Model>.objects.get(pk=1)` - получить запись объект записи из БД, где
первичное поле равняется `1`

`<Model>.objects.first()` - получить первую запись, с самым маленьким
значением по `id` или `None`

`<Model>.objects.last()` - возвращает последнюю запись из всех что есть 
по `id` или `None` 

`<Model>` - 

---
2 разных способа создания объекта(instance) модели
---

Оба следующих способа создают объект модели, но делают это разными
способами.

Первый способ это создание объекта и явный вызов метода `save()` на
вызов этого метода будут реагировать зарегистрированные сигналы 
`pre_save` и `post_save`

```python
new_category = Category(name='Category №1')
new_category.save()
```

Другой способ это вызов метода `create()` который сразу как создает 
так и сохраняет объект модели, но на него реагируют другие сигналы,
такие как `pre_init` и `post_init`

Использование метода `create()` более предпочтительно, если требуется
создавать большое множество обьектов,

```python
new_category = Category.objects.create()
```

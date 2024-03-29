Непосредственное выполнение SQL запроса
---

**Метод №1**
---

Первый способ, это выполнение полностью сырых запросов, тут
выполняется SQL запрос полностью минуя уровень модели, сделать
это можно при помощи прямого соединения с БД, импортируем :

```python
from django.db import connection
```

По скольку данный метод открывает соединение с БД, его следует
и закрывать, так что следует использовать менеджер контекста:

Следующие 2 метода эквивалентны 
```python
# Через менеджер контекста
with connection.cursor() as cursor:

# Через закрытие соединение в конце работы
c = connection.cursor()
try:
    c.execute(...)
finally:
    c.close()
```

`connection.cursor()` - Метод возвращает объект предоставляющий
доступ к соединению с БД.

`cursor.execute(sql, [params])` - Метод для создания SQL запроса.

`cursor.fetchone()` - Получить первую одну запись из сделанного 
SQL запроса.

`cursor.fetchall()` - Получить все записи из сделанного SQL проса.

Пример с получением всех записей из таблицы :
```python
from django.db import connection

def select_all_from_category():
    """Получить все записи из таблицы women_category"""
    with connection.cursor() as cursor:
        cursor.execute('SELECT * FROM "women_category";')
        row = cursor.fetchall()
    return row
```

В SQL запрос можно передавать параметры через `%s` и передаем 
параметры вторым аргументом, тут можно сделать этот метод, 
методом конкретной модели, таким образом мы можем снабдить модель 
большим количеством методов, для работы конкретно с этой моделью.

```python
from django.db import connection

def my_custom_sql(self):
    """Обновляем поле и получаем его же"""
    with connection.cursor() as cursor:
        cursor.execute("UPDATE bar SET foo = 1 WHERE baz = %s", [self.baz])
        cursor.execute("SELECT foo FROM bar WHERE baz = %s", [self.baz])
        row = cursor.fetchone()
    return row
```

---

**Метод №2**
---

Второй способ, это исполнение SQl запросов и возвращает экземпляр
модели, если первый метод использует абсолютно чистый SQL код,
не привязанный ни к чему, то данный метод вызывается у модели,
исполняет SQL код, и возвращает объекты этой модели.

Этот способ использует метод `raw()` у модели, этот метод 
принимает сырой SQL запрос, исполняет его и возвращает экземпляр
специального класса `django.db.models.query.RawQuerySet` точно 
такой же `RawQuerySet` который возвращает и обычная модель.

Метод `all()` вернет объект типа `QuerySet` а метод `raw()`
вернет экземпляр класса `RawQuerySet`

```python
# QuerySet
womens = Women.objects.all()

# RawQuerySet
womens = Women.objects.raw()
```

К примеру есть такая модель `Women`

```python
class Women(models.Model):
    cat = models.ForeignKey('category', on_delete=models.PROTECT, null=True)
    title = models.CharField(max_length=255)
    content = models.TextField(blank=True)
    time_create = models.DateTimeField(auto_now_add=True)
    is_published = models.BooleanField(default=True)
```

Cоздадим 2 метода, один метод будет получать все записи при
помощи метода `all()` а другой будет получать все записи 
произвольным SQL запросом, с помощью метода `raw()` 

```python
def get_all_women_models_all():
    """Получить все записи методом all()"""
    womens = Women.objects.all()
    print('Метод get_all_women_models_all')
    show_all_women(womens)


def get_all_women_models_raw():
    """Получить все записи произвольным SQL и методом raw()"""
    womens = Women.objects.raw('SELECT * FROM women_women')
    print('Метод get_all_women_models_raw')
    show_all_women(womens)


def show_all_women(womens):
    """Метод для отображения результата"""
    print('womens = ', womens)
    for women in womens:
        print('type(women) = ', type(women))
        print('women = ', women)
        print('women.cat = ', women.cat)
        print('women.title = ', women.title)
        print('women.content = ', women.content)
        print('women.time_create = ', women.time_create)
        print('women.is_published = ', women.is_published)
```

Результат работы метода `raw()` как видим получаем экземпляр класса
`RawQuerySet` с описанием какой именно SQL запрос был выполнен : 

```python
    get_all_women_models_raw()

    # Вывод
    # Метод get_all_women_models_raw
    # womens =  <RawQuerySet: SELECT * FROM women_women>
     
    # type(women) =  <class 'women.models.Women'>
    # women =  women 2
    # women.cat =  Категори 2
    # women.title =  women 2
    # women.content =  women 2 women 2 women 2 women 2 women 2
    # women.time_create =  2021-08-25 03:21:31.263882+00:00
    # women.is_published =  True
    
    # type(women) =  <class 'women.models.Women'>
    # women =  women 1
    # women.cat =  Категори 1
    # women.title =  women 1
    # women.content =  women 1 women 1 women 1 women 1 women 1
    # women.time_create =  2021-08-25 03:21:00.430165+00:00
    # women.is_published =  True
```

Результат работы метода `all()` как видим получаем экземпляр 
класса `QuerySet` в котором будет указано какие именно объекты 
моделей возвращены.

```python
    get_all_women_models_all()

    # Вывод
    # Метод get_all_women_models_all
    # womens =  <QuerySet [<Women: women 2>, <Women: women 1>]>
    
    # type(women) =  <class 'women.models.Women'>
    # women =  women 2
    # women.cat =  Категори 2
    # women.title =  women 2
    # women.content =  women 2 women 2 women 2 women 2 women 2
    # women.time_create =  2021-08-25 03:21:31.263882+00:00
    # women.is_published =  True
    
    # type(women) =  <class 'women.models.Women'>
    # women =  women 1
    # women.cat =  Категори 1
    # women.title =  women 1
    # women.content =  women 1 women 1 women 1 women 1 women 1 
    # women.time_create =  2021-08-25 03:21:00.430165+00:00
    # women.is_published =  True
```

---
Агрегации
---
Эквивалентны агрегационным функциям самого SQL используются так же

```python
from django.db.models.aggregates import Count, Sum, Avg, Max, Min,
```

---
Примеры запросов `ORM`
---

---
**№1 Получение записей из среза по времени за множество дат**
> Обычно для выбора данных за некий период времени, указывают промежуток
> от и до, как получить промежуток от одного промежутка времени до другого
> промежутка времени, из нескольких дат, скажем с 10:00 до 12:00 по времени,
> то есть получить записи за следующие промежутки:
> 
> 2021.05.10 10:00 - 12:00
> 
> 2021.05.11 10:00 - 12:00
> 
> 2021.05.12 10:00 - 12:00
> 
> Сделать это можно с помощью функции `Extract` которая сгруппирует все записи
по часам.

```python
from django.db.models.functions import Extract

_qs = model_class.objects\
    .filter('... тут применяем другие разные фильтры') \
    .filter( dt__gte='2021.05.10', dt__lte='2021.05.12' ) \
    .annotate(hour=Extract('dt', 'hour')) \
    .filter(dt__time__gte='10:00', dt__time__lte='12:00')
```

---
`Q` Объект
---

`Q` объекты, это условные SQL выражения для обработки `queryset` запросов,
 с его помощью можно манипулировать условиями для фильтрации `queryset`
как логическими выражениями в `python`

```python
from django.db.models import Q
from Model import MyModel

qs = MyModel.objects.all()

# Вариант №1 использование обычной фильтрации queryset
qs = qs.filter(name="admin", num__in=[1, 2, 3, 4, 5])

# Вариант №2 использование объекта Q
_query = Q(name="admin", num__in=[1, 2, 3, 4, 5])
qs = qs.filter(_query)
```

Они поддерживают операции

1) `AND` эквивалентен &
2) `OR` эквивалентен |
3) `NOT` эквивалентен ~

Примеры
```python
# Где name="admin" или num входит в список [1, 2, 3, 4, 5]
Q(name="admin") | Q(num__in=[1, 2, 3, 4, 5])
```

```python
# Где name="admin" и num входит в список [1, 2, 3, 4, 5]
Q(name="admin") & Q(num__in=[1, 2, 3, 4, 5])
```

```python
# Где name="admin" и num не входит в список [1, 2, 3, 4, 5]
Q(name="admin") & ~Q(num__in=[1, 2, 3, 4, 5])
```

Иногда может потребоваться пройтись по множеству условий 
и соединить все запросы для фильтрации в один, это можно сделать 
проходя по ряду условий и последовательно соединяя правила для фильтрации.

```python
from django.db.models import Q
from Model import MyModel

num = [1, 2, 3, 4, 5]
_query = Q()

if name == "admin":
    _query |= Q(name="admin")
if payment_id == 5:
    _query |= Q(payment_id=5)
if num:
    _query |= Q(num__in=num)
    
qs = MyModel.objects.filter(_query)
```

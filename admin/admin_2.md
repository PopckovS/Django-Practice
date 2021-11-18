Методы Действия `admin.ModelAdmin`
---
---
Метод `save_model`
---
Метод сохраняет один обьект модели, на основании параметра `change` определяет
добавление это или изменение обьекта.

Переопределение этого метода дает возможность выполнять действия до и после 
создания обьекта. 

Родительский метод :
```python
def save_model(self, request, obj, form, change):
    """Given a model instance save it to the database."""
    obj.save()
```

К примеру можно внести текущего пользователя в обьект как владельца этой 
записи, перегшрузка метода будет выглядеть так :

```python
def save_model(self, request, obj, form, change):
        obj.user = request.user
        super().save_model(request, obj, form, change)
```

---
Метод `delete_model`
---
Удаление обьекта модели.

Родительский метод 
```python
    def delete_model(self, request, obj):
        """Given a model instance delete it from the database."""
        obj.delete()
```

---
Метод `delete_queryset`
---
Этот метод отрабатывает на удаление целого `QuerySet`, реагирует так же 
и на действие администратора по удалению группы обьектов.

Родительский метод  
```python
    def delete_queryset(self, request, queryset):
        """Given a queryset, delete it from the database."""
        queryset.delete()
```

---
Метод `get_ordering`
---
Связан с атрибутом `ordering`

По дефолту это пустой кортеж, который определяется в методе родительского
класса `get_ordering` что бы создать свою сортировку, можно переропределить 
этот метод.

Родительский метод  
```python
def get_ordering(self, request):
    """
    Hook for specifying field ordering.
    """
    return self.ordering or ()
```

Перегружаем родительский метод, если это суперпользователь то сортируем по имени
и рангу, если нет то по названию
```python
def get_ordering(self, request):
    if request.user.is_superuser:
        return ['name', 'rank']
    else:
        return ['name']
```

---
Метод `get_paginator`
---
Связан с атрибутом `paginator`

По дефолту указывается класс `Paginator` в родительском классе `ModelAdmin`
мы моэем создать свой класс пагинации и подменить его, перегрузив метод
`get_paginator`

Дефолтное поведение пагинатора
```python
from django.core.paginator import Paginator

class ModelAdmin(BaseModelAdmin):
    paginator = Paginator

    def get_paginator(self, request, queryset, per_page, orphans=0, allow_empty_first_page=True):
        return self.paginator(queryset, per_page, orphans, allow_empty_first_page)
```

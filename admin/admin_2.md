Методы Действия `admin.ModelAdmin`
---
---
Метод `save_model`
---
Метод сохраняет один объект модели, на основании параметра `change` определяет
добавление это или изменение объекта.

Переопределение этого метода дает возможность выполнять действия до и после 
создания объекта. 

Родительский метод :
```python
def save_model(self, request, obj, form, change):
    """Given a model instance save it to the database."""
    obj.save()
```

К примеру можно внести текущего пользователя в обьект как владельца этой 
записи, перегрузка метода будет выглядеть так :

```python
def save_model(self, request, obj, form, change):
        obj.user = request.user
        super().save_model(request, obj, form, change)
```

---
Метод `delete_model`
---
Удаление объекта модели.

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
и на действие администратора по удалению группы объектов.

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
класса `get_ordering` что бы создать свою сортировку, можно переопределить 
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
мы можем создать свой класс пагинации и подменить его, перегрузив метод
`get_paginator`

Дефолтное поведение пагинатора
```python
from django.core.paginator import Paginator

class ModelAdmin(BaseModelAdmin):
    paginator = Paginator

    def get_paginator(self, request, queryset, per_page, orphans=0, allow_empty_first_page=True):
        return self.paginator(queryset, per_page, orphans, allow_empty_first_page)
```

---
Метод `get_search_results`
---
Возвращает `queryset` и `boolean` значение указывающее может ли набор 
содержать дубликаты, мы можем перегрузить этот метод.

Перегружаем родительский метод
```python
def get_search_results(self, request, queryset, search_term):
    queryset, may_have_duplicates = super().get_search_results(
        request, queryset, search_term,
    )
    return queryset, may_have_duplicates
```

---
Метод `get_urls`
---
Метод `ModelAdmin` возвращает URL адреса, которые будут использоваться для доступа к
объектам этой модели, этот метод расширяет базовые URL адреса.

Родительский метод
```python
def get_urls(self):
    from django.urls import path

    def wrap(view):
        def wrapper(*args, **kwargs):
            return self.admin_site.admin_view(view)(*args, **kwargs)
        wrapper.model_admin = self
        return update_wrapper(wrapper, view)

    info = self.model._meta.app_label, self.model._meta.model_name

    return [
        path('', wrap(self.changelist_view), name='%s_%s_changelist' % info),
        path('add/', wrap(self.add_view), name='%s_%s_add' % info),
        path('<path:object_id>/history/', wrap(self.history_view), name='%s_%s_history' % info),
        path('<path:object_id>/delete/', wrap(self.delete_view), name='%s_%s_delete' % info),
        path('<path:object_id>/change/', wrap(self.change_view), name='%s_%s_change' % info),
        # For backwards compatibility (was the change url before 1.9)
        path('<path:object_id>/', wrap(RedirectView.as_view(
            pattern_name='%s:%s_%s_change' % ((self.admin_site.name,) + info)
        ))),
    ]
```

Добавляем свои собственные URL адреса к дефолтным адресам асса `ModelAdmin`, подобное
расширение адресов может потребоваться для создания своего адреса на наш метод 
обработчик который будет реализовать свою логику, скажем добавление кастомной 
кнопки в админ панель, и реализовать свой обработчик этой логики
```python
from django.conf.urls import url

    def get_urls(self):
        """Добавляем cdjq flhtcc """
        urls = super().get_urls()
        custom_urls = [url('^our_address/$', self.func_worker, name='our_address'), ]
        return custom_urls + urls
```

Другой приме перегрузки метода
```python
from django.conf.urls import url
    
    def get_urls(self):
        """Добавляем свои URL адреса для обработки нашего кастомногопредставленияю."""
        urls = super().get_urls()
        my_urls = [path('my_view/', self.my_view),]
        return my_urls + urls
    
    def my_view(self, request):
        """Собственный обработчик логики, и переопределяем шаблон для отображения."""
        context = dict(
           self.admin_site.each_context(request),
           key=value,
        )
        return TemplateResponse(request, "sometemplate.html", context)
```

По дефолту все шаблоны для админ панели берутся из дефолтной `Django` библиотеки,
которая разворачивается в виртуальном окружении, и во время процесса разработки,
когда `DEBUG=False` мы можем переопределить это поведение, изменив наши шаблоны 
локально в самом приложении, для переопределения админ панель, следует 
переопределить шаблон `admin/base_site.html`

Когда будет использоваться боевой режим `DEBUG=True` то все шаблоны будут браться 
из директории `static` в корневой директории, что бы подтянуть все наши 
переопределенные шаблоны для админки в директорию `static` выполняется команда

     python3 manage.py collectstatic

Если хотим переопределять шаблоны для админ панели, то сделать это можно при
помощи расширения базовых шаблонов.

```html
{% extends "admin/base_site.html" %}
{% block content %}
...
{% endblock %}
```

---
Методы Шаблонов `admin.ModelAdmin`
---

Метод `changelist_view`
---

Метод управляет отображением главной страницей, страницей со списком всех 
объектов подключенной модели в админке, используя это мы можем перегрузить его,
и добавить свои собственные данные контексту, передать их в шаблон и там
отобразить, конечно для этого потребуется и дополнить сам шаблон админки Django.

```python
def changelist_view(self, request, extra_context=None):
    extra_context = extra_context or {}
    extra_context['total'] = self.get_total()
    return super().changelist_view(request, extra_context=extra_context)
```







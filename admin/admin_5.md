Управление порядком приложений и моделей в админке
---

В `Django` мы можем зарегистрировать модели для их подключения в админке, сделать 
это можно с помощью стандартного метода регистрации моделей в админ панели 
`admin.site.register`

```python
from django.contrib import admin
from .models import FirstModel, SecondModel 

class FirstAdmin(admin.ModelAdmin):
    pass

class SecondAdmin(admin.ModelAdmin):
    pass

admin.site.register(FirstModel, FirstAdmin)
admin.site.register(SecondModel, SecondAdmin)
```

Используя данный метод регистрации, под капотом используется класс 
`django.contrib.admin.AdminSite` у данного класса есть метод `get_app_list`
который отвечает за сортировку, с начала он сортирует зарегистрированные приложения
а после сортирует зарегистрированные модели для каждого из приложений, по дефолту
все приложения и модели в них сортируются в алфавитном порядке.
```python
class AdminSite:
    
    def get_app_list(self, request):
        """
        Return a sorted list of all the installed apps that have been
        registered in this site.
        """
        app_dict = self._build_app_dict(request)
    
        # Sort the apps alphabetically.
        app_list = sorted(app_dict.values(), key=lambda x: x['name'].lower())
    
        # Sort the models alphabetically within each app.
        for app in app_list:
            app['models'].sort(key=lambda x: x['name'])
    
        return app_list
```

По дефолту по мимо зарегистрированных приложений, также в сортировке метода
`get_app_list` учавстуют 2 системных приложения `auth`, `sites` для управленяи 
пользователями и сайтами.

Что бы не менять логику стандартного класса, но сделать свою сортировку приложений
и их моделей в админке, мы можем написать свой промежуточный класс, унаследоваться 
им от стандартного класса админки `admin.AdminSite` и переопределить метод создав
ту сортировку которая нам требуется.
```python
from django.contrib import admin


def get_app_priority():
    """В этом порядке приложения будут выведены в админке"""
    return ['creating', 'bigdata']


class MyAdminSite(admin.AdminSite):

    def get_app_list(self, request):
        """Задаем свою сортировку зарегистрированных приложений админке"""
         
        # получаем словарь с приложениями и моделями для них
        app_dict = self._build_app_dict(request)
        
        # сортируем приложения в том порядке в котором они 
        # перечислены в методе get_app_priority
        app_list = [app_dict[app] for app in get_app_priority()]

        return app_list
```
Таким образом мы сортируем наши зарегистрированные приложения в админке, но у каждого
приложения есть модели, порядок сортировки этих моделей в приложении определяется 
в том порядке в котором они регистрируются в файле `admin.py`

Перед регистрацией моделей, переопределяем дефолтный класс модели на созданный нами
```python
admin.site = MyCustomAdminSite()
admin.site.register(FirstModel, FirstAdmin)   # модель по порядку будет первой
admin.site.register(SecondModel, SecondAdmin) # модель по порядку будет второй
```

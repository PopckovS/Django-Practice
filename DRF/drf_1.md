Django Rest Framework (DRF)
---

DRF это фреймворк для создания RESTfull-интерфейсов.

Установка DRF
```
pip3 install djangorestframework
```

Подключаем в список с приложениями, сам `DRF` в `settings.py`

```python
INSTALLED_APPS = [
    ...
    # Add it here
    'rest_framework', 
    ...
]
```

---
Создадим модель для постов, с текстом и картинкой.

```python
from django.db import models

class Post(models.Model):
    """Модель для работы со статьями"""
    title = models.CharField(max_length=255)
    content = models.TextField()
    is_featured = models.BooleanField(default=False)
    image = models.ImageField(upload_to="photos/%Y/%m/%d/", blank=True, verbose_name='Фото')

    def __str__(self):
        return self.title
```

---
Создание сериализатора `serializer`
---

Для хранения данных нашего API, создадим в приложении новую директорию `api`
и будем хранить наш компоненты нашего API в ней.

Создадим в нем, следующие файлы:

`api/serializers.py` - тут будут храниться сериализаторы, которые
позволяют переводить структуру данных или объекта в формат,
который можно сохранить или передать, а впоследствии восстановить
обратно.

`api/views.py` - тут будем хранить виды, которые будут отвечать 
на какой `URL` и какой метод `HTTP` запроса отрабатывать.

Файл `api/serializers.py`
```python
from ..models import Post
from rest_framework import serializers

class PostSerializer(serializers.ModelSerializer):
    """Сериалайзер для модели Post"""
    class Meta:
        model = Post
        fields = ('id', 'title', 'content', 'is_featured', 'image')
```
В сериализаторе `api/serializers.py`создадим класс, который будет
наследоваться от `ModelSerializer`

`ModelSerializer` - класс, который предоставляет удобный ярлык
для создания сериализаторов, которые работают с экземплярами
конкретной модели и наборами запросов.

`model` - Атрибут в котором указываем модель для которой создаем API

`fields` - Указываем кортеж с полями модели которые будут 
сериализованы.

---

Создаем представления `views`
---

`api/views.py` - тут будем хранить представление, она принимает на
вход веб-запрос и возвращает ответ.

```python
from ..models import Post
from . import serializers
from rest_framework import generics, status
from rest_framework.response import Response

class PostListView(generics.ListAPIView):
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer
```

Наша функция представления будет наследоваться от `generics.ListAPIView`

`ListAPIView` - Используется для конечных точек, доступных только
для чтения, для представления коллекции экземпляров модели, 
отрабатывает на метод `GET` то есть отдает данные пользователю.

`queryset` - переменная которая будет хранить в себе данные 
получаемые из модели, она принимает `QuerySet()` то есть на этом 
этапе если мы не хотим что бы какие-либо данные были доступны пользователю,
то здесь с использованием запроса `ORM` мы можем отфильтровать данные.

`serializer_class` - переменная хранит в себе функцию нашего сериализатора, в
котором были указаны поля для сериализации, которые и будут доступны по `API`

---
Создание URL на представление API
---

В нашем приложении, в файле `urls.py` создадим `URL` который будет направлен
на представление.

Как и любое другое представление, представление от API тоже отрабатывает на URL,
для этого используется специальный метод `as_view()` который передаем в вид.

```python
from django.urls import path
from .api import views

urlpatterns = [
     path('list', views.PostListView.as_view(), name=None),
]
```

Теперь если мы перейдем на URL `/list` то отработает метод представления от нашего
API, сработает представление `PostListView` которое наследуется от `ListAPIView`
которое отрабатывает по методу `GET`, метод `GET` в архитектуре `REST` покажет нам все
записи по этой модели, модели к которой мы привязаны. 

---

Таким образом мы создали представление API которое отдает данные на `GET` запрос, 
теперь создадим другие методы, для создания полноценной `CRUD` системы

Создадим представление API которое будут отрабатывать на метод `POST` и тем самым будет
создавать новые сущности в модели, внешне это будет реализовано как отправка формы, 
загрузкой картинки.

```python
from ..models import Post
from . import serializers
from rest_framework import generics, status
from rest_framework.response import Response

class PostCreateView(generics.CreateAPIView):
    """API POST создать новую запись"""
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer

    def create(self, request, *args, **kwargs):
        super(PostCreateView, self).create(request, args, kwargs)
        response = {
            "status_code": status.HTTP_200_OK,
            "message": "Successfully created",
            "result": request.data
        }
        return Response(response)
```

Тут метод `create` будет создавать новую запись по полученному 
`POST` запросу.

---

Добавим представление для получения одной записи, обновления 
записи и ее удаления, для отработки методов `GET` `PATCH` 
`DELETE`

```python
class PostDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer

    def retrieve(self, request, *args, **kwargs):
        """Получить обьект"""
        super(PostDetailView, self).retrieve(request, args, kwargs)
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        data = serializer.data
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully retrieved",
                    "result": data}
        return Response(response)

    def patch(self, request, *args, **kwargs):
        """Обновление"""
        super(PostDetailView, self).patch(request, args, kwargs)
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        data = serializer.data
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully updated",
                    "result": data}
        return Response(response)

    def delete(self, request, *args, **kwargs):
        """Удаление ресурса"""
        super(PostDetailView, self).delete(request, args, kwargs)
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully deleted"}
        return Response(response)
```

Далее обновим наши url

```python
from django.urls import path
from . import views
from .api import views

urlpatterns = [
    path('list', views.PostListView.as_view(), name=None),
    path('create', views.PostCreateView.as_view(), name=None),
    path('<int:pk>', views.PostDetailView.as_view(), name=None)
]
```

Метод `PostListView` принимает `GET` запрос и отдает все что
найдено, при обращении к url:`list`

Метод `PostCreateView` принимает `POST` запрос и создает запись
по обращению на url:`create/`

Метод `PostDetailView` принимает любой из `GET` `PATCH` `DELETE`
методов, и при обращении по url:`15` применяет данный метод к 
записи с этим `id`

Пагинация методом GET и Ленивая загрузка
---

[Почитать про пагинацию](https://sunscrapers.com/blog/the-ultimate-tutorial-for-django-rest-framework-pagination-part-4/)

Когда мы используем метод GET для доступа к API, по URL:`/book/`
то по дефолту получаем все найденные записи, их может быть много.

Тут есть несколько важных моментов, обьектов может быть очень 
много, и на получение данных из БД с последующей сериализацией,
и передачей всех обьектов на фронт может занять много времени.

Для таких ситуация есть механизм называемый `"Ленивая загрузка"`
суть механизма в том что мы получаем только часть обьектов, и 
только когда пользователь совершит действие на сайте, активируем
`JS` скрипт, который обратится к API и получит новую небольшую
партию записей.

`Пагинация API` - при обращении к url:`/book/` методом GET, мы 
дополнительно передам параметры, что отвечают сколько записей
выводить, и с каким отступом.

`Параметры пагинации:`

1) `limit` - где параметр limit, переданный в GET, определяет 
количество элементов которое требуется получить.

2) `offset` - определяет смещение относительно начала списка.

---

В файле `settings.py` добавим настройки для создания пагинации,
тут параметр `PAGE_SIZE` отвечает за то какое количество элементов
отдавать по дефолту.

```python
REST_FRAMEWORK = {
    ....
    'DEFAULT_PAGINATION_CLASS' : 'rest_framework.pagination.LimitOffsetPagination' ,
    'PAGE_SIZE': 100 ,
    ...
}
```

---

**Пример запроса**

Получить 1 единственную самую первую запись:

    http://127.0.0.1:8000/book/?limit=1

Ответ от сервера

    {
        "count": 4,
        "next": "http://127.0.0.1:8000/book/?limit=1&offset=1",
        "previous": null,
        "results": [
                {
                    "id": 1,
                    "name": "Книга-1",
                    "type": "",
                    "status": "",
                    "content": "Привет мир !",
                    "photo": "http://127.0.0.1:8000/media/photos/store/photo_2020-06-28_04-27-44.jpg",
                    "time_create": "2021-09-02T19:04:38.254089Z",
                    "time_update": "2021-09-02T19:04:38.254112Z",
                    "is_published": true
                }
        ]
    }
---

Получение данных для Аутентифицированных пользователей
---



В файле `/store/models.py`
```python
from django.db import models

class Book(models.Model):
    ...
    owner = models.ForeignKey(
        'auth.User', default=True, blank=True, on_delete=models.CASCADE
    )
    ...
```

В файле `/store/views.py`
```python
class BookList(generics.ListAPIView):
    serializer_class = BooksSerializer
    queryset = Book.objects.all()

    def get_queryset(self):
        user = self.request.user
        return Book.objects.filter(owner=user)
        print('='*180)
        print('user : ', user)
        print('='*50)
        print('return Book.objects.filter(owner=user) : ',
             Book.objects.filter(owner=user))
        print('='*180)
```

---
Точка API, 
---

Представми что у нашего API, есть необходимость при обращении `GET` давать одни 
атрибуты обьекта, но при `PATCH` обновлять совершенно другие атрибуты обьекта, 
то есть сериализовать не одни и те же атрибуты обьекта, разные атрибуты в зависимости
от метода обращения, сделать это можно с помощью определения разных сериализаторов.

Создадим 2 сериализатора, что обрабатывают разные поля обьекта
```python
class VpsSerializers(serializers.ModelSerializer):
    """ Сериализатор для модели VpsModel """

    class Meta:
        model = VpsModel
        fields = ['pk', 'cpu', 'ram', 'hdd', 'status']


class VpsSerializerForStatus(serializers.ModelSerializer):
    """ Сериализатор для модели VpsModel, сериализует поля для смены статуса """

    class Meta:
        model = VpsModel
        fields = ['pk', 'status']
```

И определим разные сериализаторы для разных методов, при обращении к одной и той же
сущьности
```python
class VpsRetrieveUpdate(generics.RetrieveUpdateAPIView):
    """Точка API. Создание одной записи, обновление статуса."""
    queryset = VpsModel.objects.all()
    lookup_field = 'uid'

    def retrieve(self, request, *args, **kwargs):
        """Переопределяем род.метод, задаем сериализатор для одной записи"""
        self.serializer_class = VpsSerializers
        return super().retrieve(request, *args, **kwargs)

    def update(self, request, *args, **kwargs):
        """Переопределяем род.метод, задаем сериализатор для обновления статуса"""
        self.serializer_class = VpsSerializerForStatus
        return super().update(request, *args, **kwargs)
```

Теперь при обращении методом `GET` мы получаем сериализованными один набор атрибутов,
а при обновлении, обновляем другой набор атрибутов обьекта.


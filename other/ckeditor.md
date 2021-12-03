Установка html редактора `ckeditor`
---

[Почитать про установку Ckeditor](https://alimuradov.ru/ustanovka-vizualnogo-redaktora-ckeditor-dl/)

Для админки в `Django` когда мы подключаем модели к админке, поля 
в адмике определяются на основе типа поля модели, если поле определено 
как текст `content = models.TextField(blank=True)` то это будет 
просто поле для текста.

Для того чтобы сделать из обычного текстового поля, html редактор с
которым можно будет добавлять стили и теги, есть специальный модуль,
установим его

```
pip3 install django-ckeditor
```

Далее в настройках `settings.py` в списке `INSTALLED_APPS`
добавим наши приложения:

```python
INSTALLED_APPS = [
    ...
    # Подключение html-редактора ckeditor
    'ckeditor',
    'ckeditor_uploader',
]
```

Так же добавляем и другие настройки в этот файл.

```python
# Зависимость визуального редактора
CKEDITOR_JQUERY_URL = 'https://ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js'
CKEDITOR_UPLOAD_PATH = "uploads/"
CKEDITOR_IMAGE_BACKEND = 'pillow'

CKEDITOR_CONFIGS = {
 'default': {
 'toolbar': 'Full',
 'height': 500,
 'width': 900,
 'extraPlugins':'codesnippet',
 },
}

CKEDITOR_UPLOAD_SLUGIFY_FILENAME = False
CKEDITOR_RESTRICT_BY_USER = True
CKEDITOR_BROWSE_SHOW_DIRS = True
```

Добавляем в файл `urls.py` главного ядра приложения
```python
urlpatterns = [
    ...
    
    path(r'^ckeditor/', include('ckeditor_uploader.urls')),
]
```

Теперь в файле моделях `project/models.py` заменяем поля 
`models.TextField()` которое определяло текстовое поле, на 
специальное поле из специального класса `RichTextUploadingField()` 
который мы импортируем из `ckeditor_uploader.fields`

```python
from ckeditor_uploader.fields import RichTextUploadingField

class Model_1(models.Model):
    """Модель"""
    # Другие поля модели ...
    
    # Поле заменено на специальный тип для html редактора-Ckeditor
    content = RichTextUploadingField(config_name='default', blank=True, verbose_name="Текст")
    
    # Старое поле для текста
    # content = models.TextField(blank=True, verbose_name="Текст")

    # Другие поля модели ...
```

После этого зайдя в админку, можно увидеть 

Обычное поле

![](img/ckeditor_1.png)

Поле с ckeditor

![](img/ckeditor_2.png)

Валидация полей модели
---

[ Почитать про валидаторы ](https://djangodoc.ru/3.1/ref/validators/)

**Замечание**
> Существует 2 способа валидации, первый способ это функции валидаторы
> которые можно повесить на поля модели, данный способ позволит 
> проверять на корректность только те данные что приходят в это поле,
> мы не сможем иметь допуск к другим полям формы.
> 
> Так что если требуется создавать уникальную валидацию, где разрешенное 
> значение для одного поля зависит от параметров другого поля, то для этого
> требуется использовать валидацию на уровне самой формы.

---
Функция валидации
---
Валидаторы выполнены в виде функций, эти функции указываются в 
полях модели данные которых мы хотим проверять, в специальном 
атрибуте `validators=[]` можно указывать целый список различных
валидаторов.

По сути валидатор это коллбэк функция, которая вызывается каждый 
раз когда происходит валидация данного поля модели, в качестве
аргумента коллбэк функция принимает пришедшие данные в чистом 
не измененном виде.

Импорт валидаторов :

```python
from django.core.validators import ValidationError
```
    
Исключение `ValidationError` наследуется от `Exception` его можно
удобно использовать для выброса исключения в кастомной функции
валидаторе.

К примеру, при определении поля модели мы можем задать параметр 
`max_length` который ограничивает максимальное число, но такого же
параметра для минимально введенного количества символов нет, 
создадим функцию валидатор которая будет проверять на минимальное
количество символов и бросать исключение если введенное значение
меньше нашей планки.

```python
from django.core.validators import ValidationError

def validate_min_char(name):
    """Проверка поля на минимальное количество символов"""
    if len(name) < 4:
        raise validators.ValidationError(
            'Название {name} должно иметь больше чем {min} символов'.format(
                name=name, min=4
            )
        )
```

Подключаем валидатор к полю модели
```python
class Objects(models.Model):
    """Модель отражающая некий обьект"""

    id = models.AutoField(primary_key=True)
    name = models.CharField(
        verbose_name='Обьект', 
        blank=False, 
        null=False,
        max_length=255, 
        validators=[validators.validate_min_char]
    )
```

---
Валидации универсальны для Моделей и Форм отправки
---

По скольку как Модели так и Формы отправки используют валидаторы,
и сами валидаторы являются функциями коллбэками, то мы можем 
использовать одну и ту же функцию валидатор в проверке как для
Моделей, так и для формы отправки.

```python
from django.db import models
from django import forms

def validate_min_char(name):
    """Проверка поля на минимальное количество символов"""
    if len(name) < 4:
        raise validators.ValidationError(
            'Название {name} должно иметь больше чем {min} символов'.format(
                name=name, min=4
            )
        )

# Можно использовать для Моделей
class MyModel(models.Model):
    even_field = models.IntegerField(validators=[validate_min_char])

# Можно использовать для Форм отправки
class MyForm(forms.Form):
    even_field = forms.IntegerField(validators=[validate_min_char])
```

---
Валидация модели формат и размер на загружаемых файлов.
---

В самом `Django` по дефолт есть валидатор для проверки расширений загружаемых
файлов, `FileExtensionValidator` используем его, и в добавок напишим свой
собственный валидатор для проверки размера файла.

```python
from django.db import models

# Ограничение в 2 MB указанное в байтах
FILE_MAX_SIZE = 2100000

# Директории для загрузки больших и маленьких изображений
BIG_IMG_PATH = "images/img/big/"

# Форматы изображений разрешенных к загрузке
UPLOAD_IMAGE = ['jpg', 'jpeg']


def validate_file_image_max_size(image):
    """Валидатор, проверки размера изображение, не более указанного в настройках"""
    if image.size > FILE_MAX_SIZE:
        raise ValidationError(
            'Файл слишком большой, размер должен быть не больше 2 MB'
        )
    

class Images(models.Model):
    
    # Сообщение ошибки, для недопустимых форматов загружаемых изображений
    big_image_message_error = ', '.join(settings.UPLOAD_IMAGE)
    
    big_image = models.ImageField(
        verbose_name='Файл',
        upload_to=BIG_IMG_PATH, 
        blank=True,                
        null=True,
        validators=[
              FileExtensionValidator(
                  allowed_extensions=UPLOAD_IMAGE,
                  message=f'Файл должен быть одним из: {big_image_message_error}'
              ),
              validate_file_image_max_size,
        ]
    )
```

---
Валидатор регулярных выражений `RegexValidator`
---
`RegexValidator` - Валидатор с проверкой по регулярному выражению. 

- `regex` - задает регулярное выражение для проверки
- `message` - вывод сообщения если текст не соответствует регулярному выражению 

---
Валидация телефонов через регулярные выражения `RegexValidator`
---
Для валидации телефонных номеров в `Django` мы можем использовать
регулярные выражения через стандартный валидатор `RegexValidator`

Пример
```python
# Для перевода текста
from django.utils.translation import ugettext_lazy as _

# импортируем валидатор для регулярных выражений
from django.core.validators import RegexValidator


class MyModel(models.Model):    
    first_name = models.CharField(max_length=150, verbose_name=_('first name'))
    last_name = models.CharField(max_length=150, verbose_name=_('last name'))
    phone_number = models.CharField(
        validators=[
            RegexValidator(
                regex=r'^\+?1?\d{9,15}$',
                message=_("Phone number must be entered in the format: '+999999999'.")
            )
        ], 
        max_length=17, 
        blank=False, 
        verbose_name=_('phone')
    )
```






[//]: # (# TODO)
[//]: # (Формы :)
[//]: # (    from django.contrib.auth.forms import UserCreationForm)
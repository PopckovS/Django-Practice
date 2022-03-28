Подключение к PostgreSQL
---

Для работы с `PostgreSQL` требуется установить модуль `psycopg2`

```
pip3 install psycopg2-binary
```

В файл с настройками `settings.py` устанавливаем параметры для
подключения

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'django',
        'USER': 'user1',
        'PASSWORD': '123',
        'HOST': '127.0.0.1',
        'PORT': '5432',
        'OPTIONS': {
            
            # Время установки подключения
            'connect_timeout': 10,
            
            # Максимальное время выполнения запроса
            'options': '-c statement_timeout=15000ms',
        }
    }
}
```

Если используем настройки через переменные окружения, то они будут 
доступны через специальный метод.

```python
DATABASES = {
    'default': env.db_url()
}
```

Данный метод `env.db_url()` модуля `environ` используется для превращения переменной
окружения с названием `DATABASE_URL` в настройки подключения к БД, но сама переменная
окружения должна быть выполнена в виде `URL` адреса.

```dotenv
DATABASE_URL=postgres://postgres:123@127.0.0.1:5432/db_name
```

---
Опции подключения к БД `OPTIONS`
---
Параметр `OPTIONS` задает специальные параметры для работы с БД :


1) Можно указать что следует работать не с дефолтной схемой `public` а с той 
которую мы укажем, тут указываем работать со схемой `django_schema`
    ```python
        'OPTIONS': {
            # Работа со схемой django_schema
            'options': -c search_path=django_schema
        }
    ```

2) Указываем время соединения, в течении которого приложение будет пытаться соединиться
с БД
    ```python
        'OPTIONS': {
            # Время установки подключения 
            'connect_timeout': 10,
        }
    ```

3) Указываем время максимального исполнения запроса
    ```python
        'OPTIONS': {
            # Максимальное время выполнения запроса 
            'options': '-c statement_timeout=15000ms',
        }
    ```

---
Расширение моделей для работы с `PostGis`
---
В `Django` есть специальный класс для взаимодействия с моделями, 
его мы используем для работы с обычными моделями:

```python
from django.db import models
```

В СУБД `Postgres` можно установить специальное расширение `PostGis` 
для работы с гео-данными, он расширяет стандартный набор типов объектов
`Postgres` своими типами.

Для того чтобы с новыми типами данных можно было работать на уровне 
приложения `Django`, есть специальное расширение, оно использует 
стандартный набор типов данных и дополнительно расширяет их своими
классами для работы с гео данными, вот его импорт

```python
from django.contrib.gis.db import models
```

Его можно использовать как полную замену импорта обычной обычного
импорта моделей.

---
Создание моделей
---
Скажем мы хотим создать приложение `polls` которое будет создавать опрос
пользователей, нам потребуется создать 2 модели в приложении `polls`

1) Модель `Question` будет содержать 2 поля, Вопрос и Дату публикации. 

2) Модель `Choice` будет содержать 2 поля, Текст выбора и Подсчет голосов,
каждый `Choice` связан с `Question`.

В файле `polls/models.py` создадим наши модели, каждая модель будет 
задаваться как класс с именем в соответствии с таблицей в БД.

Тут все взаимодействие происходит через класс `models`, каждая модель 
наследуется от `django.db.models.Model` каждое свойство(поле) данного класса
реализуется как столбец таблицы и как класс наследуемый от `models`.

Каждое поле представлено своим классом:
   
1. `models.CharField` - реализует тип данных `char`
2. `models.DateTimeField` - дата/время `datetime`
3. `models.ForeignKey` - внешний ключ, связь `1:M`
4. `models.IntegerField` - целое число `int`

Обратим внимание, что мы не задаем первичный ключ в каждой из моделей,
он будет создан автоматически, при желании мы можем задать его самостоятельно,
если скажем требуется использовать для идентификации поле типа `uuid`

```python
# импорт модуля для создания моделей
from django.db import models

# модель для вопросов с 2 полями
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

# дель для выборов опросов
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

---
Еще о создании моделей
---

Создадим еще модель с названием `Women`

```python
class Women(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField(blank=True)
    photo = models.ImageField(upload_to="photos/%Y/%m/%d/")
    time_create = models.DateTimeField(auto_now_add=True)
    time_update = models.DateTimeField(auto_now=True)
    is_published = models.BooleanField(default=True)
```

1. `models.DateTimeField` - поле для определения даты/времени. 

```python
    time_create = models.DateTimeField(auto_now_add=True)
    time_update = models.DateTimeField(auto_now=True)
```

`auto_now_add` - этот параметр, указывает внесение даты/времени в момент
создания записи в БД, и больше она меняться не будет, хорошо подходит 
для создания поля указывающего на дату загрузки.

`auto_now` - автоматически задает дату/время, в момент изменения этой 
записи в БД.

2. `models.ImageField` - поле для хранения ссылки на загруженное фото,

```python
photo = models.ImageField(upload_to="photos/%Y/%m/%d/")
```
 
`upload_to` - параметр указывает куда сохранять img, можно указывать
в виде шаблона с подкаталогами. По такому шаблону `/%Y/%m/%d/` 
картинки будут загружаться каждый день в новый каталог.

---

Чтобы дать полю возможность быть пустым, надо дать ему именной 
атрибут `blank = True`

---

У каждого из полей в модели можно определить специальное поле `verbose_name`
именно это поле будет отражаться в полях админки, если оно не определено то 
будет браться просто названия поля.

```python
class python_section(models.Model):
    """Разделы уроков по python"""
    title = models.CharField(max_length=255, verbose_name="Заголовок")
    description = models.TextField(blank=True, verbose_name="Описание")
    photo = models.ImageField(upload_to="photos/%Y/%m/%d/", blank=True, verbose_name="Фото")
    time_create = models.DateTimeField(auto_now_add=True, verbose_name="Дата создания")
    is_published = models.BooleanField(default=True, verbose_name="Опубликовано")
```





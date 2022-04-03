Django + Celery Для периодических задач
---
---
**Celery** - нужен для запуска задач в отдельном рабочем
процессе (worker), что позволяет немедленно отправить HTTP-ответ
пользователю в веб-процессе (даже если задача в рабочем процессе
все еще выполняется). Цикл обработки запроса не будет заблокирован,
что повысит качество взаимодействия с пользователем.

**worker** - отдельный рабочий процесс, который берется за 
выполнение некой задачи в фоновом режиме. 

Варианты использования:

- Обработка отправки 10 электронных писем, В данном случае Celery
может организовать отправку писем в фоновом режиме, что в свою
очередь позволит вернуть HTTP-ответ пользователю без ожидания.

- Необходимо делать что-то периодически, например, генерировать
ежедневный отчет, очищать данные истекшей сессии.

Сельдерей требует решения для отправки и получения сообщений,
обычно это осуществляется в виде отдельной службы, называемой
брокером сообщений.

Существует 2 брокера сообщений для работы с Celery:

1. `Celery` 
2. `RQ (Redis Queue)`

Celery я работы использует свои таблицы в Бд, так что надо выполнить
миграции перед его использованием.

---
Брокеры сообщений.
---

Брокер сообщений — это хранилище, которое играет роль транспорта
между производителем и потребителем. Из документации Celery
рекомендуемым брокером является RabbitMQ, потому что он поддерживает
AMQP (расширенный протокол очереди сообщений).

Так как во многих случаях нам не нужно использовать AMQP, другой 
диспетчер очереди, такой как Redis, также подойдет. 

Бэкенд результатов — это хранилище, которое содержит информацию
о результатах выполнения Celery-задач и о возникших ошибках.

Брокеры сообщений используемые Celery:

1. Redis
2. RabbitMQ

---

По сути Брокер это БД в оперативной памяти, что позволяет хранить
сообщения между приложениями, используется не обычная Бд типа 
`PostgreSQL`, а БД в оперативной памяти, такие как Redis, RabbitMQ.

Чтобы удобно следить за выполнением задач, какие задачи запущены 
или сломаны, можно использовать как терминал, так и специальное
приложение `flower`

Таски - это к примеру функции которые выполняют некоторые задачи,
у тасков можно настраивать приоритеты, какие выполнять раньше 
других, таски можно запускать как в ручную, так и в определенный
интервал времени.

Работая с тасками, нельзя использовать ORM самого джанго внутри
тасков.

`Flower` - можно запускать и в браузере, с красивым отображением
всех воркеров, и всех работающих тасков.

---
Redis в системе Linux
---

Стандартно `Redis` работает на `127.0.0.1:6379` 

Установка Redis 

    sudo apt install redis-server

Запуск Redis и добавление в автозагрузку

    sudo systemctl start redis-server
    sudo systemctl enable redis-server

Узнать версию 

    redis-server --version

Проверить, состояние сервера

    service redis status

Остановка сервера

    service redis stop

Запуск сервера

    service redis start

---
flower
---

Flower – это легкий веб-инструмент для мониторинга Celery в 
режиме реального времени. Вы можете отслеживать запущенные
задания, увеличивать или уменьшать пул воркеров, отображать
графики и статистику.

---
Установка Celery 
---

Установка пакетов для работы `Celery`

    pip3 install Celery

    pip3 install celery==5.0.5 redis==3.5.3 flower==0.9.7

Или установка `Celery` совместно с другими пакетами для работы.

Для использования `Redis` в качестве транспорта сообщений 
или в качестве бэкэнда:

    pip3 install "celery[redis]"

Или в место `Redis` используем `RabbitMQ` 

    pip3 install "celery[librabbitmq]"

---
Настройка `Celery`
---

Создадим приложение `TimeOut` в настройках определим константы для
работы приложения :

`TimeOut/settings.py`
```python
# ===========================
# CELERY
# ===========================

REDIS_HOST = '127.0.0.1'
REDIS_PORT = 6379

CELERY_BROKER_URL = f'redis://{REDIS_HOST}:{REDIS_PORT}/0'
CELERY_RESULT_BACKEND = CELERY_BROKER_URL

CELERY_ACCEPT_CONTENT = ["json"]
CELERY_TASK_SERIALIZER = "json"
CELERY_RESULT_SERIALIZER = "json"

CELERY_EAGER_PROPAGATES_EXCEPTIONS = True
CELERY_TASK_TIME_LIMIT = 30 * 60

CELERY_BEAT_SCHEDULER = "django_celery_beat.schedulers:DatabaseScheduler"
```

---
Настройка Celery
---

1. Отключает отображение задач Celery, задачи выполняются, но мы этого
не видим, в сервисе `Flower` таски тоже не будут отображаться 

    ```python
    CELERY_TASK_ALWAYS_EAGER = True
    ```

2. Если внутри таска появляется исключение, то оно будет выбрасываться
в основной поток программы
    
    ```python
    CELERY_TASK_EAGER_PROPAGATES = True
    ```

3. Установка временной зоны

    ```python
    CELERY_TIMEZONE = TIME_ZONE
    ```


4. Указываем что использовать как брокер сообщений, что как бэкенд 
результатов, в данном случае используем только `Redis` как то и 
другое

    ```python
        CELERY_BROKER_URL = 'redis://127.0.0.1:6379/0'
        CELERY_RESULT_BACKEND = CELERY_BROKER_URL
    ```

5. Сериализация данных, приходящих в таск и отдаваемых обратно

    ```python
        CELERY_ACCEPT_CONTENT = ["json"]
        CELERY_TASK_SERIALIZER = "json"
        CELERY_RESULT_SERIALIZER = "json"
    ```

6. Максимальное время на выполнение таска, по дефолту безграничное, если
таск не выполняется за это время, тоон будет убит и заменен на новый,
время указывается в секундах

    ```python
        # 10 минут
        CELERY_TASK_TIME_LIMIT = 10 * 60
    ```

    [Почитать тут](http://docs.celeryproject.org/en/latest/userguide/configuration.html#task-soft-time-limit)


7. Если задача предусматривает быстрое выполнение, то мы можем установить
мягкое ограничение по времени в секундах, если таск превышает это время 
выполнения, то генерируется специальное иcключение, оно ни на что не 
влияет и служит в качестве оповещения, мы можем его перехватить и
решить остановить таск досрочно или нет.

    ```python
    CELERY_TASK_SOFT_TIME_LIMIT = 60
    ```

8. Используя `Celery` в качестве `crone` для периодического выполнения 
задач, тут указывается класс который будет отвечать за планирование
задач

    ```python
    CELERY_BEAT_SCHEDULER = "django_celery_beat.schedulers:DatabaseScheduler"
    ```
   
---
Создание Celery задач
---

Так же для функционирования `Celery` потребуется создать экземпляр его
приложения, создадим файл `TimeOut/celery.py` и определим в нем
приложение `Celery`

Файл `TimeOut/celery.py` :

```python
import os
from celery import Celery
from celery.schedules import crontab

# set the default Django settings module for the 'celery' program.
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "TimeOut.settings")

app = Celery("TimeOut")

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
# - namespace='CELERY' means all celery-related configuration keys
#   should have a `CELERY_` prefix.
app.config_from_object("django.conf:settings", namespace="CELERY")

# Load task modules from all registered Django app configs.
app.autodiscover_tasks()

# ===========
# Периодические задачи
# ===========

# Таким образом мы инициализируем переодические задачи
app.conf.beat_schedule = {
    'send-report-every-single-minute': {
        'task': 'app.tasks.post_counter_update', # регистрируем функцию для периодического запука
        'schedule': crontab(),  # change to `crontab(minute=0, hour=0)` if you want it to run daily at midnight
    },
}
```

`Celery` работает по той же идеи модульности приложений, что и сам
`Django` в каждом из приложений требуется определить файл `tasks.py`
его название зарезервировано, в нем определим асинхронные задачи :

Создадим приложение `app` и в нем `tasks.py` :

```python
from TimeOut.celery import app
import time
from django.conf import settings
from .models import Post

@app.task
def add(x, y):
    time.sleep(5)
    print(f'x + y = {x + y}')


@app.task
def post_counter_update():
    post = Post.objects.get(pk=1)
    post.view_count += 1
    post.save()
    print('Объект Post pk = %s обновлен, количество просмотров = %s' % (post.pk, post.view_count))
```

Как можно видеть мы импортируем `app` экземпляр приложения `Celery`
и используем его декоратор для пометки функции как асинхронного
действия.

Там где нам требуется вызвать функцию `add` асинхронно мы вызываем
ее специальный метод `delay()`

```python
    add.delay(4, 4)
```

Для работы приложения асинхронно с использованием `Celery` потребуется
открыть еще одну конcоль, и запустить приложение `Celery` 

```
    celery -A TimeOut worker -l debug
    
    или так
    
    celery -A TimeOut worker --loglevel=debug --concurrency=4
```

После этого приложение будет запущено и сможет выполниться асинхронно.



Промежуточное ПО `middleware`
---
---
Промежуточное ПО - это специальная структура для перехвата запросов/ответов в
`Django`, это глобальная система которая перехватывает и обрабатывает, изменяет 
и передает его дальше по стандартному ходу.

`middleware` - работает функционально, где каждый компонент 
обрабатывает определенное поведение. К примеру `AuthenticationMiddleware`
производит связывание пользователя с запросом, таким образом мы можем иметь
доступ к `request.user` в видах .

`middleware` обрабатывается перед и после работы запроса, подобно тому как
сигналы обрабатываются для модели.

---

Модули `middleware` подключается в `settings.py` в списке настроек `MIDDLEWARE`

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    
    # Управление сессиями между запросами
    'django.contrib.sessions.middleware.SessionMiddleware',
    
    # Запрет user-agent, нормализация url, назначение длины запроса
    'django.middleware.common.CommonMiddleware',

    # Обработка Csrf для работы с формами 
    'django.middleware.csrf.CsrfViewMiddleware',
    
    # Связывание пользователей с запросами используя сессии
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

---
Путь от `request` до `response` в `Django`
---

`WSGI` - работает как обработчик всех запросов и ответом, что проходят между
веб-сервером и проектом `Django`, когда пользователь делает запрос к приложению
то создается экземпляр обработчика `WSGI` далее этот обработчик:

1. Получает настройки `settings.py` из `Django` и классы для выброса исключений
2. Получает все промежуточные классы которые указаны в `MIDDLEWARES` настроек
`settings.py`
3. Создает четыре списка методов, которые обрабатывают запрос, представление,
ответ и исключения
4. Перебирает методы запроса, выполняя их по порядку, это уровень `Request Middlewares`
5. Определяет запрошенный URL
6. Проходит через каждый из методов обработки представления
7. Вызывает функцию отображения (к примеру рендеринг шаблона)
8. Обрабатывает любые методы исключения
9. Проходит через каждый из методов ответа, в обратном порядке тому как выполнял
методы запроса, это уровень `Response Middlewares`
10. Отдает ответ веб-серверу

Та же самая последовательность действий:

1. `Request Middlewares` - принимает запрос пользователя и прогоняет его через набор
промежуточного ПО, по порядку из `MIDDLEWARES`, через аутентификацию, авторизацию,
сессии и те промежуточные классы которые мы можем написать самостоятельно. 
2. `URL Router (URL Dispatcher)` - маршрутизация URL, находит путь к представлениям
3. `Views` - представления, выполняет некоторые действия в себе, бизнес логика.
4. `Context Processors` - контекстный процессор
5. `Template Renderers` - рендеринг шаблона
6. `Response Middlewares` - прогоняет ответ для пользователя через набор промежуточного
ПО, в обратном порядке, изменяет информацию заголовков или тела ответа, и отдает
ее клиенту.

Та же самая последовательность действий:

    HttpRequest
        
        SecurityMiddleware
            SessionMiddleware
                CommonMiddleware
                    CsrfViewMiddleware
                        AuthenticationMiddleware
                            MessageMiddleware
                                XFrameOptionsMiddleware

                                    URL Router
                                    Views
                                    Context Processors
                                    Template Renderers

                                XFrameOptionsMiddleware
                            SecurityMiddleware
                        SessionMiddleware
                    CommonMiddleware
                CsrfViewMiddleware
            AuthenticationMiddleware
        MessageMiddleware

    HttpResponse

---
`Middleware`
---
`Middleware` - должен наследоваться от `django.utils.deprecation.MiddlewareMixin`
и определять ряд методов, эти методы будут вызваны при обработке запросов.

`process_request` - метод срабатывает на этапе запроса, либо возвращает `None` и 
тогда процесс выполнения промежуточного ПО продолжается дальше, либо при 
необходимости, уже на этом этапе мы можем вернуть от сюда объект `HttpResponse`.
Если мы отдаем `HttpResponse` то веся цепочка выполнения промежуточного ПО,
начинает сразу обратную цепочку вызовов, и вызывает в работу `process_response`.

Все методы `process_request` всех промежуточных ПО, собраны вместе, и запрос проходит 
их все по порядку, и только после этого определяется URL, и запрос переходит 
представлению.

`process_view` - теперь когда обработчик `WSGI` знает какое представление ему 
вызвать, он снова просматривает списки методов промежуточного ПО, и вызывает 
их методы `process_view` которые должны быть объявлены так

```python
def process_view(request, view_function, view_args, view_kwargs)
```

Эта функция должа возвращать `None` либо `HttpResponse` либо выбросить
исключение.

`process_exception` - если функция представления бросает исключение, то 
обработчик `WSGI` вызывает методы `process_exception` которые выполняются 
в обратном порядке от последнего `Middleware` что указан в настройках `settings.py`
до первого

`process_response` - после того как пройден этап представления, у нас появляется 
объект ответа `HttpResponse`, список методов `process_response` передают 
объект ответа друг от друга на верх в обратном порядке, как это было с запросом,
и в конечном счете отдают ответ пользователю.

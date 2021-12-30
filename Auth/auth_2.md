Дефолтная система рег-ции и аутент-ции самого `Django`
---
Дефолтная система устанавливается сразу же, с настройками в `settings.py`

```python
INSTALLED_APPS = [
# Фреймворк аутентификации и моделей по умолчанию.
'django.contrib.auth',  

# Django контент-типовая система (даёт разрешения, связанные с моделями).
'django.contrib.contenttypes',  
]

MIDDLEWARE = [
# Управление сессиями между запросами
'django.contrib.sessions.middleware.SessionMiddleware',  

# Связывает пользователей, использующих сессии м запросами.
'django.contrib.auth.middleware.AuthenticationMiddleware',     
]
```
Все эти приложения работают с моделями в `Django` эти модели поставляются 
вместе с приложением `django.contrib.auth` вот они :


1. `User` модель с пользователями, в БД представлена как `auth_user`
2. `Permission` модель с правами доступа, в БД представлена как `auth_permission `
3. `Group` модель с группами пользователей, в БД представлена как `auth_group`

Помимо этих 3 моделей так же между ними создаются 3 связующих модели

4. `auth_group_permissions`
5. `auth_user_groups` - связь M:M между пользователями и группами
6. `auth_user_user_permissions`

Вся работа с пользователями и их правами происходит между этими моделями.


---
Формы самого `Django`
---
`Django` предоставляет набор стандартных форм, для работы с пользователями,
все они находятся в модуле `django.contrib.auth.forms` нас интересуют 
только 2 формы:

1. `UserCreationForm` - форма для регистрации новых пользователей
2. `AuthenticationForm` - форма аутентификации пользователей

---
Как регистрировать пользователей
---
Представим что нам требуется зарегистрировать нового пользователя, для 
этого создадим представление которое будет отрабатывать страницу сайта
с формой регистрации пользователей. 

Представление можно создавать не только из методов, но и из классов,
представление на основе классов должно наследоваться от класса
`django.views.generic.CreateView`

Задаем атрибуты для класса представления :

1. `form_class` - форма которая будет подключена к этому представлению,

2. `template_name` - путь к шаблону, который будет отрабатывать это представление

3. `success_url` - `URL` к представлению на которое пользователь будет 
перенаправлен при успешной регистрации

Для дефолтной регистрации пользователей есть специальный класс
`django.contrib.auth.forms.UserCreationForm` именно его и используем как 
форму для атрибута `form_class` 

Для теста этой регистрации создадим новое приложение `test_auth`

Создаем представления `test_auth/views.py`
```python
from django.http import HttpResponse

from django.contrib.auth.forms import UserCreationForm
from django.views.generic import CreateView
from django.urls import reverse_lazy


class RegisterUser(CreateView):
    """Класс для представления страницы РЕГИСТРАЦИИ пользователей."""
    form_class = UserCreationForm
    template_name = 'test_auth/register.html'
    success_url = reverse_lazy('register_page')


def register_page(request):
    data = 'Страница РЕГИСТРАЦИИ'
    return HttpResponse(data)
```

Определяем пути для приложения `test_auth/urls.py`
```python
from django.urls import path

from .views import register_page, RegisterUser

urlpatterns = [
    path('register', RegisterUser.as_view(), name='register'),
    path('register_page', register_page, name='register_page'),
]
```

Создадим `html` в приложении `templates/test_auth/register.html` для 
вывода нашей формы регистрации пользователей.
```html
<h1>Регистрация</h1>

<!-- форма отправки -->
<form method="post">
    
    <!-- токен для уникальности этого POST проса к серверу -->
    {% csrf_token %}
    
    <!-- вывод полей формы и ошибок если они есть -->
    {{ form.as_p }}

    <!-- кнопка отправки -->
    <button type="submit">Регистрация</button>
    
</form>
```

---
Как аутентифицировать пользователей
---
Для аутентификации(логировании) пользователя под своим профилем, можно 
использовать все тот же механизм, только класс представление наследуем
от класса `django.views.generic.LoginView` который уже содержит в себе
логику аутентификации пользователей. 

В качестве формы для аутентификации используем `AuthenticationForm`
так же будем перенаправлять пользователя на страницу, с выводом 
информации об аутентификации.

Помним что каждый запрос связан с пользователем, либо с аутентифицированным
пользователем, либо с анонимным. Получить пользователя, что связан с запросом 
можно через атрибут `request.user`

```python
from django.http import HttpResponse

from django.contrib.auth.forms import AuthenticationForm
from django.views.generic import CreateView
from django.urls import reverse_lazy

# Класс представление для авторизации пользователей
from django.contrib.auth.views import LoginView


class AuthenticationUser(LoginView):
    """Класс для представления страницы АВТОРИЗАЦИИ пользователей."""
    form_class = AuthenticationForm
    template_name = 'test_auth/authentication.html'
    success_url = reverse_lazy('auth_page')


def auth_page(request):
    data = 'Страница АВТОРИЗАЦИИ</br>'
    data += f'request.user = {request.user}</br>'
    data += f'request.user.is_authenticated = {request.user.is_authenticated}</br>'
    return HttpResponse(data)
```

Определяем пути для приложения `test_auth/urls.py`
```python
from django.urls import path

from .views import auth_page, AuthenticationUser

urlpatterns = [
    path('auth', AuthenticationUser.as_view(), name='auth'),
    path('auth_page', auth_page, name='auth_page'),
]
```

---
Страница выхода
---






---
Разграничение правд доступа к Моделям
---


Создание Миграции
---
---

Миграции - это способ, которым Django распространяет изменения сделанные 
с моделями, на схемы базы данных.

---
Начальная Миграция
---

В настройке `INSTALLED_APPS` по дефолту определены приложения для 
стандартной работы сайта, также мы и сами создаем и подключаем свои 
приложения, все эти приложения имеют свои модели, для того чтобы создать 
все эти таблицы, требуется запустить команду:

```
python3 manage.py migrate
```

`migrate` - команда просматривает все подключенные приложения и анализирует
модели в приложениях, если находит еще не примененные миграции то запускает 
их.

---

Создание миграции для новой модели, когда мы создаем новые модели, они 
еще не отслеживаются, для создания миграции на новую модель, используется
следующая команда.

    ./manage.py makemigrations

В каждом приложении Django, есть директория `migrations` которая сама по 
себе, является пакетом python, каждый раз когда мы создаем миграции, в этой
директории создается новый файл, это файл миграций, его название задается 
специальным образом, с указанием номера миграции и краткого описания 
изменений, к примеру так: `0002_alter_message_geo_location.py`

Так же есть и специальный файл `0001_initial.py` который генерируется 
первым.

---

При создании файла с миграцией, генерируется новый класс с различными
атрибутами, к примеру такой класс, если модели наследуются от класса
`models.Models` то миграции от класса `migrations.Migration`

Атрибут `initial` указывает, является ли эта миграция первой для приложения 
или нет.

Атрибут `dependencies = []` это зависимость, от другой миграции, ибо нельзя
менять какие-либо поля в таблице если эта таблица не была создана.

Атрибут `operations = []` это список с объектами, где каждый объект, это 
определенный тип, к примеру такие:

`migrations.CreateModel` - создание модели

`migrations.RenameField` - переименование полей модели

`migrations.AddField` - добавить поле

```python
from django.db import migrations, models
import django.db.models.deletion

class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='Category',
            fields=[
                ('id', models.BigAutoField(
                                          auto_created=True, 
                                          primary_key=True, 
                                          serialize=False, 
                                          verbose_name='ID'
                                        )),
                ('type', models.CharField(max_length=60, verbose_name='Категория')),
                ('tag', models.CharField(max_length=255, verbose_name='Теги')),
            ],
        ),
        migrations.CreateModel(
            name='Message',
            fields=[
                ('id', models.BigAutoField(auto_created=True, 
                                           primary_key=True, 
                                           serialize=False, 
                                           verbose_name='ID'
                                           )),
                ('type', models.CharField(max_length=60, verbose_name='Вид карточки')),
                ('date_time', models.DateTimeField(verbose_name='Дата и время')),
                ('address', models.CharField(max_length=120, verbose_name='Адрес')),
                ('description', models.TextField(verbose_name='Описание происшествия')),
                ('geo_location', models.CharField(
                                                   max_length=120, 
                                                   null=True, 
                                                   verbose_name='Координаты'
                                               )),
                ('category', models.ForeignKey(
                                                   null=True, 
                                                   on_delete=django.db.models.deletion.SET_NULL, 
                                                   to='parse.category', 
                                                   verbose_name='Категория обращения'
                                               )),
            ],
        ),
    ]
```

---

Миграции можно задать специальное имя вместо генерируемого автоматически

    ./manage.py makemigrations --name changed_my_model your_app_label


---
Создание миграции на модели
---

После того как модели для приложения `polls` созданы, на эти модели 
требуется создать миграции, миграции для моделей именно приложения `polls`
можно создать при помощи следующей команды:

```
python3 manage.py makemigrations polls

# Вывод
# Создана миграция с созданием 2 моделей/таблиц
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

После этого будет создана миграция именно и только для приложения `polls`
и будет помещена в пакет `polls/migrations` сформирована в файл 
`0001_initial.py` в последующем каждая новая миграция, на новую модель или 
изменение старых моделей будет сформирована и положена в новый файл миграции,
с номером по порядку.

---
Применение миграций
---

Применение созданной миграции, можно выполнить следующей командой, эта
команда приведет изменение миграций в реальность для таблиц в БД.

    ./manage.py migrate

---
Откатить миграции
---
Мы можем отменять миграции, скажем, для того чтобы отменить миграцию
для приложения `books` с номером `0003` мы можем выполнить следующую 
команду

    python manage.py migrate books 0002

Будет взято приложение `books` и с уровня миграции `0003` будет возвращены
те состояния, что присутствовали в миграции `0002`

---
Откатить все миграции до начальной
---
Если мы хотим сбросить все миграции для приложения, то можем использовать 
команду `zero`

    python manage.py migrate books zero

---
Фейковые миграции
---

Данная команда осуществляет фейковую миграцию, миграция будет считаться
примененной, но на самом деле никаких изменений происходить не будет, 
никаких операторов SQL применено не будет, будет только создана пометка
в специальной таблице в БД для отслеживания миграций, что такая миграция
была применена.

    ./manage.py migrate --fake

---
Где применимы фейковые миграции ?
---
Мы не можем взаимодействовать с таблицами в БД, если не имеем их модели,
таким образом если мы хотим работать с таблицей то нам нужно описать ее 
модель, при создании модели и попытке применить миграцию на ее создание,
Django автоматически проверяет не существует ли эта таблица уже, и если
она существует то он, выдает сообщение, что не может применить данную 
миграцию.

Таким образом нам требуется применить миграцию, но таблица уже создана, 
к примеру таблица загружена через дамп БД, для таких случаем и используется
фейковая миграция, Django будет считать что миграция применена, без реального
исполнения SQL команд.


---
Просмотр SQL кода, что создан миграцией 
---
Когда миграция на основе новых моделей создана, она еще не применена, то
есть миграция создана и заложена в файл `0001_initial.py` но еще не применена,
и в самой БД таблицы на ее основе не созданы.

Перед запуском миграции на исполнение, можно посмотреть тот `SQL` код
который будет применен этой миграцией, сделать это можно следующим способом.

Используем специальную утилиту `sqlmigrate` по следующему примеру, 
`sqlmigrate <project_name> <number_of_migration>` посмотрим `SQL` 
созданных моделей:

```
python3 manage.py sqlmigrate polls 0001


    BEGIN;
    --
    -- Create model Question
    --
    CREATE TABLE "polls_question" (
      "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
      "question_text" varchar(200) NOT NULL, 
      "pub_date" datetime NOT NULL
     );
    --
    -- Create model Choice
    --
    CREATE TABLE "polls_choice" (
      "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
      "choice_text" varchar(200) NOT NULL,
      "votes" integer NOT NULL,
      "question_id" bigint NOT NULL 
      REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED
     );
    --
    --
    CREATE INDEX 
    "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
    --
    COMMIT;
```

Именно этот `SQL` и будет выполнен при запуске миграции.

Первичные ключи созданы в таблицах автоматически, даже без описания их в 
самих моделях.

В классе моделей мы определяли названия классов с большой буквы, в запросе
видим что Django автоматически переводит название моделей в нижний регистр, и 
добавляет префикс в виде названия приложения, таким образом это позволяет
по первому слову таблицы сразу определить к какому приложению оно относится
`polls_choice` и `polls_question`.

Также при создании связи на уровне СУБД, название поля по которому будет 
создана связь, `question` автоматически получает `id` для указания, что это 
поле `question_id` служит связью.

Помимо просмотр SQL при помощи `sqlmigrate` также есть команда для проверки
ошибок в миграциях, для этого используем команду:

      python3 manage.py check

Если ошибок нет, то в результате получаем ответ:

      System check identified no issues (0 silenced)

---
Посмотреть какие миграции применены, а какие нет
---
Мы можем посмотреть какие из существующих файлов миграций в наших 
приложениях применены к БД, а какие нет, сделать это можно командой

    python3 manage.py showmigrations

В результате получим вывод обо всех найденных в приложениях миграциях,
и вывод были они применены или нет.

```commandline
admin
 [X] 0001_initial
 [X] 0002_logentry_remove_auto_add
 [X] 0003_logentry_add_action_flag_choices
auth
 [X] 0001_initial
 [X] 0002_alter_permission_name_max_length
 [X] 0003_alter_user_email_max_length
 [X] 0004_alter_user_username_opts
 [X] 0005_alter_user_last_login_null
 [X] 0006_require_contenttypes_0002
 [X] 0007_alter_validators_add_error_messages
 [X] 0008_alter_user_username_max_length
 [X] 0009_alter_user_last_name_max_length
 [X] 0010_alter_group_name_max_length
 [ ] 0011_update_proxy_permissions
 [ ] 0012_alter_user_first_name_max_length
contenttypes
 [X] 0001_initial
 [X] 0002_remove_content_type_name
sessions
 [X] 0001_initial
test1
 [X] 0001_initial
 [] 0002_auto_20211114_0523
```

---
Кастомизация миграций
---

---
Чистый SQL в миграциях
---

В миграциях мы применяем изменения в модели на реальные объекты
таблиц в БД, к примеру создав модель в миграции она будет отображена
как метод `migrations.CreateModel()`

Но так же в миграциях можно использовать и чистый SQL запрос, сделать
это можно с помощью метода `migrations.RunSQL()`

```python
from django.db import migrations

class Migration(migrations.Migration):

    dependencies = [
        ('bigdata', '0007'),
    ]

    operations = [
        migrations.RunSQL(
            """
            INSERT INTO public.customer (id, name)
            VALUES 
                (1, 'Первый клиент'), (2, 'Второй клиент') 
            """
        )
    ]
```

Таким образом можно выполнять любой SQL запрос в миграциях.

---
Исполнение `python` крипта в миграциях
---

Существует возможность исполнять `python` скрипт с использованием `ORM`
в миграциях `Django`.

Приме с файлом миграцией.
```python
import django.db.models.deletion
from django.db import migrations, models

# Функция будет исполнена в процессе работы миграции,
def link_to_satellites(apps, schema_editor):
    # Один из способов получения модели БД для работы
    Model_one = apps.get_model('my_app', 'Model_one')
    Model_two = apps.get_model('my_app', 'Model_two')
    # Далее нужная логика работы с БД...
    
    
class Migration(migrations.Migration):

    dependencies = [
        ('images', '0004_add_table'),
    ]

    operations = [
        
        # Вызываем функцию коллбек для исполнения
        migrations.RunPython(link_to_bands),
        
        migrations.AddField(
            model_name='test_model',
            name='name',
            field=models.BooleanField(default=False, verbose_name='Name'),
        ),
    ]
```

В случае если у нас есть потребность в `python` скрипте вызвать исполнение фикстур,
то мы можем это сделать с помощью метода `django.core.management.call_command`

```python
import django.core.validators
from django.db import migrations, models
import django.db.models.deletion
from django.core import management


def exec_fixtures(apps, schema_editor):
    management.call_command('loaddata', 'fixtures/one.json')
    management.call_command('loaddata', 'fixtures/two.json')
```

Вызов команды `management.call_command` аналогичен вызову команды их консоли.

---
Обратные миграции `reverse_sql`
---

[Ссылка на документацию](https://docs.djangoproject.com/en/3.2/ref/migration-operations/#runsql)

Миграции можно как накатывать так и откатить, но это работает только когда сами
миграции сгенерированы самим `Django`, в случае если мы используем в миграциях 
`migrations.RunSQL` то есть используем чистый `SQL` в перемешку с кодом миграций
самого `Django`, то в этом случае откатить миграцию нет возможности, `Django` просто
не знает какой должен быть обратный `SQL` запрос.

Это можно изменить с помощью создания обратной `SQL` миграции, которая указывается в
аттрибуте `reverse_sql` той же команды `migrations.RunSQL`, при совершении отката,
именно этот `SQL` будет использоваться `Django`.

Пример обратной миграции:

```python
class Migration(migrations.Migration):

    dependencies = []

    operations = [
        migrations.RemoveConstraint(
            # ...
        ),
        migrations.AddField(
            # ...
        ),

        migrations.RunSQL(
            # Этот SQL будет выполнен при использовании миграции
            sql=[(
                """
                UPDATE public.test_table
                SET status = 
                  CASE 
                    WHEN status = 'A' THEN 'AA'
                    WHEN status = 'B' THEN 'BB'
                    ELSE status
                  END;
                """
            )],
            
            # Этот SQL будет выполнен при откате миграции 
            reverse_sql=[(
                """
                UPDATE public.test_table
                SET status = 
                  CASE 
                    WHEN status = 'AA' THEN 'A'
                    WHEN status = 'BB' THEN 'B'
                    ELSE status
                  END;
                """
            )]
        ),

        migrations.AlterField(
            # ...
        ),
        migrations.AlterField(
            # ...
        ),
        migrations.AddConstraint(
            # ...
        ),
    ]
```

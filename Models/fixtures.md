Фикстуры
---
---

Выгрузка данных из БД `dumpdata`
---

Фикстура – это файл с сериализированными данными из базы данных,  сути это 
один из вариантов дампа БД.

`dumpdata` - это специальная утилита Django для работы с фикстурами, ее
можно использовать для выгрузки данных их БД, по дефолту фикстуры создаются
в формате `.json`

---

Выгрузить всю данные из всех таблиц  
```python
python3 manage.py dumpdata > fixtures/db.json
```

---

Указываем сделать экспорт всех моделей определенного проекта.

```python
python3 manage.py dumpdata my_project > my_project.json
```


---

Из приложения `my_project` выгрузить все данные из таблицы `my_model`, 
то есть мы указываем проект из которого хотим сделать экспорт, и 
указываем модель этого проекта из которой хотим сделать экспорт.

```python
python3 manage.py dumpdata my_project.my_model > my_model.json
```

---

В дефолтном исполнении фикстура будет выглядеть нечитаемо, что бы
добавить отступы есть специальный атрибут `--indent 2` это сделает
отступ в 2 пробела.

```python
python3 manage.py dumpdata my_project.my_model --indent 2 > my_model.json
```

---
Загрузка данных в БД `loaddata`
---

С помощью утилиты `dumpdata` можно сделать фикстуру, которая будет 
содержать данные взятые из БД.

`loaddata` - это утилита, делает противоположное, она восстанавливает
данные из фикстуры в таблицы БД.


Восстановление всех данных из файла в БД 
```
python3 manage.py loaddata db.json
```

Django сам хранит и знает какой файл фикстуры из какой таблицы какого
приложения был сделан, таким образом при восстановлении данных утилитой
`loaddata` если эта фикстура была сделана из приложения `my_project` и 
из его таблицы `my_model` то именно туда и будут восстановлены данные.
Методы доступные для `QuerySet`
---

`values` - возвращает словарь с ключами в виде столбцов таблицы (атрибут модели),
и значениями в виде данных из записи в таблице.

Получить все записи в таблице в виде словаря, по дефолту возвращает все значения
```python
Blog.objects.values()
# <QuerySet 
# [
#    {'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}
#   
# ]>
```

Если передать названия полей, то будут возвращены только указанные поля
```python
Blog.objects.values('id', 'name')
# <QuerySet 
# [
#     {'id': 1, 'name': 'Beatles Blog'}
# ]>
```

---

`values_list` - возвращает кортежи со значениями атрибутов модели, в том порядке в 
котором они были переданы.

Получить кортежи с данными моделей(записей в таблицах)
```python
Entry.objects.values_list('id', 'headline')
# <QuerySet 
# [
#      (1, 'First entry'),   
#      (2, 'Second entry'),   
# ]>
```

По дефолту возвращается именно кортеж, но если извлекать только одно поле, то 
можно указать специальный атрибут `flat` и он `True` то возвращаемые значения будут
просто набором обьектов.
```python
Entry.objects.values_list('id').order_by('id')
# <QuerySet[(1,), (2,), (3,), ...]>

Entry.objects.values_list('id', flat=True).order_by('id')
# <QuerySet [1, 2, 3, ...]>
```
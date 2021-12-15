Формы отправки
---



---
Токен CSRF 
---
Все формы POST, нацеленные на внутренние URL-адреса, должны
использовать тег шаблона `{% csrf_token %}`

```html
<form action="..." method="post">
    {% csrf_token %}
    
    <!--  тут разные поля формы  -->
    
    <input type="submit" value="Vote">
</form>
```

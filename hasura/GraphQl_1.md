[//]: # ([Почитать №!]&#40;https://webdevblog.ru/sozdanie-api-interfejsa-graphql-s-pomoshhju-django/&#41;)

[//]: # ()
[//]: # ([Почитать №!]&#40;https://github.com/hasura/graphql-engine/blob/master/translations/README.russian.md&#41;)

[//]: # ()
[//]: # ([Почитать №2]&#40;https://temofeev.ru/info/articles/rabota-s-graphql-cherez-hasura-dlya-sistemnykh-analitikov-i-qa/&#41;)

[//]: # (Обучение hasura)

[//]: # ()
[//]: # (    https://medium.com/open-graphql/effortless-real-time-graphql-api-with-serverless-business-logic-running-in-any-cloud-8585e4ed6fa3)

[//]: # (    https://hasura.io/learn/)

[//]: # ()
[//]: # ()
[//]: # (Бизнес логика в hasura)

[//]: # ()
[//]: # (    https://hasura.io/learn/graphql/hasura/custom-business-logic/5-create-event-trigger/)

[//]: # (    https://dev.to/vladimirnovick/different-ways-of-adding-your-business-logic-to-hasura-16d)

---
Что такое `GraphQL`
---

>Если совсем просто то, представьте себе что на основе максимально
>упрощенного синтаксиса `SQL` был создан свой язык и назван `GraphQL`
> и на сервере есть своя точка API, всегда с методом `POST` вместо 
> множества точек с кучей методов как в `REST`. Когда фронту требуются
> данные он используя синтаксис `GraphQL` описывает те данные которые
> ему требуется получить, этот запрос летит на API и там расшифровывается
> в обычный язык SQL, после этот запрос исполняется, а данные в стандартном
> `JSON` формате летят обратно на клиент, совсем грубо, но механизм именно
> такой.

`GraphQL` - это язык запросов для API-интерфейсов, это синтаксис
описывающий как какие конкретно данные требуется получить из БД, 
при обращении к endpoint нашей API системы.

Стандартный подход `REST` предусматривает конкретные endpoint
для получения набора данных, при этом набор получаемых данных строго
фиксируемый, и если нам требуется другой набор данных то нам требуется 
либо изменить логику этого endpoint либо создать новый endpoint, или
получать все данные с этого endpoint и отсекать излишие.

`GraphQL` - позволяет в произвольной форме описывать какие данные 
требуется получить,

Все типы запросов на получение данных в `GraphQL` отправляются
через `POST` в то время как в архитектуре `REST` запросы на 
получение данных происходят методом `GET` и передаются в `URL`
как `get` параметры. 

Является следующим этапом развития архитектуры `API` приложений

    `SOAP` -> `REST` -> `GraphQL`

За счет того что `GraphQL` имеет свои изолированные типы данных, 
он не зависит не от языка программирования ни от типа СУБД, сервер
`GraphQL` может быть развернут на разных языках программирвоания,
и данные можно получать из разных СУБД.

[//]: # (---)

[//]: # (Модуль `Graphene`)

[//]: # (---)

[//]: # (`Graphene` - модуль для работы с `GraphQL` имеет интеграцию с )

[//]: # (`Djnago` позволяет развернуть сервер для обработки запросов`GraphQL`)

---
`GraphQL` как это работает
---
Для работы данного механизма нам требуется создать типы данных и 
схемы.

По сути мы создаем Модели и функции обработчики для этих моделей, 
подобно тому как в `Django` мы описываем модель для конкретной таблицы,
так же и в `GraphQL` мы создаем тип данных для таблицы.

Схемы (функции обработчики) это действия, которые будут выполняться 
и производить некотрые операции с данными тех типов которые в эти
функции переданы.

`GraphQL` во всей этой схеме, описывает то какие типы данных,
какими функциями обрабатывать.

---
Типы данных `GraphQL` 
---

Рассмотрим типы данных :

`ID` - указывает что этот параметр уникальный для этого типа данных

`String` - строка

`Int` - число

`['Другой тип данных']` - в качестве атрибута используется другой 
тип данных, по сути это является отображением связи таблицами в БД

`!` - указывает что этот атрибут обезателен

```text
type Actor {  
  id: ID!
  name: String!
}

type Movie {  
  id: ID!
  title: String!
  actors: [Actor]
  year: Int!
}
```


---
`GraphQL` запросы 
---
`query` - получение данных.

Параметр `query` говорит о том что это запрос, `users` это сущьность к 
которой мы обращаемся, а `fname` и `age` это атрибуты сущьности которые
мы хотим получить:
```text
query {
    users {
        fname
        age
    }
}
```

В ответ получаем `JSON` с обьектом`data` которая будет представляь
из себя ответ из тех и только из тех данных что были запрошены.

```text
data : {
    users [
        {
            "fname": "Joe",
            "age": 23
        },
        {
            "fname": "Betty",
            "age": 29
        }
    ]
}
```

---
`GraphQL` добавление данных
---
`Mutation` - добавление данных, это аналог `POST`, `PUT` в системе
`REST`

Каждый подобный запрос будет связан с функцией обработчиком, параметры
`fname: "Sergio", age: 30` указывают данные для новой записи которую 
требуется создать, а параметр `id` данные что требуется вернуть из 
созданной записи. 

```text
mutation createUser{
    addUser(fname: "Sergio", age: 30) {
        id
    }
}
```

В результате получим
```text
data : {
    addUser : 7
}
```

---
`GraphQL` подписки на получение данных
---
`Subscription` - подписка на получение данных, дял соединения 
используется вебсокеты, таким образом все изменения происходящие 
в БД будут в реальном времени отправлены на клиент.

```text
subscription listenLikes {
    listenLikes {
        fname
        likes
    }
}
```

Означает получать пользователей атрибуты `fname` и `likes` которых
меняются в режиме реального времени.

К примеру когда количество лайков сменится с 776 на 777, то мы 
получим ответ в таком формате.
```text
data: {
    listenLikes: {
        "fname": "Sergio",
        "likes": 777
    }
}
```

---
Запуск hasura в Docker локально
---

Полезные ссылки:
- [ hasura в облаке, для быстрого старта, бесплатно](https://cloud.hasura.io/signup?redirect_url=/?pg=learn-hasura-backend&plcmt=body&tech=default)
- [ Установка и настройка hasura в Docker ](https://hasura.io/docs/latest/graphql/core/getting-started/docker-simple.html)

По такому инструементу как `hasura` есть прекрасный и быстрый учебник
[ Вводный курс по работе с hasura ](https://hasura.io/learn/graphql/hasura/setup/)
советую его изучить, тут же я сделаю кроткую выжимку основной информации
для себя, что бы иметь возможность быстро к ней обратиться.

---
Примеры запросов GraphQL
---

Создание таблицы 

    profiles (
      id SERIAL PRIMARY KEY, -- serial -> auto-incrementing integer
      name TEXT
    )

---

Пример запроса на получение всех данных из таблицы по полям

    query {
      profiles {
        id
        name
      }
    }

    # Пример ответа, помним что GraphQL отдает в виде JSON
    {
      "data": {
        "profiles": [
          {
            "id": 1,
            "name": "Запись №1"
          },
          {
            "id": 2,
            "name": "Запись №2"
          },
          {
            "id": 3,
            "name": "Запись №3"
          }
        ]
      }
    }

---
Мутации в `GraphQL` 
---

Мутации - это механизм `GraphQL` по добавлению новых записей в БД. 

Создадим новую запись в БД с помощью мутации, добавим пользователя 
в таблицу `users` и попросим вернуть количество затронутых строк.

    mutation {
      insert_users(objects:[{id: "1", name:"Sergio"}]) {
        affected_rows
      }
    }

    # Ответ
    {
      "data": {
        "insert_users": {
          "affected_rows": 1
        }
      }
    }

Вставляем данные в таблицу `todos`

    mutation {
      insert_todos(objects:[{title: "My First Todo", user_id: "1"}]) {
        affected_rows
      }
    }


---
Подписки в `GraphQL`
---

Подписки это механизм работающий основанный на подключение через
веб-сокеты (протокол обмена данных через постоянное соединение),
создавая подписку мы тем самым подписываемся на получение изменений
происходящих в БД, когда будут происходить изменения в данных, мы тут 
же будем получать эти изменения через сокеты.

Когда мы создаем подписку в самый первый раз, то мы получаем те 
данные которые уже находятся в таблице, даже если они еще не 
изменялись.

    subscription {
      users {
        id
        name
        created_at
      }
    }

    # ответ, данные старые не измененные
    {
      "data": {
        "users": [
          {
            "id": "1",
            "name": "Sergio",
            "create_at": "2021-12-20T08:55:42.479719+00:00"
          }
        ]
      }
    }


---
Отношения в `GraphQL`
---

`Object Relationship`
`Array Relationship`


---
Запросы через отношения в `GraphQL`
---

Для начала следует создать связь между таблицами на уровне
внешних ключей, далее следует создать отношение между таблицами,
сделать это можно через админ панель `hasure`

Запрос на получение связи можно сделать указав связанную таблицу в 
запросе просто как вложенный обьект.

    query {
        todos {
            id
            title
            user {
                id
                name
            }
        }
    }

Поле `user` не существует в реальной таблице в самой БД, но присутствует
как связь установленая через `hasure` но не присутствует в реальной 
таблице, реальная связь через внешний ключ установлена через поле
`user_id` а поле `user` мы используем только для обращения в `GraphQL`

---
Подписка `hasure` на представления в БД.   
---
Стандартные Views (представления) в `Postgres` тоже можно использовать
в `hasure`, созданные представления на уровне БД можно вызывать
через `hasure`.

Создаем представление которое получает пользователей что были активны
не более 30 секунд назад
```sql
CREATE OR REPLACE VIEW "public"."online_users" AS
    SELECT users.id, users.last_seen
        FROM users
        WHERE (users.last_seen >= (now() - '00:00:30'::interval));
```

Далее, подобно тому как мы подписывались на события изменения данных 
в таблице, так же мы можем подписаться на `views` после этого мы будем
получать информацию о всех пользователях которые были активны за 
последние 30 секунд.

    subscription {
        online_users {
            id
            last_seen
        }
    }

---
Создание отношения на представление
---

[ Создание отношения с представлением ](https://hasura.io/learn/graphql/hasura/data-transformations/1-create-relationship-from-view/)

`GraphQL` позволяет устанавливать связи не только между таблица + таблица
но и между таблица + представление.

Так же `GraphQL` использует единый синтаксис запросов что к таблицам
что к представлениям, имея отношение между представлением и таблицей,
мы можем обращаться за получением пользователей что были выбраны 
представлением.

    query {
        online_users {
            id
            last_seen
            user {
                id
                name
            }
        }
    }

**Это довольно интересный механизм, его механика может позволить
организовать довольно сложную бизнес логику.** 

---
Индивидуальная бизнес-логика
---

1) Действия 
2) Удаленные схемы
3) Триггеры событий

---
Действия
---
Действия - проверка данных, изменение данных и что угодно другое,
подобные действия могут быть `Query Action` или `Mutation Action`

`Query Action` - проверки и изменение данных перед отправкой в БД.

`Mutation Action` - 




# Компонентная архитектура

<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->

## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(user, "Пользователь")

System_Ext(web_site, "Клиентский веб-сайт", "HTML, CSS, JavaScript, React", "Веб-интерфейс")
System_Ext(ios_app, "Мобильное приложение ios", "Switf", "Веб-интерфейс")
System_Ext(android_app, "Мобильное приложение android", "Kotlin", "Веб-интерфейс")

System_Boundary(conference_site, "Социальная сеть") {
   Container(api_gateway, "Api Gateway", "", "Gateway с аутентификаций и авторизацией")
   Container(client_service, "Сервис клиентов", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(wall_service, "Сервис стены", "C++", "Сервис управления публикациями на стене", $tags = "microService") 
   Container(chat_service, "Сервис чатов", "C++", "Сервис управления чатами", $tags = "microService")   
   ContainerDb(client_db, "БД пользователей", "Postgresql", "Хранение данных о пользователях", $tags = "storage")
   ContainerDb(wall_db, "БД поубликаций", "Postgresql", "Хранение данных о публикациях пользователей", $tags = "storage")
   ContainerDb(chat_db, "БД чатов и сообщений", "Cassandra", "Хранение данных о чатах и сообщениях", $tags = "storage")
}

Rel(user, web_site, "Регистрация, просмотр/изменение информации записях на стене, отправка/получения сообщений")
Rel(user, ios_app, "Регистрация, просмотр/изменение информации записях на стене, отправка/получения сообщений")
Rel(user, android_app, "Регистрация, просмотр/изменение информации записях на стене, отправка/получения сообщений")

Rel(web_site, api_gateway, "Запросы")
Rel(android_app, api_gateway, "Запросы")
Rel(ios_app, api_gateway, "Запросы")

Rel(api_gateway, client_service, "Работа с пользователями", "REST")
Rel(api_gateway, wall_service, "Работа с публикациями", "REST")
Rel(api_gateway, chat_service, "Работа с чатами", "REST")

Rel(client_service, client_db, "INSERT/SELECT/UPDATE", "SQL")
Rel(wall_service, wall_db, "INSERT/SELECT/UPDATE", "SQL")
Rel(chat_service, chat_db, "INSERT/SELECT/UPDATE", "СQL")

@enduml
```

## Список компонентов

### Сервис клиентов

**API**:

- Создание нового пользователя
    - входные параметры: login, пароль, имя, фамилия, email, дата рождения
    - выходные параметры: отсутствуют
- Поиск пользователя по логину
    - входные параметры:  login
    - выходные параметры: id, login, имя, фамилия, email, дата рождения
- Поиск пользователя по маске имени и фамилии
    - входные параметры: маска фамилии, маска имени
    - выходные параметры: id, login, имя, фамилия, email, дата рождения

### Сервис стены

**API**:

- Добавление записи на стену
    - Входные параметры: название публикации, текст публикации, список тегов
    - Выходыне параметры: id, название публикации, текст публикации, список тегов
- Получение стены
    - Входные параметры: параметры пагинации
    - Выходные параметры: номер страницы, список публикаций {id, название публикации, текст публикации, список тегов}

### Сервис чатов

**API**:

- Отправка сообщения пользователю
    - Входные параметры: id пользователя, текст сообщения
    - Выходные параметры: отсутствуют
- Получение списка сообщений для пользователя
    - Входные параметры: id пользователя, параметры пагинации
    - Выходные параметры: номер страницы, список сообщений {id польователя, текст сообщения}
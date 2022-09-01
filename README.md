# Проект: yamdb_final
Проект приложения YaMDb с настроенным GitHub Actions workflow.
Приложение YaMDb собирает отзывы пользователей на различные произведения.

![YamDB-app workflow](https://github.com/blackworse8/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

---
## Технологии:

- Python <code><img height="20" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/python/python.png"></code>
- Docker Engine and Docker Compose <code><img height="20" src="https://www.docker.com/wp-content/uploads/2022/03/horizontal-logo-monochromatic-white.png"></code>
- Django <code><img height="20" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/django/django.png"></code>
- Django REST Framework
- Git <code><img height="20" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/git/git.png"></code>
- Linux <code><img height="20" src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/linux/linux.png"></code>

---
## Команды для запуска приложения в контейнерах 🛫

- Установить docker 
- Склонировать репозиторий с github
```
git clone https://github.com/blackworse8/yamdb_final.git
```
- Перейти в папку "infra"
```
cd infra
```
- Выполнить команду для пересборки и запуска контейнеров
```
sudo docker-compose up -d --build 
```
Приложение станет доступно по адресу http://localhost/api/v1/
Информация об api методах приложения YaMDb представлена ниже в разделе "Документация API"

- При необходимости можно создать суперпользователя
```
sudo docker-compose exec web python manage.py createsuperuser
```
- Остановить и удалить контейнеры можно командой
```
sudo docker-compose down -v
```

---
## Заполнение базы данных ☯

- Перейти в папку "infra"
```
cd infra
```
- Восстановить базу данных из дампа
```
sudo docker-compose exec web python manage.py loaddata fixtures.json
```
- Создать резервную копию базы
```
sudo docker-compose exec web python manage.py dumpdata 
--exclude auth.permission --exclude contenttypes > ../api_yamdb/fixtures.json
```

---
## Шаблон наполнения env-файла ㊙ :

- При необходимости можно создать в папке "infra" файл .env и наполнить его переменными окружения
```
включение/выключение режима отладки 
DEBUG=False
```
- настроить переменные для работы с базой данных
```
тип используемой БД. Например, для postgresql: 
DB_ENGINE=django.db.backends.postgresql 
```
```
имя базы данных
DB_NAME=postgres
```
```
логин для подключения к базе данных
POSTGRES_USER=postgres
```
```
пароль для подключения к БД
POSTGRES_PASSWORD=postgres
```
```
название сервиса (контейнера)
DB_HOST=db
```
```
порт для подключения к БД
DB_PORT=5432
```

---
## Документация API 📖
- Посмотреть перечень всех эндпоинтов можно после запуска проекта по адресу:
```
http://localhost/redoc/
```

## Пользовательские роли
- Аноним - может просматривать описания произведений, читать отзывы и комментарии.
- Аутентифицированный пользователь (user) — может читать всё, как и Аноним, может публиковать отзывы и ставить оценки произведениям (фильмам/книгам/песенкам), может комментировать отзывы; может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. Эта роль присваивается по умолчанию каждому новому пользователю.
- Модератор (moderator) — те же права, что и у Аутентифицированного пользователя, плюс право удалять и редактировать любые отзывы и комментарии.
- Администратор (admin) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.
- Суперюзер Django должен всегда обладать правами администратора, пользователя с правами admin. Даже если изменить пользовательскую роль суперюзера — это не лишит его прав администратора. Суперюзер — всегда администратор, но администратор — не обязательно суперюзер.

## Самостоятельная регистрация новых пользователей
1. Пользователь отправляет POST-запрос с параметрами email и username на эндпоинт `/api/v1/auth/signup/`
2. Сервис YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email
3. Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт `/api/v1/auth/token/`, в ответе на запрос ему приходит token (JWT-токен)
4. В результате пользователь получает токен и может работать с API проекта, отправляя этот токен с каждым запросом
5. После регистрации и получения токена пользователь может отправить PATCH-запрос на эндпоинт `/api/v1/users/me/` и заполнить поля в своём профайле (описание полей — в документации)

## Создание пользователя администратором
Пользователя может создать администратор — через админ-зону сайта или через POST-запрос на специальный эндпоинт api/v1/users/ (описание полей запроса для этого случая — в документации). В этот момент письмо с кодом подтверждения пользователю отправлять не нужно.
После этого пользователь должен самостоятельно отправить свой email и username на эндпоинт /api/v1/auth/signup/ , в ответ ему должно прийти письмо с кодом подтверждения.
Далее пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит token (JWT-токен), как и при самостоятельной регистрации.


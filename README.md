#  Foodgram - продуктовый помощник
![workflow](https://github.com/DenisSivko/foodgram-project-react/actions/workflows/foodgram_workflow.yml/badge.svg)

## Описание проекта
На этом сервисе пользователи смогут публиковать рецепты, подписываться на публикации других пользователей, добавлять понравившиеся рецепты в список «Избранное», а перед походом в магазин скачивать сводный список продуктов, необходимых для приготовления одного или нескольких выбранных блюд.

## Описание Workflow
##### Workflow состоит из четырёх шагов:
###### tests
- Проверка кода на соответствие PEP8.
###### Push Docker image to Docker Hub
- Сборка и публикация образа на DockerHub.
###### deploy 
- Автоматический деплой на боевой сервер при пуше в главную ветку.
###### send_massage
- Отправка уведомления в телеграм-чат.

## Подготовка и запуск проекта
##### Клонирование репозитория
Склонируйте репозиторий на локальную машину:
```bash
git clone https://github.com/DenisSivko/foodgram-project-react.git
```

## Установка на удаленном сервере (Ubuntu):
##### Шаг 1. Выполните вход на свой удаленный сервер
Прежде, чем приступать к работе, необходимо выполнить вход на свой удаленный сервер:
```bash
ssh <USERNAME>@<IP_ADDRESS>
```

##### Шаг 2. Установите docker на сервер:
Введите команду:
```bash
sudo apt install docker.io 
```

##### Шаг 3. Установите docker-compose на сервер:
Введите команды:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

##### Шаг 4. Локально отредактируйте файл nginx.conf
Локально отредактируйте файл `infra/nginx.conf` и в строке `server_name` впишите свой IP.

##### Шаг 5. Скопируйте подготовленные файлы из каталога infra:
Скопируйте подготовленные файлы `infra/docker-compose.yml` и `infra/nginx.conf` из вашего проекта на сервер в `home/<ваш_username>/docker-compose.yml` и `home/<ваш_username>/nginx.conf` соответственно.
Введите команду из корневой папки проекта:
```bash
scp docker-compose.yml <username>@<host>:/home/<username>/docker-compose.yml
scp nginx.conf <username>@<host>:/home/<username>/nginx.conf
```

##### Шаг 6. Cоздайте .env файл:
На сервере создайте файл `nano .env` и заполните переменные окружения (или создайте этот файл локально и скопируйте файл по аналогии с предыдущим шагом):
```bash
SECRET_KEY=<SECRET_KEY>
DEBUG=<True/False>

DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
```

##### Шаг 7. Добавьте Secrets:
Для работы с Workflow добавьте в Secrets GitHub переменные окружения для работы:
```bash
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432

DOCKER_PASSWORD=<пароль DockerHub>
DOCKER_USERNAME=<имя пользователя DockerHub>

USER=<username для подключения к серверу>
HOST=<IP сервера>
PASSPHRASE=<пароль для сервера, если он установлен>
SSH_KEY=<ваш SSH ключ (для получения команда: cat ~/.ssh/id_rsa)>

TELEGRAM_TO=<ID своего телеграм-аккаунта>
TELEGRAM_TOKEN=<токен вашего бота>
```

##### Шаг 8. После успешного деплоя:
Зайдите на боевой сервер и выполните команды:

###### На сервере соберите docker-compose:
```bash
sudo docker-compose up -d --build
```

###### Создаем и применяем миграции:
```bash
sudo docker-compose exec backend python manage.py makemigrations --noinput
sudo docker-compose exec backend python manage.py migrate --noinput
```
###### Подгружаем статику
```bash
sudo docker-compose exec backend python manage.py collectstatic --noinput 
```
###### Заполнить базу данных:
```bash
sudo docker-compose exec backend python manage.py loaddata fixtures/ingredients.json
```
###### Создать суперпользователя Django:
```bash
sudo docker-compose exec backend python manage.py createsuperuser
```

##### Шаг 9. Проект запущен:
Проект будет доступен по вашему IP-адресу.

# yamdb_final


[![yamdb_final workflow](https://github.com/QuiShimo/yamdb_final/actions/workflows/main.yaml/badge.svg)](https://github.com/QuiShimo/yamdb_final/actions/workflows/main.yaml)

## Workflow
### Структура
* **tests** - Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest. 
Дальнейшие шаги выполнятся только если push был в ветку master или main.
* **build_and_push_to_docker_hub** - Сборка и доставка докер-образов на Docker Hub
* **deploy** - Автоматический деплой проекта на боевой сервер. Выполняется копирование файлов из репозитория на сервер:
* **send_message** - Отправка уведомления в Telegram
### Подготовка для запуска workflow
1. Создайте и активируйте виртуальное окружение, обновите pip:
    ```
    python3 -m venv venv
    . venv/bin/activate
    python3 -m pip install --upgrade pip
    ```
2. Запустите автоматические тесты:
    ```
    pytest
    ```
3. Отредактируйте файл nginx/default.conf и в строке server_name впишите IP виртуальной машины (сервера).
4. Скопируйте подготовленные файлы docker-compose.yaml и nginx/default.conf из вашего проекта на сервер:
```
scp docker-compose.yaml <username>@<host>/home/<username>/docker-compose.yaml
sudo mkdir nginx
scp default.conf <username>@<host>/home/<username>/nginx/default.conf
```

В репозиторий на Гитхабе добавьте данные в Settings - Secrets - Actions secrets:
* DOCKER_USERNAME - имя пользователя в DockerHub
* DOCKER_PASSWORD - пароль пользователя в DockerHub
* HOST - ip-адрес сервера
* USER - пользователь
* SSH_KEY - приватный ssh-ключ (публичный должен быть на сервере)
* PASSPHRASE - кодовая фраза для ssh-ключа
* DB_ENGINE - django.db.backends.postgresql
* DB_HOST - db
* DB_PORT - 5432
* DB_NAME - postgres (по умолчанию)
* POSTGRES_USER - postgres (по умолчанию)
* POSTGRES_PASSWORD - postgres (по умолчанию)
* SECRET_KEY - секретный ключ приложения django (необходимо чтобы были экранированы или отсутствовали скобки)
* TELEGRAM_TO - id своего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
* TELEGRAM_TOKEN - токен бота (получить токен можно у @BotFather, /token, имя бота)
## Как запустить проект на сервере:
1. Установите Docker и Docker-compose:
    ```
    sudo apt install docker.io
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    ```
2. Создать папку nginx:
    ```
    mkdir nginx
    ```

3. После успешного деплоя собрать статические файлы (статику):
    ```
    docker-compose exec web python manage.py collectstatic --no-input
    ```
4. Применить миграции:
    ```
    docker-compose exec web python manage.py migrate --noinput
    ```
5. Создать суперпользователя:
   ```
   docker-compose exec web python manage.py createsuperuser
   ```
## Документация к API YaMDb
Документация доступна по эндпойнту: http://localhost/redoc/

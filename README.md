# SmartProrab — запуск старого проекта

Это архив старого проекта SmartProrab.

Проект запускается в Docker и включает:

* PHP 7.4
* Apache
* MySQL 5.7
* Caddy
* phpMyAdmin
* готовую копию базы данных

После запуска исходный код проекта и база данных уже будут доступны.

---

## 1. Что необходимо установить

Перед запуском необходимо установить:

### Docker Desktop

Скачать и установить Docker Desktop:

https://www.docker.com/products/docker-desktop/

После установки **запустите Docker Desktop**.

Проверить, что Docker работает, можно в терминале:

```bash
docker --version
```

Должна появиться версия Docker, например:

```text
Docker version 28.x.x
```

Также проверьте Docker Compose:

```bash
docker compose version
```

Должна появиться версия:

```text
Docker Compose version v2.x.x
```

---

# 2. Скачать проект

Скачайте или клонируйте репозиторий.

Если используется Git:

```bash
git clone <АДРЕС_РЕПОЗИТОРИЯ>
```

Например:

```bash
git clone https://github.com/example/smartprorab.git
```

После этого перейдите в папку проекта:

```bash
cd smartprorab
```

> Все следующие команды необходимо выполнять **внутри папки `smartprorab`**.

Проверить текущую папку можно командой:

```bash
pwd
```

---

# 3. Структура проекта

В корне проекта должна находиться примерно такая структура:

```text
smartprorab/
├── README.md
├── docker-compose.yml
├── Caddyfile
│
├── apache/
│   ├── Dockerfile
│   ├── 000-default.conf
│   └── php.ini
│
├── init/
│   └── master_dump.sql.gz
│
└── www/
    └── smartprorab/
        ├── lib2/
        └── www/
            ├── index.php
            └── ...
```

**Важно:** файл

```text
init/master_dump.sql.gz
```

содержит копию базы данных.

---

# 4. Запустить проект

Откройте терминал.

Перейдите в папку проекта:

```bash
cd /путь/до/smartprorab
```

Например, если проект находится в `~/development`:

```bash
cd ~/development/smartprorab
```

Запустите контейнеры:

```bash
docker compose up -d
```

При первом запуске Docker может достаточно долго скачивать образы.

Это нормально.

---

# 5. Проверить запуск

После запуска выполните:

```bash
docker compose ps
```

Все основные контейнеры должны иметь статус:

```text
Up
```

Пример:

```text
NAME           STATUS
mysql57        Up
apache_php     Up
caddy          Up
php_my_admin   Up
```

---

# 6. Открыть приложение

После успешного запуска откройте браузер:

http://smartprorab.localhost

Должно открыться приложение SmartProrab.

---

# 7. Открыть phpMyAdmin

Для просмотра базы данных откройте:

http://localhost:8081

Вход:

```text
Сервер: mysql
Пользователь: master
Пароль: пароль из docker-compose.yml
```

Также можно использовать пользователя `root`.

---

# 8. Если приложение не открывается

Сначала посмотрите состояние контейнеров:

```bash
docker compose ps
```

Затем посмотрите логи приложения:

```bash
docker compose logs web
```

Или в режиме реального времени:

```bash
docker compose logs -f web
```

Чтобы выйти из просмотра логов:

```text
Ctrl + C
```

---

# 9. Посмотреть логи базы данных

Если проблема связана с MySQL:

```bash
docker compose logs mysql
```

В режиме реального времени:

```bash
docker compose logs -f mysql
```

Выход:

```text
Ctrl + C
```

---

# 10. Проверить, что база данных загрузилась

Выполните:

```bash
docker compose exec mysql mysql -umaster -p master -e "SHOW TABLES;"
```

Docker попросит пароль.

После ввода пароля должен появиться список таблиц базы `master`.

---

# 11. Остановить проект

Чтобы остановить контейнеры:

```bash
docker compose down
```

**Данные базы при этом не удаляются.**

После этого проект можно снова запустить:

```bash
docker compose up -d
```

---

# 12. Перезапустить проект

Если нужно просто перезапустить контейнеры:

```bash
docker compose restart
```

---

# 13. Пересобрать PHP/Apache контейнер

Если были изменены:

```text
apache/Dockerfile
apache/php.ini
apache/000-default.conf
```

пересоберите контейнер:

```bash
docker compose up -d --build
```

---

# 14. Полностью пересоздать базу данных

> **ВНИМАНИЕ:** эта команда удалит текущую базу данных Docker и заново создаст её из файла:
>
> `init/master_dump.sql.gz`

Используйте это только если нужно вернуть базу в исходное состояние.

```bash
docker compose down -v
```

Затем:

```bash
docker compose up -d
```

При запуске MySQL снова импортирует:

```text
init/master_dump.sql.gz
```

Первый запуск после удаления базы может занять некоторое время.

---

# 15. Если Docker сообщает, что порт занят

Возможно, на компьютере уже запущен другой MySQL, Apache или другой Docker-контейнер.

Проверить контейнеры:

```bash
docker ps
```

Проверить, что используется порт `3306`:

```bash
lsof -i :3306
```

Порт приложения:

```bash
lsof -i :80
```

Порт phpMyAdmin:

```bash
lsof -i :8081
```

Если какой-то порт занят другим приложением, его необходимо остановить либо изменить порт в `docker-compose.yml`.

---

# 16. Полезные команды

### Посмотреть все контейнеры проекта

```bash
docker compose ps
```

### Посмотреть все логи

```bash
docker compose logs
```

### Логи Apache/PHP

```bash
docker compose logs -f web
```

### Логи MySQL

```bash
docker compose logs -f mysql
```

### Перезапустить

```bash
docker compose restart
```

### Остановить

```bash
docker compose down
```

### Запустить

```bash
docker compose up -d
```

### Запустить с пересборкой

```bash
docker compose up -d --build
```

---

# 17. Краткая инструкция

Если Docker уже установлен, то для обычного запуска достаточно:

### 1. Открыть терминал

### 2. Перейти в папку проекта

```bash
cd /путь/до/smartprorab
```

### 3. Запустить

```bash
docker compose up -d
```

### 4. Открыть браузер

```text
http://smartprorab.localhost
```

### 5. Остановить

```bash
docker compose down
```

---

# 18. Важная информация

Это архив старого проекта.

В `docker-compose.yml` находятся настройки подключения к локальной базе данных.

База данных загружается автоматически из:

```text
init/master_dump.sql.gz
```

Исходный код находится здесь:

```text
www/smartprorab/
```

Публичная директория PHP-приложения:

```text
www/smartprorab/www/
```

Внутри Docker проект доступен Apache по адресу:

```text
/var/www/smartprorab/www
```

PHP подключается к MySQL через Docker-имя сервиса:

```text
mysql
```

а не через:

```text
localhost
```

---

# 19. Если что-то пошло не так

Самая полезная команда для диагностики:

```bash
docker compose ps
```

После неё:

```bash
docker compose logs
```

Если проблему не удалось определить, можно получить полный лог и передать его разработчику:

```bash
docker compose logs > docker.log
```

После этого рядом с `docker-compose.yml` появится файл:

```text
docker.log
```

Его можно передать для диагностики.

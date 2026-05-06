## Dockerfile. PHP

**PHP** — серверный скриптовый язык общего назначения для веб-разработки. Код выполняется на сервере, результат (обычно HTML) отправляется в браузер.

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
php-docker/
├── Dockerfile
└── src/
    └── index.php
```

Создать структуру одной командой:
```shell
mkdir -p php-docker/src && touch php-docker/Dockerfile php-docker/src/index.php && cd php-docker
```

### 2. Содержимое файлов

`src/index.php`:
```php
<!DOCTYPE html>
<html>
<head>
    <title>PHP в Docker</title>
    <meta charset="utf-8">
</head>
<body>
    <h1>Привет из PHP в Docker! 🐳</h1>
    <div>
        <h3>Информация о PHP:</h3>
        <?php
        echo "<p>Версия PHP: " . phpversion() . "</p>";
        echo "<p>Загруженные расширения: " . implode(', ', get_loaded_extensions()) . "</p>";
        ?>
    </div>
</body>
</html>
```

`Dockerfile`:
```dockerfile
FROM php:8.2-apache
RUN docker-php-ext-install pdo_mysql mysqli
COPY src/ /var/www/html/
WORKDIR /var/www/html
EXPOSE 80
```

### 3. Сборка и запуск

Сборка образа (из папки `php-docker`):
```shell
docker build -t php-app .
```

Запуск контейнера:
```shell
docker run -d -p 8081:80 --name my-php-app php-app
```

Проверка: откройте http://localhost:8081

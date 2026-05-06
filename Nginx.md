
## Dockerfile. Статический сайт на Nginx

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

Структура проекта:
```
my-website/
├── Dockerfile
└── index.html
```

Создать структуру одной командой:
```shell
mkdir -p my-website && touch my-website/Dockerfile my-website/index.html && cd my-website
```

Файл `index.html`:
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Мой сайт в Docker</title>
</head>
<body>
    <h1>Привет из контейнера! 🐳</h1>
    <p>Здесь может быть любой русский текст.</p>
</body>
</html>
```

Файл `Dockerfile`:
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

Сборка образа (из папки `my-website`):
```shell
docker build -t my-site .
```

Запуск контейнера с монтированием текущей папки (чтобы изменения в `index.html` сразу отображались на сайте):
```shell
docker run -d -p 8081:80 --name my-site -v "$(pwd)":/usr/share/nginx/html my-site
```
> В PowerShell вместо `$(pwd)` используйте `${PWD}`

Проверка: откройте http://localhost:8081

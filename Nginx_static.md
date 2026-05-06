## Dockerfile. Статический сайт на веб-сервере Nginx

> Никогда в разработке не используйте русские имена файлов и каталогов!

> Никогда в разработке не используйте пробелы и спец.символы в именах файлов и каталогов!

Структура проекта:
```
my-website/
├── Dockerfile
└── index.html
```

В каталоге для Docker-проектов создать одной bash-командой всю структуру для нового приложения:
```shell
mkdir -p my-website && touch my-website/Dockerfile my-website/index.html && cd my-website
```

<img width="678" height="42" alt="изображение" src="https://github.com/user-attachments/assets/682ca959-ebd3-448e-b6f6-3f728d322cd7" />

<img width="318" height="147" alt="изображение" src="https://github.com/user-attachments/assets/25028edc-e69d-4f9c-b867-e6d54ef60ef7" />

Файл `index.html`:

или
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
# Используем официальный легковесный образ Nginx
FROM nginx:alpine
# Копируем наш HTML-файл в стандартную директорию Nginx
COPY index.html /usr/share/nginx/html/index.html
# Открываем порт (документация)
EXPOSE 80
```

В командной строке, находясь в папке `my-website`, выполнить:
```shell
docker build -t my-site .
```
> Флаг `-t` задает имя образа

<img width="887" height="661" alt="изображение" src="https://github.com/user-attachments/assets/662a5bcc-05c1-417d-bcfa-6cd3f42cb62e" />

Создание и запуск контейнера:
```shell
docker run -d -p 8081:80 --name my-site -v "$(pwd)":/usr/share/nginx/html my-site
```

<img width="629" height="85" alt="изображение" src="https://github.com/user-attachments/assets/0cba6d45-603e-4561-9e4a-7fe0f97f03b6" />

Пояснение:
* `-v "$(pwd)":/usr/share/nginx/html` — монтирует вашу текущую папку (где лежит `index.html`) в директорию, откуда **Nginx** берёт файлы.
* `$(pwd)` в **Linux/macOS/Git Bash**; в **PowerShell** используйте `${PWD}`.

Теперь любые изменения в `index.html` на хосте мгновенно отобразятся на сайте (потребуется обновить страницу в браузере).

[Откройте в браузере http://localhost:8081 для проверки](http://localhost:8081)

<img width="560" height="225" alt="изображение" src="https://github.com/user-attachments/assets/291216ef-26c5-4c95-89c4-60ffac760d83" />

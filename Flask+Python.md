Хорошо, вот перефразированный текст обычной строкой, без блока кода. Можете просто выделить и скопировать:

## Dockerfile. Flask + Python (мини-проект)

> Для выполнения этого задания лучше применять WSL/Ubuntu, установленную на вашем Windows-компьютере, либо любой иной десктопный Linux

> Никогда в процессе разработки не давайте файлам и папкам русские имена!

> Никогда в процессе разработки не ставьте пробелы и спецсимволы в названиях файлов и папок!

Элементарный Dockerfile для компактного веб-приложения на Python, написанного с помощью Flask

- Dockerfile — рецепт, по которому собирается Docker-образ
- Flask — микрофреймворк для веб-разработки на языке программирования Python

### 1. Структура проекта
```
simple_flask_app/
├── app.py
├── Dockerfile
└── requirements.txt
```

Находясь в директории для Docker-проектов, одной bash-командой создать всю структуру для нового приложения:

```shell
mkdir -p simple_flask_app && touch simple_flask_app/app.py simple_flask_app/requirements.txt simple_flask_app/Dockerfile simple_flask_app/.dockerignore && cd simple_flask_app
```

<img width="1262" height="55" alt="изображение" src="https://github.com/user-attachments/assets/2903c8fe-5fd2-4aae-954c-8f19b2c77772" />

<img width="295" height="132" alt="изображение" src="https://github.com/user-attachments/assets/705e86c1-cb05-4b2c-ae43-71d0f7f87ff4" />

Общая структура проекта должна иметь такой вид:

```
simple_flask_app/
├── app.py
├── Docker
└── requirements.txt
```

### 2. Содержимое файла requirements.txt:
```
Flask==3.0.0
```
### 3. Содержимое файла Dockerfile:

# Базовый образ – официальный облегчённый Python
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

### 4. Содержимое файла .dockerignore
```shell
__pycache__
*.pyc
.git
.env
```

### 5. Содержимое файла app.py
```py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, Docker! Привет из Flask!"

@app.route('/health')
def health():
    return {"status": "healthy"}, 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```
### 5. Сборка образа

В командной строке, находясь в папке simple_flask_app, выполнить:

```shell
docker build -t my-flask-app .
```
<img width="930" height="552" alt="изображение" src="https://github.com/user-attachments/assets/52473fcc-b55f-4342-b3d4-497ae1b244f6" />

[изображение]

### 6. Создание и запуск контейнера

```shell
docker run -d --name my-running-app -p 8082:5000 my-flask-app
```

<img width="512" height="72" alt="изображение" src="https://github.com/user-attachments/assets/1b4ce997-c643-4e66-a556-f8a3846202cc" />

[изображение]

### 7. Проверка работы

[Откройте браузер и перейдите по адресу http://localhost:8082](http://localhost:8082)

<img width="494" height="101" alt="изображение" src="https://github.com/user-attachments/assets/415c62b3-4c70-4b0b-bbad-daebb28ccc54" />

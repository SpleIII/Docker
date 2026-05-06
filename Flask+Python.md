## Dockerfile. Flask + Python

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

- **Dockerfile** — инструкция для сборки Docker-образа
- **Flask** — микрофреймворк для веб-приложений на Python

### 1. Структура проекта
```
simple_flask_app/
├── app.py
├── Dockerfile
├── requirements.txt
└── .dockerignore
```

Создать структуру одной командой:
```shell
mkdir -p simple_flask_app && touch simple_flask_app/app.py simple_flask_app/requirements.txt simple_flask_app/Dockerfile simple_flask_app/.dockerignore && cd simple_flask_app
```

### 2. Содержимое файлов

`requirements.txt`:
```
Flask==3.0.0
```

`Dockerfile`:
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

`.dockerignore`:
```
__pycache__
*.pyc
.git
.env
```

`app.py`:
```python
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

### 3. Сборка и запуск

Сборка образа (из папки `simple_flask_app`):
```shell
docker build -t my-flask-app .
```

Запуск контейнера:
```shell
docker run -d --name my-running-app -p 8082:5000 my-flask-app
```

Проверка: откройте http://localhost:8082

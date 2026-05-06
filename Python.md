## Dockerfile. Простое приложение на Python

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
my-python-app/
├── Dockerfile
└── app.py
```

Создать структуру одной командой:
```shell
mkdir -p my-python-app && touch my-python-app/Dockerfile my-python-app/app.py && cd my-python-app
```

### 2. Содержимое файла `Dockerfile`
```dockerfile
FROM python:3-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

### 3. Содержимое файла `app.py`
```python
print("Hello from Python in Docker! 🐍")
```

### 4. Сборка и запуск

Сборка образа (из папки `my-python-app`):
```shell
docker build -t my-python-app .
```

Запуск контейнера (с автоматическим удалением после завершения):
```shell
docker run --rm my-python-app
```

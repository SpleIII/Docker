## Dockerfile. Приложение на Pascal

**Pascal** — процедурный язык программирования, созданный в 1970 году как учебный язык для обучения структурному программированию.

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
pascal-app/
├── Dockerfile
└── hello.pas
```

Создать структуру одной командой:
```shell
mkdir -p pascal-app && touch pascal-app/Dockerfile pascal-app/hello.pas && cd pascal-app
```

### 2. Содержимое файлов

`hello.pas`:
```pascal
program Hello;
begin
  Writeln('Hello from Pascal in Docker! 🐳');
end.
```

`Dockerfile`:
```dockerfile
FROM primeimages/freepascal:3.2.2
WORKDIR /app
COPY hello.pas .
RUN fpc hello.pas
CMD ["./hello"]
```

### 3. Сборка и запуск

Сборка образа (из папки `pascal-app`):
```shell
docker build -t pascal-app .
```

Запуск контейнера:
```shell
docker run --rm pascal-app
```

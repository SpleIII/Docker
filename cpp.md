## Dockerfile. Hello, World на C++

**C++** — компилируемый, статически типизированный язык общего назначения, сочетающий свойства высокоуровневых и низкоуровневых языков.

> Используйте WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
cpp-docker/
├── Dockerfile
└── hello.cpp
```

Создать структуру одной командой:
```shell
mkdir -p cpp-docker && touch cpp-docker/Dockerfile cpp-docker/hello.cpp && cd cpp-docker
```

### 2. Содержимое файлов

`hello.cpp`:
```cpp
#include <iostream>
int main() {
    std::cout << "Hello, Docker! from C++" << std::endl;
    return 0;
}
```

`Dockerfile` (двухэтапная сборка: gcc для компиляции, alpine для запуска):
```dockerfile
FROM gcc:latest AS build
WORKDIR /app
COPY hello.cpp .
RUN g++ -static -o hello hello.cpp

FROM alpine:latest
COPY --from=build /app/hello /usr/local/bin/hello
CMD ["hello"]
```

### 3. Сборка и запуск

Сборка образа (из папки `cpp-docker`):
```shell
docker build -t cpp-hello .
```

Запуск контейнера:
```shell
docker run --rm cpp-hello
```

Войти в контейнер для исследования:
```shell
docker run -it --entrypoint sh cpp-hello
```

Выйти из контейнера:
```shell
exit
```

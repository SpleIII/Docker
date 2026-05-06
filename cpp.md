## Dockerfile. Hello, World на C++

**C++** — компилируемый, статически типизированный язык программирования общего назначения. Сочетает свойства высокоуровневых и низкоуровневых языков, поддерживает несколько парадигм программирования

> Для выполнения этого задания используйте WSL/Ubuntu, установленную на вашем компьютере с Windows или любой другой десктопный Linux

> Никогда в разработке не используйте русские имена файлов и каталогов!

> Никогда в разработке не используйте пробелы и спец.символы в именах файлов и каталогов!

### 1. Структура проекта
```
cpp-docker/
├── Dockerfile
└── hello.cpp
```

В каталоге для Docker-проектов создать одной bash-командой всю структуру для нового приложения:
```shell
mkdir -p cpp-docker && touch cpp-docker/Dockerfile cpp-docker/hello.cpp && cd cpp-docker
```

<img width="674" height="65" alt="изображение" src="https://github.com/user-attachments/assets/7edb344a-077b-4275-ba72-47e81465494a" />

<img width="290" height="83" alt="изображение" src="https://github.com/user-attachments/assets/06624007-8afa-4024-9736-6f3851d89e36" />

Файл `hello.cpp`
```cpp
#include <iostream>
int main() {
    std::cout << "Hello, Docker! from C++" << std::endl;
    return 0;
}
```

### 2. Содержимое файла `Dockerfile`
```dockerfile
# ---- Этап 1: сборка ----
FROM gcc:latest AS build
# Устанавливаем рабочую директорию
WORKDIR /app
# Копируем исходный код
COPY hello.cpp .
# Компилируем статически, чтобы не зависеть от динамических библиотек
RUN g++ -static -o hello hello.cpp
# ---- Этап 2: финальный образ ----
FROM alpine:latest
# Копируем скомпилированный бинарник из первого этапа
COPY --from=build /app/hello /usr/local/bin/hello
# Запускаем приложение
CMD ["hello"]
```

### 3. Сборка образа

В командной строке, находясь в папке `cpp-docker`, выполнить:
```shell
docker build -t cpp-hello .
```

<img width="984" height="775" alt="изображение" src="https://github.com/user-attachments/assets/6e284dc7-cc40-4d2f-80c6-3358ade0a826" />

Сборка выполнит компиляцию в первом этапе и затем создаст минимальный образ на основе Alpine Linux.

### 4. Запуск контейнера

```shell
docker run --rm cpp-hello
```

<img width="247" height="58" alt="изображение" src="https://github.com/user-attachments/assets/e76ab9c7-0f14-4108-8096-b439f8e14d90" />

### 5. Войти в контейнер для исследования
```shell
docker run -it --entrypoint sh cpp-hello
```

<img width="357" height="88" alt="изображение" src="https://github.com/user-attachments/assets/f05a707f-f1ef-499e-aded-c85054149c6a" />

выйти из контейнера:
```shell
exit
```

<img width="541" height="150" alt="изображение" src="https://github.com/user-attachments/assets/14f624b9-6704-4fbe-bd38-7bbd78a8136d" />


> Если вы обнаружили ошибку в этом тексте - сообщите пожалуйста автору!

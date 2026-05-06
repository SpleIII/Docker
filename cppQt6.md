## Dockerfile. Qt6/C++ приложение

**Qt** — кросс-платформенный фреймворк для разработки ПО на C++.

> Используйте WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

> Задание выполняется в WSL. Если WSL/Ubuntu не установлен — установите по инструкции. В VS Code установите расширение WSL, затем откройте VS Code в WSL командой `code .`

### 1. Структура проекта
```
qt6-docker-app/
├── CMakeLists.txt
├── main.cpp
└── Dockerfile
```

Создать структуру одной командой:
```shell
mkdir -p qt6-docker-app && touch qt6-docker-app/Dockerfile qt6-docker-app/main.cpp qt6-docker-app/CMakeLists.txt && cd qt6-docker-app
```

### 2. Содержимое файлов

`CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.16)
project(Qt6DockerApp)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_AUTOMOC ON)

find_package(Qt6 REQUIRED COMPONENTS Core Widgets)

add_executable(qt6_app main.cpp)
target_link_libraries(qt6_app Qt6::Core Qt6::Widgets)
```

`main.cpp`:
```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QPushButton>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    QWidget window;
    window.setFixedSize(400, 200);
    window.setWindowTitle("Фиксированное окно с кнопкой");
    QVBoxLayout *layout = new QVBoxLayout(&window);
    QLabel *label = new QLabel("Привет из Docker-контейнера с Qt6!");
    label->setAlignment(Qt::AlignCenter);
    layout->addWidget(label);
    QPushButton *button = new QPushButton("Нажми меня");
    button->setFixedSize(120, 30);
    layout->addWidget(button, 0, Qt::AlignCenter);
    window.show();
    return app.exec();
}
```

`Dockerfile`:
```dockerfile
FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    ninja-build \
    qt6-base-dev \
    qt6-base-dev-tools \
    libgl1-mesa-dev \
    libxkbcommon-x11-0 \
    libxcb-icccm4 \
    libxcb-image0 \
    libxcb-keysyms1 \
    libxcb-randr0 \
    libxcb-render-util0 \
    libxcb-shape0 \
    libxcb-shm0 \
    libxcb-sync1 \
    libxcb-xfixes0 \
    libxcb-xinerama0 \
    libxcb-cursor0 \
    libxcb-xkb1 \
    libxcb-util1 \
    libxkbcommon0 \
    libxkbcommon-x11-0 \
    libxcb-icccm4-dev \
    libxcb-image0-dev \
    libxcb-keysyms1-dev \
    libxcb-randr0-dev \
    libxcb-render-util0-dev \
    libxcb-shape0-dev \
    libxcb-xfixes0-dev \
    libxcb-xinerama0-dev \
    libxcb-cursor-dev \
    libxcb-xkb-dev \
    libxkbcommon-dev \
    libxkbcommon-x11-dev \
    mesa-utils \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY CMakeLists.txt main.cpp ./
RUN cmake -B build -S . -G Ninja && \
    cmake --build build --parallel
ENV QT_QPA_PLATFORM=xcb
CMD ["./build/qt6_app"]
```

### 3. Сборка и запуск

Сборка образа (из папки `qt6-docker-app` в VS Code+WSL):
```shell
docker build -t qt6-app .
```

Запуск для Windows WSLg/WSL:
```shell
docker run -it --rm \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  qt6-app
```

Запуск для macOS (не проверялось):
```shell
xhost + 127.0.0.1
```
```shell
docker run -it --rm \
  -e DISPLAY=host.docker.internal:0 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  qt6-app
```

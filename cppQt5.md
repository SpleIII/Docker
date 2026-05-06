## Dockerfile. Qt 5/C++ приложение

**Qt** — кросс-платформенный фреймворк для разработки ПО на C++/QML/Python.

> Используйте WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

> Задание выполняется в WSL. Если WSL/Ubuntu не установлен — установите по инструкции. В VS Code необходимо установить расширение WSL, затем открыть VS Code в WSL командой `code .`

### 1. Структура проекта
```
qt-docker-app/
├── Dockerfile
├── main.cpp
├── CMakeLists.txt
└── run.sh
```

Создать структуру одной командой в WSL/Ubuntu:
```shell
mkdir -p qt-docker-app && touch qt-docker-app/Dockerfile qt-docker-app/main.cpp qt-docker-app/CMakeLists.txt qt-docker-app/run.sh && cd qt-docker-app
```

### 2. Содержимое файлов

`CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.10)
project(QtDockerApp)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

find_package(Qt5 COMPONENTS Widgets REQUIRED)

add_executable(app main.cpp)
target_link_libraries(app Qt5::Widgets)
```

`main.cpp`:
```cpp
#include <QApplication>
#include <QLabel>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    QLabel label("Привет из Docker + Qt!");
    label.setAlignment(Qt::AlignCenter);
    label.resize(400, 200);
    label.show();
    return app.exec();
}
```

`Dockerfile`:
```dockerfile
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    qtbase5-dev \
    qtchooser \
    default-jdk \
    libgl1-mesa-glx \
    libxkbcommon-x11-0 \
    libxcb-icccm4 \
    libxcb-image0 \
    libxcb-keysyms1 \
    libxcb-randr0 \
    libxcb-render-util0 \
    libxcb-xinerama0 \
    libxcb-xfixes0 \
    x11-xkb-utils \
    xkb-data \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY . .

RUN mkdir -p build && \
    cd build && \
    cmake .. && \
    make -j$(nproc)

CMD ["./build/app"]
```

`run.sh` (для Linux/WSL):
```bash
#!/bin/bash
xhost +local:docker
docker run --rm -it \
  -e DISPLAY=$DISPLAY \
  -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  qt-docker-app
```

### 3. Сборка и запуск

Сборка образа (из папки `qt-docker-app` в VS Code+WSL):
```shell
docker build -t qt-docker-app .
```

Запуск для Linux/WSL:
```shell
chmod +x run.sh
```
```shell
./run.sh
```

После запуска появится окно приложения. Если окно не появилось — проверьте логи:
```shell
docker logs <container_id>
```

## Dockerfile. Приложение на C++ и FLTK

**FLTK** (Fast Light Toolkit) — кросс-платформенная библиотека с открытым исходным кодом для построения графических интерфейсов (GUI) на C++.

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
cpp-fltk/
├── Dockerfile
├── main.cpp
└── CMakeLists.txt
```

Создать структуру одной командой:
```shell
mkdir -p cpp-fltk && touch cpp-fltk/Dockerfile cpp-fltk/main.cpp cpp-fltk/CMakeLists.txt && cd cpp-fltk
```

### 2. Содержимое файлов

`main.cpp`:
```cpp
#include <FL/Fl.H>
#include <FL/Fl_Window.H>
#include <FL/Fl_Button.H>

int main(int argc, char **argv) {
    Fl_Window *window = new Fl_Window(300, 180, "Привет из Docker!");
    Fl_Button *button = new Fl_Button(80, 60, 140, 40, "Нажми меня!");
    window->end();
    window->show(argc, argv);
    return Fl::run();
}
```

`CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.10)
project(FLTKDockerDemo)
set(CMAKE_CXX_STANDARD 11)
cmake_policy(SET CMP0072 NEW)
find_package(FLTK REQUIRED)
include_directories(${FLTK_INCLUDE_DIRS})
add_executable(fltk_demo main.cpp)
target_link_libraries(fltk_demo ${FLTK_LIBRARIES})
```

`Dockerfile`:
```dockerfile
FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    libx11-dev \
    libxft-dev \
    libxext-dev \
    libxinerama-dev \
    libxcursor-dev \
    libxrender-dev \
    libxfixes-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    pkg-config \
    libfltk1.3-dev \
    fluid \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY main.cpp CMakeLists.txt ./
RUN mkdir build && cd build && \
    cmake .. && \
    make
CMD ["./build/fltk_demo"]
```

### 3. Сборка и запуск

Сборка образа (из папки `cpp-fltk`):
```shell
docker build -t fltk-demo .
```

Запуск в Windows 10 (сборка 22000+) или 11 в PowerShell:
```shell
docker run -it --rm `
  -v /run/desktop/mnt/host/wslg/.X11-unix:/tmp/.X11-unix `
  -v /run/desktop/mnt/host/wslg:/mnt/wslg `
  -e DISPLAY=:0 `
  -e WAYLAND_DISPLAY=wayland-0 `
  -e XDG_RUNTIME_DIR=/mnt/wslg/runtime-dir `
  fltk-demo
```

Запуск в Linux/WSL 2.0/Mac:
```shell
xhost +local:docker
docker run -it --rm \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  fltk-demo
```

Войти в контейнер для исследования:
```shell
docker run -it --entrypoint bash fltk-demo
```

Выйти из контейнера:
```shell
exit
```

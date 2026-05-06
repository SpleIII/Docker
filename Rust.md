## Dockerfile. Приложение на Rust

**Rust** — современный язык общего назначения, ориентированный на безопасность, скорость и параллелизм.

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
rust-docker/
├── Dockerfile
├── Cargo.toml
└── src/
    └── main.rs
```

Создать структуру одной командой:
```shell
mkdir -p rust-docker/src && touch rust-docker/Dockerfile rust-docker/Cargo.toml rust-docker/src/main.rs && cd rust-docker
```

### 2. Содержимое файлов

`Cargo.toml`:
```toml
[package]
name = "rust-app"
version = "0.1.0"
edition = "2021"
[dependencies]
```

`src/main.rs`:
```rust
fn main() {
    eprintln!("Hello from Rust inside Docker! 🦀");
    std::thread::sleep(std::time::Duration::from_millis(100));
}
```

`Dockerfile` (двухэтапная сборка: rust:slim для компиляции, debian:stable-slim для запуска):
```dockerfile
FROM rust:1-slim AS builder
WORKDIR /app
COPY Cargo.toml .
RUN mkdir src && echo "fn main() {}" > src/main.rs
RUN cargo build --release && rm -f target/release/rust-app*
COPY src ./src
RUN cargo build --release

FROM debian:stable-slim
RUN useradd --create-home appuser
WORKDIR /home/appuser
COPY --from=builder /app/target/release/rust-app ./rust-app
USER appuser
EXPOSE 8081
CMD ["./rust-app"]
```

### 3. Сборка и запуск

Сборка образа (из папки `rust-docker`):
```shell
docker build -t rust-app .
```

Запуск контейнера:
```shell
docker run -it --rm rust-app
```

Зайти в контейнер для исследования:
```shell
docker run -it --rm --entrypoint sh rust-app
```
Запустить программу внутри:
```shell
./rust-app
```

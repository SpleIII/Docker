## Dockerfile. Приложение на Ruby

**Ruby** — высокоуровневый язык программирования.

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
ruby-app/
├── Dockerfile
├── Gemfile
└── app.rb
```

Создать структуру одной командой:
```shell
mkdir -p ruby-app && touch ruby-app/Dockerfile ruby-app/Gemfile ruby-app/app.rb && cd ruby-app
```

### 2. Содержимое файлов

`Gemfile`:
```ruby
source 'https://rubygems.org'
gem 'httparty'
```

`app.rb` (получает случайный факт о котах через API):
```ruby
require 'httparty'

response = HTTParty.get('https://catfact.ninja/fact')
if response.success?
  fact = response.parsed_response['fact']
  puts "🐱 Случайный факт о котах: #{fact}"
else
  puts "😿 Не удалось получить факт."
end
```

`Dockerfile`:
```dockerfile
FROM ruby:3.3-slim
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY Gemfile ./
RUN bundle install
COPY . .
CMD ["ruby", "app.rb"]
```

### 3. Сборка и запуск

Сборка образа (из папки `ruby-app`):
```shell
docker build -t ruby-app .
```

Запуск контейнера:
```shell
docker run -it --rm ruby-app
```

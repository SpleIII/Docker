## Dockerfile. Node.js-приложение

**Node.js** — бесплатная кросс-платформенная среда выполнения JavaScript с открытым исходным кодом, позволяющая запускать JavaScript-код на сервере или локальном компьютере, а не в браузере.

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
my-node-app/
├── package.json
├── app.js
├── Dockerfile
└── .dockerignore
```

Создать структуру одной командой:
```shell
mkdir -p my-node-app && touch my-node-app/Dockerfile my-node-app/package.json my-node-app/app.js my-node-app/.dockerignore && cd my-node-app
```

### 2. Содержимое файлов

`package.json`:
```json
{
  "name": "my-node-app",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

`app.js` (простой веб-сервер):
```javascript
const express = require('express');
const app = express();
const port = 3000;
app.get('/', (req, res) => {
  res.send('Hello from Node.js in Docker! 🐳');
});
app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

`Dockerfile` (двухэтапная сборка: builder для установки зависимостей, финальный образ с непривилегированным пользователем):
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --omit=dev

FROM node:20-alpine
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
WORKDIR /app
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .
USER nodejs
EXPOSE 3000
CMD ["node", "app.js"]
```

`.dockerignore` (опционально):
```
node_modules
npm-debug.log
.git
.gitignore
README.md
```

### 3. Сборка и запуск

Сборка образа (из папки `my-node-app`):
```shell
docker build -t my-node-app .
```

Запуск контейнера:
```shell
docker run -d -p 3000:3000 --name my-node-app my-node-app
```

Проверка: откройте http://localhost:3000

### 4. Для разработки с автообновлением кода (необязательно)

Монтирование текущей папки, чтобы изменения применялись без пересборки:
```shell
docker run -d -p 3000:3000 -v $(pwd):/app --name my-node-app-dev my-node-app
```

> Если вы нашли ошибку — сообщите автору!

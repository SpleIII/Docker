## Dockerfile. Приложение на TypeScript

**TypeScript** — надстройка над JavaScript. Процесс контейнеризации похож на Node.js, но с дополнительным шагом — компиляцией TypeScript в JavaScript перед запуском.

> Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
my-ts-app/
├── src/
│   └── index.ts
├── package.json
├── tsconfig.json
├── Dockerfile
└── .dockerignore
```

Создать структуру одной командой:
```shell
mkdir -p my-ts-app/src && touch my-ts-app/Dockerfile my-ts-app/package.json my-ts-app/tsconfig.json my-ts-app/.dockerignore my-ts-app/src/index.ts && cd my-ts-app
```

### 2. Содержимое файлов

`package.json`:
```json
{
  "name": "my-ts-app",
  "version": "1.0.0",
  "main": "dist/index.js",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  }
}
```

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

`src/index.ts`:
```typescript
const greet = (name: string): void => {
    console.log(`Hello, ${name}! Welcome to TypeScript in Docker! 🐳`);
};
greet('Developer');
```

`Dockerfile` (двухэтапная сборка: builder компилирует TS, runner запускает готовый JS):
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json tsconfig.json ./
RUN npm install
COPY src ./src
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./
RUN npm install --omit=dev
USER node
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

`.dockerignore` (опционально):
```
node_modules
dist
.git
.env
*.log
```

### 3. Сборка и запуск

Сборка образа (из папки `my-ts-app`):
```shell
docker build -t my-ts-app .
```

Запуск контейнера:
```shell
docker run -it --rm my-ts-app
```

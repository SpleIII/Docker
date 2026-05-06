## Dockerfile. Приложение на C# (.NET)

Пример ASP.NET Core приложения. **ASP.NET** — фреймворк с открытым кодом от Microsoft для веб-сайтов, веб-приложений и веб-сервисов на платформе .NET.

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
MyApp/
├── Dockerfile
├── MyApp.csproj
├── Program.cs
```

Создать структуру одной командой:
```shell
mkdir -p MyApp && touch MyApp/Program.cs MyApp/MyApp.csproj MyApp/Dockerfile && cd MyApp
```

### 2. Содержимое файлов

`MyApp.csproj` (веб-приложение на .NET 8.0):
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>
</Project>
```

`Program.cs`:
```csharp
var app = WebApplication.Create(args);
app.MapGet("/", () => "Hello from Docker! Привет из Docker!");
app.Run("http://*:80");
```

`Dockerfile` (многоэтапная сборка: SDK для компиляции, aspnet для рантайма):
```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["MyApp.csproj", "."]
RUN dotnet restore "MyApp.csproj"
COPY . .
RUN dotnet publish "MyApp.csproj" -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

### 3. Сборка и запуск

Сборка образа (из папки `MyApp`):
```shell
docker build -t myapp .
```

Запуск контейнера:
```shell
docker run -d -p 8081:80 --name myapp myapp
```

Проверка: откройте http://localhost:8081

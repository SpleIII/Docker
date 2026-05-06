## Dockerfile. Java приложение

**Java** — строго типизированный объектно-ориентированный язык программирования общего назначения.

> Рекомендуется использовать WSL/Ubuntu или любой десктопный Linux. Не используйте русские имена, пробелы и спецсимволы в названиях файлов и папок!

### 1. Структура проекта
```
my-java-app/
├── Dockerfile
├── pom.xml
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── MyApp.java
```

Создать структуру одной командой:
```shell
mkdir -p my-java-app/src/main/java/com/example && \
touch my-java-app/Dockerfile my-java-app/pom.xml my-java-app/src/main/java/com/example/MyApp.java && cd my-java-app
```

### 2. Содержимое файлов

`pom.xml` (Maven-проект, Java 17):
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-java-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.example.MyApp</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

`src/main/java/com/example/MyApp.java`:
```java
package com.example;

public class MyApp {
    public static void main(String[] args) {
        System.out.println("Hello, World! from Dockerized Java! 🐳");
    }
}
```

`Dockerfile` (двухэтапная сборка: Maven для компиляции, JRE для запуска):
```dockerfile
FROM maven:3.8.5-eclipse-temurin-17 AS builder
WORKDIR /build
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
WORKDIR /app
COPY --from=builder --chown=appuser:appgroup /build/target/*.jar app.jar
USER appuser
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 3. Сборка и запуск

Сборка образа (из папки `my-java-app`):
```shell
docker build -t my-java-app .
```

Запуск контейнера:
```shell
docker run --rm my-java-app
```

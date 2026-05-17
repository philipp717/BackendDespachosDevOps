# 🚚 Backend Despachos - Spring Boot API REST

API REST para la gestión de despachos construida con Spring Boot.

## 📦 Dockerfile

Este proyecto incluye un **Dockerfile multi-stage optimizado** para containerización:

```
Dockerfile              # Dockerfile multi-stage con Maven
```

### 🚀 Ejecución con Docker

```bash
# Construir la imagen
docker build -t backend-despachos:latest .

# Ejecutar con docker-compose (RECOMENDADO)
docker compose up -d

# Ver logs
docker compose logs -f
```

📄 Para más información sobre Docker, consulta [DOCKER.md](./DOCKER.md)

## 🛠️ Requisitos

- Java 21
- Maven 3.8+
- Docker & Docker Compose (para ejecución containerizada)

## 📋 Estructura del Proyecto

```
src/
├── main/
│   ├── java/com/citt/
│   │   ├── controller/
│   │   ├── persistence/
│   │   ├── config/
│   │   └── exceptions/
│   └── resources/
│       └── application.properties
└── test/
    └── java/com/citt/
```

## ✨ Características

- API REST con Spring Boot
- Gestión de despachos
- Logging configurado
- Healthchecks
- Soporte multi-ambiente

## 📝 Configuración

La configuración se gestiona en `src/main/resources/application.properties`

## 🧪 Tests

```bash
mvn test
```

## 📚 Documentación

- [Guía Docker](./DOCKER.md)
- [Docker Compose](./docker-compose.yml)

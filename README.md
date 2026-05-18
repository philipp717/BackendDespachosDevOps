# Backend Despachos DevOps

## Descripción del Proyecto

Este microservicio forma parte de la arquitectura DevOps desplegada en AWS.

Su objetivo es gestionar funcionalidades relacionadas con despachos dentro del sistema.

El servicio se ejecuta mediante contenedores Docker en una instancia privada.

---

## Tecnologías Utilizadas

- Java 17
- Spring Boot
- Maven
- Docker
- GitHub Actions
- Docker Hub
- AWS EC2

---

## Estructura del Proyecto

```bash
BackendDespachosDevOps/
│
├── Springboot-API-REST-DESPACHO/
│   ├── src/
│   ├── Dockerfile
│   ├── pom.xml
│   └── target/
│
├── .github/workflows/deploy.yml
└── README.md
```

## Funcionamiento del Proyecto

El microservicio permite gestionar funcionalidades de despacho dentro de la arquitectura de microservicios.

Se encuentra desplegado en contenedores Docker utilizando AWS EC2.

## Cómo utilizar el Proyecto

1. Clonar repositorio
```bash
git clone https://github.com/philipp717/BackendDespachosDevOps.git
```

2. Entrar al proyecto
```bash
cd BackendDespachosDevOps/Springboot-API-REST-DESPACHO
```

3. Compilar aplicación
```bash
mvn clean package -DskipTests
```

4. Construir imagen Docker
```bash
docker build -t backend-despachos .
```

5. Ejecutar contenedor
```bash
docker run -d -p 8081:8080 backend-despachos
```

## CI/CD

El proyecto utiliza GitHub Actions para automatizar:

- Build Docker
- Push Docker Hub
- Deploy automático

## GitHub Actions

Archivo utilizado:

```
.github/workflows/deploy.yml
```

## Seguridad

- Servicio desplegado en subred privada
- Restricción mediante Security Groups
- Comunicación interna entre servicios

## Commits Explicativos

El repositorio contiene commits descriptivos relacionados con:

- Dockerización
- Configuración backend
- Integración CI/CD
- Configuración AWS

Ejemplos:
```
update: agrega workflow deploy
fix: corrige dockerfile
feat: implementa backend despachos
```

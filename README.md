# Backend Despachos DevOps

## Descripción

Microservicio Spring Boot para la gestión de despachos, preparado para ejecutarse en contenedores sobre Kubernetes y AWS EKS.

La aplicación utiliza variables de entorno para conectarse a una base de datos MySQL, por ejemplo Amazon RDS, sin almacenar credenciales en el repositorio.

## Tecnologías

- Java 17
- Spring Boot
- Maven
- Docker
- Kubernetes
- AWS EKS
- Amazon ECR
- GitHub Actions
- MySQL / Amazon RDS

## Construcción local

Compilar la aplicación:

```bash
mvn clean package -DskipTests
```

Construir la imagen:

```bash
docker build -t backend-despachos-devops:latest .
```

Ejecutar el contenedor con las variables de conexión necesarias:

```bash
docker run --rm -p 8080:8080 \
  -e DB_ENDPOINT=database.example.com \
  -e DB_PORT=3306 \
  -e DB_NAME=despachos \
  -e DB_USERNAME=usuario \
  -e DB_PASSWORD=contraseña \
  backend-despachos-devops:latest
```

## Despliegue en AWS EKS

Los manifiestos están en `k8s/` y crean:

- El namespace `devops`.
- El Deployment `backend-despachos` con 2 réplicas.
- El Service `backend-despachos-service` de tipo `LoadBalancer`.
- Un HPA con un mínimo de 2 réplicas, un máximo de 5 y un objetivo promedio de CPU del 50%.

La imagen configurada en el Deployment usa el siguiente formato:

```text
ACCOUNT_ID.dkr.ecr.AWS_REGION.amazonaws.com/backend-despachos-devops:latest
```

`ACCOUNT_ID` y `AWS_REGION` son placeholders. El workflow los reemplaza con los GitHub Secrets antes de aplicar los manifiestos.

### Secret de MySQL

Antes del primer despliegue, crear el Secret `mysql-secret` en el namespace `devops`. No se deben guardar credenciales reales en archivos versionados.

```bash
kubectl create namespace devops --dry-run=client -o yaml | kubectl apply -f -

kubectl create secret generic mysql-secret \
  --namespace devops \
  --from-literal=DB_ENDPOINT='RDS_ENDPOINT' \
  --from-literal=DB_PORT='3306' \
  --from-literal=DB_NAME='DATABASE_NAME' \
  --from-literal=DB_USERNAME='DATABASE_USER' \
  --from-literal=DB_PASSWORD='DATABASE_PASSWORD'
```

El Deployment carga todas estas variables mediante `envFrom` y `secretRef`.

## CI/CD con GitHub Actions

El workflow `.github/workflows/deploy-eks.yml` se ejecuta con cada push a la rama `master`.

Flujo automatizado:

```text
GitHub Actions → Build Docker → Push Amazon ECR → Deploy AWS EKS
```

El pipeline configura las credenciales de AWS, inicia sesión en ECR, construye y publica la imagen `latest`, actualiza el kubeconfig del clúster, aplica los manifiestos y espera que finalice el rollout.

Secrets requeridos en GitHub:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `AWS_ACCOUNT_ID`
- `EKS_CLUSTER_NAME`

El repositorio ECR `backend-despachos-devops` y el clúster EKS deben existir previamente. El clúster también debe tener Metrics Server instalado para que el HPA obtenga métricas de CPU.

## Verificación

```bash
kubectl get pods -n devops
kubectl get svc -n devops
kubectl get hpa -n devops
kubectl logs -n devops deployment/backend-despachos
```

Para obtener la dirección pública asignada por AWS:

```bash
kubectl get svc backend-despachos-service -n devops
```

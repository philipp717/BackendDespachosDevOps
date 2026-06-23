# Backend Despachos DevOps

## Descripción

Microservicio Spring Boot para la gestión de despachos, preparado para ejecutarse en contenedores sobre Kubernetes en AWS EKS. La aplicación usa Java 17, Maven y variables de entorno para conectarse a una base de datos MySQL en Amazon RDS.

## Tecnologías

- Java 17
- Spring Boot
- Maven
- Docker
- Kubernetes
- Amazon ECR
- Amazon EKS
- GitHub Actions
- MySQL / Amazon RDS

## Flujo CI/CD

El workflow `.github/workflows/deploy-eks.yml` se ejecuta con cada `push` a las ramas `master` o `PhilippReyes`:

```text
GitHub Actions -> Build Docker -> Push Amazon ECR -> Deploy Amazon EKS
```

El pipeline construye la imagen, la publica como:

```text
ACCOUNT_ID.dkr.ecr.AWS_REGION.amazonaws.com/backend-despachos-devops:latest
```

Luego actualiza el `kubeconfig`, aplica los manifiestos de `k8s/` y verifica el rollout del Deployment `backend-despachos`.

## Construcción local

```bash
mvn clean package -DskipTests
docker build -t backend-despachos .
```

Para ejecutar la imagen localmente, se deben proporcionar las variables de conexión:

```bash
docker run --rm -p 8080:8080 \
  -e DB_ENDPOINT=host-mysql \
  -e DB_PORT=3306 \
  -e DB_NAME=despachos \
  -e DB_USERNAME=usuario \
  -e DB_PASSWORD=contraseña \
  backend-despachos
```

## Configuración de Kubernetes

Los recursos se despliegan en el namespace `devops`:

- Deployment `backend-despachos`, inicialmente con 2 réplicas.
- Service `backend-despachos-service` de tipo `LoadBalancer`, para permitir consumo desde un frontend en el navegador.
- HPA `backend-despachos-hpa`, con un mínimo de 2, máximo de 5 réplicas y objetivo promedio de CPU del 50%.

Antes del primer despliegue, crear el namespace y el Secret `mysql-secret` sin guardar credenciales reales en el repositorio:

```bash
kubectl create namespace devops

kubectl create secret generic mysql-secret \
  --namespace devops \
  --from-literal=DB_ENDPOINT=RDS_ENDPOINT \
  --from-literal=DB_PORT=3306 \
  --from-literal=DB_NAME=DATABASE_NAME \
  --from-literal=DB_USERNAME=DATABASE_USER \
  --from-literal=DB_PASSWORD=DATABASE_PASSWORD
```

Para aplicar manualmente los manifiestos, reemplazar `ACCOUNT_ID` y `AWS_REGION` en `k8s/backend-despachos-deployment.yaml` y ejecutar:

```bash
kubectl apply -f k8s/backend-despachos-deployment.yaml
kubectl apply -f k8s/backend-despachos-service.yaml
kubectl apply -f k8s/backend-despachos-hpa.yaml
```

## Secrets requeridos en GitHub

Configurar estos Actions secrets en el repositorio:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `AWS_ACCOUNT_ID`
- `EKS_CLUSTER_NAME`

La cuenta o usuario IAM debe tener permisos para publicar imágenes en el repositorio ECR `backend-despachos-devops`, consultar el clúster EKS y desplegar recursos Kubernetes.

## Verificación

```bash
kubectl get pods -n devops
kubectl get svc -n devops
kubectl get hpa -n devops
kubectl logs -n devops deployment/backend-despachos
```

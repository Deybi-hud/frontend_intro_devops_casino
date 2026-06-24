# Casino Frontend

Frontend en Angular de la plataforma VidalCasino.

## Arquitectura y Despliegue en Amazon EKS (EP3)

Este servicio ha sido migrado a una arquitectura nativa de la nube en Kubernetes.

### Workflow de CI/CD Paso a Paso
El despliegue de esta aplicación está automatizado mediante GitHub Actions (`.github/workflows/deploy.yaml`).

1. **Commit y Push**: Al realizar un push a la rama `deploy`, se activa el pipeline.
2. **Build Docker**: GitHub Actions lee el `Dockerfile` optimizado (multi-stage, con ejecución de usuario `nginx` no root) y construye la imagen de producción.
3. **Push a Amazon ECR**: La imagen se publica de forma privada en ECR etiquetada con el SHA del commit para un versionado preciso, además de la etiqueta `latest`.
4. **Deploy a Amazon EKS**: El pipeline se conecta de forma segura al clúster y actualiza el Deployment en Kubernetes usando la nueva imagen.

### Componentes de Kubernetes (Carpeta `k8s/`)
- **Deployment**: Configura el contenedor y gestiona el ciclo de vida de los pods.
- **Service**: Expone el frontend externamente mediante un `LoadBalancer` de AWS para que los usuarios puedan acceder por internet.
- **HPA (Autoescalado)**: Escala dinámicamente los pods si el consumo de CPU supera el 50%.

### Secretos de GitHub Requeridos
El correcto funcionamiento del pipeline depende de la inyección segura de credenciales vía GitHub Secrets:
`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`, `AWS_REGION`, `EKS_CLUSTER`, `ECR_REPOSITORY`, `DEPLOYMENT_NAME`, `CONTAINER_NAME`.

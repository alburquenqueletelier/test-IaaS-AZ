# Despliegue en Azure Kubernetes Service (AKS)

Este proyecto contiene los manifiestos para desplegar una aplicación compuesta por frontend, backend y base de datos Postgres en un clúster de Kubernetes en Azure.

## Documentación Docker

Para ver los pasos necesarios para habilitar Docker, construir y subir imágenes, consulta la documentación:

[Guía Docker: docs/docker/README.md](docs/docker/README.md)

## Estructura

k8s/
├── base/                       # Recursos genéricos o reutilizables
│   ├── namespace.yaml           # Si quieres usar un namespace dedicado
│   ├── secrets.yaml             # Contraseñas, API keys, etc.
│   └── configmaps.yaml          # Configuración compartida
│
├── postgres/                    # Carpeta específica de la DB
│   ├── deployment.yaml          # Deployment de Postgres
│   └── service.yaml             # Service de Postgres
│
├── backend/                     # Carpeta específica del backend
│   ├── deployment.yaml          # Deployment del backend
│   └── service.yaml             # Service del backend
│
├── frontend/                    # Carpeta específica del frontend
│   ├── deployment.yaml          # Deployment del frontend
│   └── service.yaml             # Service del frontend
│
└── ingress/                     # Ingress para front + back
    └── ingress.yaml             # Reglas de reverse proxy

## Requisitos

- [Azure CLI (`az`)](https://docs.microsoft.com/es-es/cli/azure/install-azure-cli)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- Acceso a una suscripción de Azure y permisos para crear recursos

## Pasos para el despliegue

1. **Login en Azure**
   ```bash
   az login
   ```

2. **Crear un grupo de recursos**
   ```bash
   az group create --name test-k8s-rg --location eastus
   ```

3. **Crear un clúster de Kubernetes**
   ```bash
   az aks create --resource-group test-k8s-rg --name test-k8s-cluster --node-count 2 --generate-ssh-keys
   ```

4. **Conectar kubectl al clúster**
   ```bash
   az aks get-credentials --resource-group test-k8s-rg --name test-k8s-cluster
   ```

5. **(Opcional) Crear Namespace**
   ```bash
   kubectl apply -f base/namespace.yaml
   ```

6. **Crear ConfigMaps y Secrets**
   ```bash
   kubectl apply -f base/configmaps.yaml
   kubectl apply -f base/secrets.yaml
   ```

7. **Desplegar Postgres**
   ```bash
   kubectl apply -f postgres/postgres-dev.yaml
   ```

8. **Desplegar Backend**
   ```bash
   kubectl apply -f backend/back-dev.yaml
   kubectl apply -f backend/backend-service.yaml
   ```

9. **Desplegar Frontend**
   ```bash
   kubectl apply -f frontend/front-dev.yaml
   kubectl apply -f frontend/frontend-service.yaml
   ```

10. **Configurar Ingress (requiere NGINX Ingress Controller)**
    - Instala el controlador si no está presente:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
      ```
    - Aplica las reglas de Ingress:
      ```bash
      kubectl apply -f ingress/ingress.yaml
      ```


## Notas

- Los servicios de frontend y backend consumen imágenes Docker que deben estar previamente subidas a un repositorio (por ejemplo, Docker Hub o Azure Container Registry). Asegúrate de construir y subir tus imágenes antes de desplegar.
- Los secretos están en `base/secrets.yaml` (valores en base64).
- El frontend usa una API Key de Google Maps en `base/configmaps.yaml`.
- Modifica los manifiestos según tus necesidades (nombres, imágenes, variables).
- Para exponer el servicio, revisa la IP externa del Ingress:
   ```bash
   kubectl get ingress
   ```

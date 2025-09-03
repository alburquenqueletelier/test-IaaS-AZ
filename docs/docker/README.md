# Guía para construir y subir imágenes Docker

Esta guía explica cómo crear la imagen Docker para tu servicio (frontend o backend) y subirla a un repositorio para que Kubernetes pueda consumirla.

## Requisitos
- Tener instalado [Docker](https://docs.docker.com/get-docker/)
- Acceso a un repositorio Docker (Docker Hub, Azure Container Registry, etc.)

## 1. Construir la imagen

Ubícate en el directorio donde está tu `Dockerfile` y ejecuta:

```bash
docker build -t <usuario>/<nombre-imagen>:<tag> .
```
Ejemplo:
```bash
docker build -t baal1992/back-dev:latest .
```

## 2. Iniciar sesión en el repositorio

Para Docker Hub:
```bash
docker login
```
Para Azure Container Registry:
```bash
az acr login --name <nombre-registro>
```

## 3. Subir la imagen

Para Docker Hub:
```bash
docker push <usuario>/<nombre-imagen>:<tag>
```
Ejemplo:
```bash
docker push baal1992/back-dev:latest
```

Para Azure Container Registry:
```bash
docker tag <usuario>/<nombre-imagen>:<tag> <acr-login-server>/<nombre-imagen>:<tag>
docker push <acr-login-server>/<nombre-imagen>:<tag>
```

## 4. Usar la imagen en Kubernetes

Asegúrate de que el campo `image:` en tus manifiestos apunte a la imagen subida.

---

**Recomendación:** Actualiza el tag (`:latest`, `:v1`, etc.) cada vez que subas una nueva versión para evitar problemas de caché.

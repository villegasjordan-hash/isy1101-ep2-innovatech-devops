# Innovatech DevOps EP2

Proyecto de contenedorizacion y despliegue automatizado para la Evaluacion Parcial 2 de ISY1101. La solucion despliega un frontend React/Nginx y dos microservicios Spring Boot conectados a MySQL, usando Docker, Docker Hub, EC2 en AWS Academy y GitHub Actions.

## Version entregada

- Rama de despliegue: `deploy`
- Version publicada: `v1.0.0`
- Commit final: `docs: documentar despliegue final en AWS`

## Arquitectura desplegada

- Frontend EC2 publica: `http://98.91.157.229`
- Backend EC2 privada: `10.0.129.168`
- API ventas: `http://98.91.157.229/api/v1/ventas`
- API despachos: `http://98.91.157.229/api/v1/despachos`

La instancia frontend es el unico punto expuesto a Internet. El backend queda en subred privada y recibe trafico desde el frontend mediante reglas de Security Group.

## Imagenes Docker publicadas

Las imagenes se publican en Docker Hub bajo el usuario `javm1996`:

- `javm1996/frontend-despacho:latest`
- `javm1996/backend-ventas:latest`
- `javm1996/backend-despachos:latest`

## Servicios en contenedores

Frontend:

- `frontend-despacho`, publicado en puerto `80` de la EC2 publica.

Backend:

- `mysql-ep2`, base de datos MySQL.
- `backend-ventas`, API en puerto `8080`.
- `backend-despachos`, API en puerto `8081`.
- Volumen Docker nombrado `ep2_mysql_data` para persistencia de MySQL.

## Pipeline CI/CD

El workflow de GitHub Actions se ejecuta con push en la rama `deploy` y realiza:

1. Construccion de imagen frontend.
2. Construccion de imagen backend ventas.
3. Construccion de imagen backend despachos.
4. Publicacion de las imagenes en Docker Hub.
5. Despliegue automatico en EC2 usando SSH y la instancia frontend como salto hacia el backend privado.

Secrets usados:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `EC2_USER`
- `EC2_SSH_KEY`
- `FRONTEND_PUBLIC_IP`
- `BACKEND_PRIVATE_IP`
- `MYSQL_ROOT_PASSWORD`
- `MYSQL_DATABASE`
- `MYSQL_USER`
- `MYSQL_PASSWORD`

## Validacion funcional

Comandos de verificacion usados en EC2:

```bash
docker ps
docker volume ls
```

Pruebas HTTP esperadas:

```text
http://98.91.157.229
http://98.91.157.229/api/v1/ventas
http://98.91.157.229/api/v1/despachos
```

Las APIs pueden devolver `[]` cuando la base de datos esta vacia. Esto confirma que los endpoints responden correctamente con HTTP 200.

## Documentacion adicional

La guia operativa de despliegue esta en:

- `deploy/README_DEPLOY.md`

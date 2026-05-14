# Despliegue EP2 en AWS Academy

## Frontend EC2

La instancia frontend esta en subred publica y expone solo HTTP.
IP publica elastica configurada: `98.91.157.229`.

```bash
mkdir -p ~/ep2
cd ~/ep2
cp frontend.env.example .env
docker compose --env-file .env -f docker-compose.frontend.yml pull
docker compose --env-file .env -f docker-compose.frontend.yml up -d
docker ps
```

## Backend EC2

La instancia backend esta en subred privada. Ejecuta los dos microservicios Spring Boot y MySQL con volumen nombrado.
MySQL se levanta con `mysql_native_password` para mantener compatibilidad con el conector JDBC usado por los backends.

```bash
mkdir -p ~/ep2
cd ~/ep2
cp backend.env.example .env
docker compose --env-file .env -f docker-compose.backend.yml pull
docker compose --env-file .env -f docker-compose.backend.yml up -d
docker ps
docker volume ls
```

El workflow tambien ejecuta un ajuste idempotente del usuario de aplicacion para evitar el error `Public Key Retrieval is not allowed` cuando existe un volumen MySQL previo:

```bash
docker exec mysql-ep2 mysql -uroot -pRootPass12345 -e "ALTER USER 'appuser'@'%' IDENTIFIED WITH mysql_native_password BY 'AppPass12345'; FLUSH PRIVILEGES;"
docker restart backend-ventas backend-despachos
```

## Puertos

- Frontend: `80`
- Backend ventas: `8080`
- Backend despachos: `8081`
- MySQL: `3306`, solo interno al backend

## Pruebas de integracion

- Frontend: `http://98.91.157.229`
- API ventas: `http://98.91.157.229/api/v1/ventas`
- API despachos: `http://98.91.157.229/api/v1/despachos`

## Secrets requeridos en GitHub Actions

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

### docker-compose.yml
```
version 3.8
services:
    backend_service_name:
        build: ./backend_directory/
        container_name: backend_container
        ports:
            - "4000: 4000" ("Docker_port": "Local_host_port")
        volumes:
            - ./backend:/app/backend_directory (sync changes only)
        depends_on: (First compose db_service_name then compose backend_service_name)
            - db_service_name
        environment:
            DB_USER: dbuser
            DB_PASSWORD: dbpassword
            DB_NAME: dbname
            DB_TYPE: postgres
            DB_PORT: 5432
            DB_HOST: db_service_name
    db_service_name:
        image: postgres
        container_name: database_container
        environment: (Based on this docs: https://hub.docker.com/_/postgres)
            POSTGRES_PASSWORD: dbpassword
            POSTGRES_USER: dbuser
            POSTGRES_DB: dbname
            PG_DATA: /var/lib/postgresql/data
        volumes:
            - ./db:/var/lib/postgresql/data
```
## .dockerignore
```
node_modules
Dockerfile
docker-compose.yml
pnpm-lock.yml
```
## Commands
Compose normally, and the container will run automatically:
```
docker-compose up
```
Detach mode:
```
docker-compose up -d
```


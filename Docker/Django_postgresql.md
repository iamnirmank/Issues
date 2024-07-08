### docker-compose.yml
```
version: "3.8"
name: "image_name"
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
Dockerfile
docker-compose.yml
```
## Commands
Compose normally, and all the containers inside the image will run automatically (Create image and run):
```
docker-compose up
```
Detach mode:
```
docker-compose up -d
```
Stopping Docker Image
```
docker-compose down
```
Show the running processes
```
docker-compose ps
```
Restart
```
docker-compose restart service_name
```
Stop
```
docker-compose stop service_name
```
Create only image
```
docker-compose build
```
## Running at Localhost in Case of Localhost
```
HTTP://localhost:4000
```



#headless-cms

1. 프로젝트 폴더 생성 및 docker-compose.yml 작성
```
.
├── backend
├── frontend 
└── docker-compose.yml

```

Strapi 를 처음 실행하는 경우 Dockerfile 따로 생성 또는 docker hub의 pre-built 된 이미지로 프로젝트를 시작한다. 

(optional) Dockerfile을 사용할 경우 주석된 경로에 Dockerfile 생성 후 실행한다.

```yml
version: "3"  
services:  
  strapi-backend:  
    container_name: strapi-backend  
#    build: ./backend  
    image: strapi/strapi:latest  
    restart: unless-stopped  
    env_file: .env  
    environment:  
      - DATABASE_CLIENT=${DATABASE_CLIENT}  
      - DATABASE_HOST=strapi-mariadb  
      - DATABASE_PORT=${DATABASE_PORT}  
      - DATABASE_NAME=${DATABASE_NAME}  
      - DATABASE_USERNAME=${DATABASE_USERNAME}  
      - DATABASE_PASSWORD=${DATABASE_PASSWORD}  
      - JWT_SECRET=${JWT_SECRET}  
      - ADMIN_JWT_SECRET=${ADMIN_JWT_SECRET}  
      - APP_KEYS=${APP_KEYS}  
      - NODE_ENV=development  
    volumes:  
      - ./backend:/srv/app  
    ports:  
      - "9097:1337"  
    networks:  
      - headless  
    depends_on:  
      strapi-mariadb:  
        condition: service_healthy  
  
  strapi-mariadb:  
    container_name: strapiDB  
    image: mariadb:10.5.9  
    command: --default-authentication-plugin=mysql_native_password  
    restart: unless-stopped  
    env_file: .env  
    environment:  
      - MYSQL_USER=${DATABASE_USERNAME}  
      - MYSQL_ROOT_PASSWORD=${DATABASE_PASSWORD}  
      - MYSQL_PASSWORD=${DATABASE_PASSWORD}  
      - MYSQL_DATABASE=${DATABASE_NAME}  
    healthcheck:  
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]  
      timeout: 20s  
      retries: 10  
    volumes:  
      - ${MARIADB_DATABASE_DIR}:/var/lib/mysql  
    networks:  
      - headless  
  
  next-frontend:  
    container_name: next-frontend  
#    build:  
#      context: ./frontend  
#      dockerfile: Dockerfile  
    image: sunny0183/nextjs-docker:latest  
    restart: always  
    volumes:  
      - ./frontend/public:/app/public  
      - ./frontend/src:/app/src  
    ports:  
      - "9098:3000"  
    networks:  
      - headless  
    depends_on:  
      - strapi-backend  
  
networks:  
  headless:  
    driver: bridge
```

2. .env 파일 작성 후
```
.
├── backend
├── frontend 
├── .env
└── docker-compose.yml
```

3. docker-compose.yml 실행

``` bash
# 폴더명으로 docker compose를 명명할 경우
docker-compose -p $(basename "$(pwd)") up -d --force-recreate
```


4. backend 기본 세팅
- localhost:9097 접속
- admin 계정 생성


5. model

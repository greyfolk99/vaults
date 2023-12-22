#Elixir #Phoneix


https://sumin-yang.tistory.com/29
- erlang 기반이라 erlang 설치도 필요

환경 구성
```bash
mix local.hex # hex 패키지 관리자 설치
mix archive.install hex phx_new 1.4.10 # Phoenix 라이브러리 아카이브에 저장
```

프로젝트 생성
```
mix phx.new hello # hello 프로젝트 생성
```

ecto 라이브러리로 데이터베이스 만들기
```
mix ecto.create
```


도커 환경 예제
Dockerfile
```Dockerfile
# Use an official Elixir runtime as a parent image.  
FROM elixir:latest  
  
RUN apt-get update && \  
  apt-get install -y postgresql-client  
  
# Create app directory and copy the Elixir projects into it.  
RUN mkdir /app  
COPY . /app  
WORKDIR /app  
  
# Install Hex package manager.  
RUN mix local.hex --force  
  
# Compile the project.  
RUN mix do compile  
  
CMD ["/app/entrypoint.sh"]
```

docker-compose.yml
```yml
services:  
  chat-server:  
    build: ./app
    environment:  
      PGUSER: postgres  
      PGPASSWORD: postgres  
      PGDATABASE: postgres  
      PGPORT: 5432  
    volumes:  
      - ./app:/app  
    ports:  
      - "4000:4000"  
    depends_on:  
      - db  
      - redis
```
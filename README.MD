# Lab 4 - DevOps Docker
W zadaniu 1 stworzyłem własny obraz oparty o nginx:alpine, dodałem curl, skopiowałem własny index.html i uruchomiłem serwer przez ENTRYPOINT i CMD.
W zadaniu 2 skonfigurowałem PostgreSQL w Docker Compose z nazwanym woluminem, dzięki czemu dane pozostają po restarcie kontenerów.
W zadaniu 3 rozbudowałem środowisko o usługę app, własną sieć backend-net, zależność depends_on oraz komunikację po nazwie hosta db przez wewnętrzny DNS Dockera.


## Zadanie 1
Dockerfile z nginx + własny index.html
![z1i1](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad1-dockerfile/image.png?raw=true)
## Zadanie 2
PostgreSQL w docker-compose + trwały wolumin

## Zadanie 3
Aplikacja wielokontenerowa (app + db) + sieć + komunikacja

## Jak uruchomić

### Zadanie 1
cd lab4zad1-dockerfile
docker build -t moj-nginx:v1 .
docker run -d -p 8080:80 moj-nginx:v1

### Zadanie 2
cd lab4zad2-db
docker compose up -d

### Zadanie 3
cd lab4zad3-app-db
docker compose up -d

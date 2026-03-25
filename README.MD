# Lab 4 - DevOps Docker
W zadaniu 1 stworzyłem własny obraz oparty o nginx:alpine, dodałem curl, skopiowałem własny index.html i uruchomiłem serwer przez ENTRYPOINT i CMD.
W zadaniu 2 skonfigurowałem PostgreSQL w Docker Compose z nazwanym woluminem, dzięki czemu dane pozostają po restarcie kontenerów.
W zadaniu 3 rozbudowałem środowisko o usługę app, własną sieć backend-net, zależność depends_on oraz komunikację po nazwie hosta db przez wewnętrzny DNS Dockera.


## Zadanie 1
Dockerfile z nginx + własny index.html

![z1i1](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad1-dockerfile/image.png?raw=true)

![z1i2](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad1-dockerfile/Zrzut%20ekranu%202026-03-25%20220042.png?raw=true)

## Zadanie 2
PostgreSQL w docker-compose + trwały wolumin

![z2i1](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad2-db/Zrzut%20ekranu%202026-03-25%20222731.png?raw=true)

![z2i2](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad2-db/Zrzut%20ekranu%202026-03-25%20224435.png?raw=true)


## Zadanie 3
Aplikacja wielokontenerowa (app + db) + sieć + komunikacja

![z3i1](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad2-db/Zrzut%20ekranu%202026-03-25%20222731.png?raw=true)

![z3i2](https://github.com/Rzelonek/lab4-devops/blob/main/lab4zad3-app-db/Zrzut%20ekranu%202026-03-25%202247121.png?raw=true)




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

## Odpowiedzi na pytania 


### Zadanie 1
1. Dlaczego używa się rm -rf /var/cache/apk/* po instalacji?

Bo po instalacji pakietów zostają pliki cache menedżera pakietów. Usunięcie ich zmniejsza końcowy rozmiar obrazu. W Dockerze każda instrukcja tworzy warstwę, więc najlepiej robić instalację i czyszczenie w jednym kroku, żeby nie przechowywać niepotrzebnych danych w warstwach obrazu. 
przy apk add --no-cache curl Alpine i tak nie zapisuje klasycznego cache.

2. Shell form vs exec form

Przykład shell form:

CMD nginx -g "daemon off;"

Przykład exec form:

CMD ["nginx", "-g", "daemon off;"]

Exec form jest zalecana, bo:
nie uruchamia dodatkowej powłoki,
lepiej przekazuje sygnały do procesu,
poprawnie działa jako proces PID 1 w kontenerze,
jest bezpieczniejsza i bardziej przewidywalna.

3. Różnica między CMD i ENTRYPOINT
ENTRYPOINT ustawia główny program uruchamiany zawsze.
CMD ustawia domyślne argumenty albo domyślne polecenie, które można łatwo nadpisać.
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]

### Zadanie 2

1. Named volume vs bind mount

Named volume

zarządzany przez Dockera,
Docker sam trzyma dane w swojej przestrzeni,
jest wygodny, bezpieczny i przenośny.

Bind mount

mapuje konkretny katalog z hosta, np. C:\dane do kontenera,
zależy od struktury systemu hosta,
łatwiej o problemy z uprawnieniami i przenoszeniem projektu.

1 Dlaczego named volume jest lepszy dla bazy danych?
Bo jest bardziej stabilny, mniej szależny od hosta, lepiej współpracuje z Dockerem i jest standardowym wyborem dla danych bazodanowych. 

2. Ryzyko braku woluminu
Jeśli niezdefiniujesz woluminu, dane bazy będą zapisane tylko w warstwie zapisywalnej kontenera. Po usunięciu kontenera możena stracić całą bazę danych.

3. Jak Docker Compose tworzy sieć automatycznie?
Compose tworzy domyślną sieć typu bridge dla projektu, jeśli nie zdefiniujesz własnej. Wszystkie usługi w tym samym pliku docker-compose.yaml trafiają do tej sieci i mogą komunikować się po nazwach usług.

### Zadanie 2

1. Dlaczego DATABASE_HOST: db działa?
Bo Docker Compose uruchamia usługi we wspólnej sieci i udostępnia w niej wewnętrzny DNS. 
Nazwa usługi staje się nazwą hosta. Dlatego db rozwiązuje się do adresu IP kontenera bazy. 
PDF na stronie 5 wskazuje dokładnie taki mechanizm dla DATABASE_HOST: db.

2. Czy trzeba mapować port 5432 na hosta, żeby app łączyła się z db?
Nie.
Jeśli app i db są w tej samej sieci Dockera, app łączy się z db po wewnętrznej sieci kontenerów.
Mapowanie 5432:5432 jest potrzebne tylko wtedy, gdy chcesz łączyć się z bazą z komputera hosta, np. z psql.

3. Bridge network vs host network
Bridge network
standardowa sieć Dockera,
daje izolację,
kontenery komunikują się po nazwach usług,
najczęściej używana.

Host network
kontener używa bezpośrednio sieci hosta,
mniej izolacji,
czasem lepsza wydajność,
zwykle stosowana w specyficznych przypadkach, np. gdy potrzebujesz minimalnych narzutów sieciowych albo bezpośredniego dostępu do interfejsów hosta.


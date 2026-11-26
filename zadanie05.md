# 1. Healthcheck backendu

```
PS C:\Users\mtlc2\Documents\breach-checker> docker compose up -d --build
...
 ✔ Container breach-checker-db-1       Healthy                                                                      6.0s
 ✔ Container breach-checker-backend-1  Healthy                                                                     11.7s
 ✔ Container breach-checker-frontend-1 Started                                                                     11.8s

PS C:\Users\mtlc2\Documents\breach-checker> docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    22 seconds ago   Up 16 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         22 seconds ago   Up 22 seconds (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   22 seconds ago   Up 10 seconds             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
```

# 2. Trwałość bazy

Odpowiedzi API:
- po `INSERT`:
    ```
    PS C:\Users\mtlc2\Documents\breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
    {"found":true}
    ```

- po `down` + `up`:
    ```
    PS C:\Users\mtlc2\Documents\breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
    {"found":true}
    ```

- po `down -v` + `up`:
    ```
    PS C:\Users\mtlc2\Documents\breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
    {"found":false}
    ```

> **Dlaczego skrót przetrwał `down`, a nie przetrwał `down -v`; dlaczego po `down -v` w bazie znów są 89 skróty z próbki, choć cała baza została skasowana?**
>
> Skrót nie przetrwał `down -v`, gdyż w odróżnieniu do `down` usuwa on również wolumen, w którym przechowywana jest baza danych w której zaszła zmiana (dodany został nowy skrót). Po `down -v` w bazie znów jest 89 skrótów, gdyż po usunięciu wolumenu, odtworzone zostały dane z pliku `init.sql` podczas ponownego uruchomienia usług.

# 3. Skalowanie

```
PS C:\Users\mtlc2\Documents\breach-checker> docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    7 minutes ago    Up 7 minutes (healthy)    5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    16 seconds ago   Up 14 seconds (healthy)   5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    16 seconds ago   Up 14 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         7 minutes ago    Up 7 minutes (healthy)    5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   7 minutes ago    Up 7 minutes              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
```

```PS C:\Users\mtlc2\Documents\breach-checker> docker compose exec frontend nslookup backend
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:

Non-authoritative answer:
Name:   backend
Address: 172.20.0.3
Name:   backend
Address: 172.20.0.5
Name:   backend
Address: 172.20.0.6
```

Przed restartem frontendu:
```
PS C:\Users\mtlc2\Documents\breach-checker> docker compose logs backend
...
backend-1  | 172.20.0.4 - - [26/Nov/2026 02:54:37] "POST /check HTTP/1.1" 200 -
...
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 03:03:20] "POST /check HTTP/1.1" 200 -
...
```

Po restarcie frontendu:
```
PS C:\Users\mtlc2\Documents\breach-checker> docker compose logs backend
...
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [26/Nov/2026 02:54:37] "POST /check HTTP/1.1" 200 -
...
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
...
backend-3  | check: hash=bc454...
backend-3  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
backend-2  | 172.20.0.4 - - [26/Nov/2026 03:05:18] "POST /check HTTP/1.1" 200 -
...
```

> **Dlaczego przed restartem frontendu ruch trafiał do jednej repliki (wskazówka: kiedy `nginx` tłumaczy nazwę `backend` na adresy - patrz krok 2); dlaczego skalowanie byłoby niemożliwe, gdyby `backend` nadal miał `ports: "5000:5000"`?**
>
> Nginx rozwiązuje nazwę `backend` na adresy IP tylko raz przy starcie. Gdy uruchomiliśmy trzy repliki backendu, `nginx` już działał i miał w pamięci stary DNS. Dopiero po `docker compose restart frontend` `nginx` startuje od nowa, ponownie odpytuje DNS i dostaje listę wszystkich trzech adresów IP replik, więc dopiero wtedy zaczyna rozkładać ruch. Gdyby backend nadal miał `ports: "5000:5000"` skalowanie byłoby niemożliwe, bo port `5000` na hoście może być zajęty tylko przez jeden kontener.
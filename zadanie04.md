# 1. Endpoint `GET /health`

odpowiedź `/health`:
```
{"status":"ok"}
```

linia z `docker logs backend`:
```
172.19.0.1 - - [25/Nov/2026 23:45:53] "GET /health HTTP/1.1" 200 -
```

# 2. Kontener diagnostyczny `healthcheck-probe`

```
PS C:\Users\mtlc2\Documents\breach-checker> docker run --rm --network breach-net healthcheck-probe
backend: OK

PS C:\Users\mtlc2\Documents\breach-checker> docker run --rm healthcheck-probe
curl: (28) Resolving timed out after 3002 milliseconds
backend: UNREACHABLE

PS C:\Users\mtlc2\Documents\breach-checker> docker stop backend
backend

PS C:\Users\mtlc2\Documents\breach-checker> docker run --rm --network breach-net healthcheck-probe
curl: (28) Resolving timed out after 3002 milliseconds
backend: UNREACHABLE
```

> **Dlaczego bez `--network` nazwa `backend` nie działa, choć `backend` jest uruchomiony?**
>
> W takiej sytuacji nazwa `backend` nie jest rozpoznawana, ponieważ kontener, działający jako sonda, nie funkcjonuje w sieci `breach-net`, w której działa kontener `backend`.

# 3. Trwałość danych

1. Warstwa zapisywalna
     - po restarcie
        ```
        PS C:\Users\mtlc2\Documents\breach-checker> docker restart backend
        backend

        PS C:\Users\mtlc2\Documents\breach-checker> docker exec backend cat /tmp/notatka.txt
        notatka
        ```
     - po `rm` + `run`
        ```
        PS C:\Users\mtlc2\Documents\breach-checker> docker rm -f backend
        backend

        PS C:\Users\mtlc2\Documents\breach-checker> docker run -d --name backend --network breach-net -p 5000:5000 -v ./data/leaked-sha1.txt:/app/data/leaked-sha1.txt:ro breach-checker-backend:v1
        c6bca7a4465f752c1703e9bdd47a8df398fe1bbe26c1fb9642d6729e78056326

        PS C:\Users\mtlc2\Documents\breach-checker> docker exec backend cat /tmp/notatka.txt
        cat: /tmp/notatka.txt: No such file or directory
        ```
2. Wolumen
     - zawartość `historia.txt`
        ```
        Thu Nov 26 00:06:44 UTC 2026
        Thu Nov 26 00:07:11 UTC 2026
        ```

> **Dlaczego plik w `/tmp` przetrwał restart, ale nie `rm`; dlaczego `historia.txt` przetrwała, choć oba kontenery zostały usunięte; czy lista wycieków przetrwałaby docker `rm` i dlaczego?**
>
> Plik w `/tmp` przetrwał `docker restart`, bo restart zachowuje ten sam kontener i jego warstwę zapisywalną; `docker rm` usuwa kontener wraz z tą warstwą, więc nowy kontener uruchomiony tym samym poleceniem startuje z pustym systemem plików. `historia.txt` przetrwała, bo dane trafiły do nazwanego wolumenu `breach-dane`, który istnieje niezależnie od kontenerów i nie znika przy ich usuwaniu.
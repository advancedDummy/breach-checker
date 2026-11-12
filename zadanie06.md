# 1. Własne testy
```
PS C:\Users\Miłosz\Downloads\breach-checker> docker compose exec backend pytest -v
================================== test session starts ===================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 8 items

tests/test_check.py::test_leaked_hash_is_found PASSED                              [ 12%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                         [ 25%]
tests/test_check.py::test_missing_hash_field PASSED                                [ 37%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED                    [ 50%]
tests/test_check.py::test_uppercase_leaked_hash_is_found PASSED                    [ 62%]
tests/test_check.py::test_hash_too_short_is_rejected PASSED                        [ 75%]
tests/test_check.py::test_hash_with_invalid_hex_is_rejected PASSED                 [ 87%]
tests/test_check.py::test_non_json_body_is_rejected PASSED                         [100%]

=================================== 8 passed in 0.29s ====================================
```

```
PS C:\Users\Miłosz\Downloads\breach-checker> docker compose exec backend pytest
>>
================================== test session starts ===================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0
rootdir: /app
collected 8 items

tests/test_check.py ....F...                                                       [100%]

======================================== FAILURES ========================================
__________________________ test_uppercase_leaked_hash_is_found ___________________________

client = <FlaskClient <Flask 'app'>>

    def test_uppercase_leaked_hash_is_found(client):
        response = client.post("/check", json={"hash": LEAKED.upper()})
>       assert response.status_code == 200
E       assert 400 == 200
E        +  where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code

tests/test_check.py:27: AssertionError
================================ short test summary info =================================
FAILED tests/test_check.py::test_uppercase_leaked_hash_is_found - assert 400 == 200
============================== 1 failed, 7 passed in 0.43s ===============================
```

> **Dlaczego testy z zajęć nie wykryły usunięcia `.lower()`?**
> Bo żaden z istniejących testów nie wysyłał skrótu wielkimi literami. Wszystkie hashe użyte w testach z zajęć są zapisane małymi literami.

# 2. Awaria a blokada

```
PS C:\Users\Miłosz\Downloads\breach-checker> '{"hash":"7c4a8d09ca3762af61e59520943dc26494f8941b"}' | curl.exe -s -o blad.html -w "HTTP %{http_code}\n" -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
HTTP 500
```

```
PS C:\Users\Miłosz\Downloads\breach-checker> docker compose ps -a
NAME                        IMAGE                                                                     COMMAND                  SERVICE    CREATED          STATUS                     PORTS
breach-checker-backend-1    breach-checker-backend                                                    "flask --app app run…"   backend    58 minutes ago   Exited (1) 1 second ago  
breach-checker-db-1         postgres:16-alpine                                                        "docker-entrypoint.s…"   db         2 hours ago      Exited (0) 5 minutes ago 
breach-checker-frontend-1   sha256:c491898e73b92866a49d9f109a12555725a277a144a10b1cbb54b3f49976e6e0   "/docker-entrypoint.…"   frontend   2 hours ago      Up About an hour           0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
breach-checker-frontend-2   breach-checker-frontend                                                   "/docker-entrypoint.…"   frontend   38 minutes ago   Created 
```

```
PS C:\Users\Miłosz\Downloads\breach-checker> docker inspect --format '{{.State.Status}} {{.State.ExitCode}}' breach-checker-backend-1
exited 1
```
|                            | awaria | blokada |
| -------------------------- | ------ | ------- |
| status w docker compose ps | `(healthy)` | `exited 1` |
| CPU w docker stats         | `0%` | `0%` |
| ostatnie linie logów       | żądanie dotarło, ale nie zakończyła się obsługa | traceback |
| co dostał klient           | `504 Gateway Time-out` | traceback |
| po jakim czasie            | 60 sek. | - |

Plik `blad.html` zawierał informacje o błędzie dotyczącym braku możliwości przetłumaczenia nazwy `db` na adres.

# 3. Frontend w trybie deweloperskim

```
PS C:\Users\Miłosz\Downloads\breach-checker> curl http://localhost:8080/nginx.conf


StatusCode        : 200
StatusDescription : OK
Content           : {115, 101, 114, 118...}
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 326
                    Content-Type: application/octet-stream
                    Date: Thu, 12 Nov 2026 13:57:00 GMT
                    ETag: "6aec6fc9-146"
                    Last-Modified: Th...
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 326], [Content-Type, applicatio
                    n/octet-stream]...}
RawContentLength  : 326
```

Po poprawce:

```
PS C:\Users\Miłosz\Downloads\breach-checker> curl.exe -I http://localhost:8080/nginx.conf
HTTP/1.1 404 Not Found
Server: nginx/1.31.6
Date: Thu, 12 Nov 2026 14:10:05 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive
```

> **Dlaczego obraz produkcyjny frontendu nie ma tego problemu (wskazówka: frontend/Dockerfile) i dlaczego montowanie całego katalogu projektu do katalogu serwowanego przez serwer WWW jest ryzykowne?**
> Obraz produkcyjny nie ma tego problemu, ponieważ w Dockerfile pliki są kopiowane do obrazu instrukcją COPY, a nie montowane z hosta.  
> Montowanie całego katalogu projektu jest ryzykowne, bo serwer WWW może udostępnić pliki, których nie powinien (Dockerfile, konfigurację, sekrety itp.).
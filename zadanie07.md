# 1. Raport podatności

```
breach-checker-frontend:prod (alpine 3.24.4)
============================================
Total: 3 (HIGH: 3, CRITICAL: 0)
```

z `--ignore-unfixed`:

```
breach-checker-frontend:prod (alpine 3.24.4)
============================================
Total: 3 (HIGH: 3, CRITICAL: 0)
```

```
breach-checker-backend:prod (debian 13.7)
=========================================
Total: 49 (HIGH: 49, CRITICAL: 0)
```

z `--ignore-unfixed`:

```
breach-checker-backend:prod (debian 13.7)
=========================================
Total: 2 (HIGH: 2, CRITICAL: 0)
```

# 2. Utwardzenie kontenera w działaniu

```
backend-1  | FileNotFoundError: [Errno 2] No usable temporary directory found in ['/tmp', '/var/tmp', '/usr/tmp', '/app']
```

```
PS C:\Users\Miłosz\Documents\breach-checker> docker compose exec backend touch /etc/test
touch: cannot touch '/etc/test': Read-only file system

What's next:
    Debug this Compose error with Gordon → docker ai "help me fix this compose error"

PS C:\Users\Miłosz\Documents\breach-checker> docker compose exec backend touch /tmp/test

PS C:\Users\Miłosz\Documents\breach-checker> docker compose exec backend grep CapEff /proc/1/status
CapEff: 0000000000000000
```

W konfiguracji deweloperskiej:
```
PS C:\Users\Miłosz\Documents\breach-checker> docker compose exec backend pytest -v
=============================== test session starts ================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 8 items

tests/test_check.py::test_leaked_hash_is_found PASSED                        [ 12%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                   [ 25%]
tests/test_check.py::test_missing_hash_field PASSED                          [ 37%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED              [ 50%]
tests/test_check.py::test_uppercase_leaked_hash_is_found PASSED              [ 62%]
tests/test_check.py::test_hash_too_short_is_rejected PASSED                  [ 75%]
tests/test_check.py::test_hash_with_invalid_hex_is_rejected PASSED           [ 87%]
tests/test_check.py::test_non_json_body_is_rejected PASSED                   [100%]

================================ 8 passed in 0.21s =================================
```

> **Przed czym chroni każde z trzech ustawień i czym `read_only` różni się od `USER appuser` z zajęć?**
>
> `read_only: true` chroni przed zmianą systemu plików kontenera. `cap_drop: ALL` odbiera uprawnienia jądra, takie jak zmiana właściciela plików, surowe gniazda sieciowe czy montowanie, więc nawet proces działający jako `root` nie wykona uprzywilejowanych operacji. `no-new-privileges` blokuje eskalację uprawnień. `USER appuser` zmienia to, kim jest proces (zwykły użytkownik zamiast roota), a `read_only` zmienia to, co w ogóle można zapisać, i działa na cały system plików niezależnie od użytkownika, więc nawet `root` nie zapisze nic poza wyjątkami typu `tmpfs` i bind mount.

# 3. Sekret w usuniętym pliku

```
PS C:\Users\Miłosz\Documents\lab07-sekret> docker history sekret-demo:plik
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
e0352ff57550   24 seconds ago   RUN /bin/sh -c rm /tmp/klucz.txt # buildkit     8.19kB    buildkit.dockerfile.v0
<missing>      24 seconds ago   RUN /bin/sh -c test -s /tmp/klucz.txt && ech…   4.1kB     buildkit.dockerfile.v0
<missing>      24 seconds ago   COPY klucz.txt /tmp/klucz.txt # buildkit        12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ADD alpine-minirootfs-3.22.8-x86_64.tar.gz /…   8.97MB    buildkit.dockerfile.v0
```

```
PS C:\Users\Miłosz\Documents\lab07-sekret> Get-ChildItem obraz/blobs/sha256 | Where-Object { tar -tf $_.FullName 2>$null | Select-String '^tmp/klucz.txt$' } | ForEach-Object { tar -xOf $_.FullName tmp/klucz.txt }
tajny-klucz-milosz
```

> **Dlaczego `RUN rm` nie usunął sekretu z obrazu i jak należało przekazać ten klucz?**
>
> `RUN rm` nie usunął sekretu z obrazu, bo każda instrukcja Dockerfile tworzy nową, niezmienną warstwę. `rm` dodaje tylko w wyższej warstwie znacznik usunięcia, który ukrywa plik w działającym kontenerze, ale warstwa z COPY nadal zawiera `klucz.txt` w całości i każdy, kto ma obraz, może ją wypakować. Klucz należało przekazać jako sekret budowania, który jest montowany tylko na czas jednej instrukcji `RUN` i nie trafia do żadnej warstwy ani do historii obrazu.
# 1. Własne etykiety i pochodzenie warstw

```
PS C:\Users\mtlc2\Documents\breach-checker> docker inspect --format '{{json .Config.Labels}}' milosz-frontend:v1
{"autor":"Miłosz Wybrański","data":"2026-11-25","maintainer":"Miłosz Wybrański"}

PS C:\Users\mtlc2\Documents\breach-checker> docker inspect --format '{{json .Config.Labels}}' milosz-frontend:latest
{"autor":"Miłosz Wybrański","data":"2026-11-25","maintainer":"Miłosz Wybrański"}

PS C:\Users\mtlc2\Documents\breach-checker> docker history milosz-frontend:v1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
21096a03c6b9   24 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      24 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      24 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      24 minutes ago   RUN /bin/sh -c apk add --no-cache nodejs npm…   99.4MB    buildkit.dockerfile.v0
<missing>      24 minutes ago   LABEL autor=Miłosz Wybrański data=2026-11-25    0B        buildkit.dockerfile.v0
<missing>      24 minutes ago   LABEL maintainer=Miłosz Wybrański               0B        buildkit.dockerfile.v0
<missing>      2 days ago       RUN /bin/sh -c set -x     && apkArch="$(cat …   52.4MB    buildkit.dockerfile.v0
<missing>      2 days ago       ENV ACME_VERSION=0.4.2                          0B        buildkit.dockerfile.v0
<missing>      2 days ago       ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 days ago       ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      2 days ago       CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 days ago       STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 days ago       EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 days ago       ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 days ago       COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      2 days ago       COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      2 days ago       COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      2 days ago       COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      2 days ago       COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      2 days ago       RUN /bin/sh -c set -x     && addgroup -g 101…   11.9MB    buildkit.dockerfile.v0
<missing>      2 days ago       ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      2 days ago       ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 days ago       ENV NGINX_VERSION=1.31.10                       0B        buildkit.dockerfile.v0
<missing>      2 days ago       LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ADD alpine-minirootfs-3.24.4-x86_64.tar.gz /…   9.09MB    buildkit.dockerfile.v0
```

> **Które warstwy pochodzą z obrazu bazowego, które z Twojego Dockerfile, dlaczego niektóre mają rozmiar `0B` i dlaczego widać tylko Twoją wartość `maintainer`?**
>
> Wszystkie warstwy poniżej `LABEL maintainer=Miłosz Wybrański` pochodzą z obrazu startowego, z kolei ta wartwa i wyższe pochodzą z `Dockerfile`. Niektóre warstwy mają rozmiar `0B` ponieważ są to jedynie metadane. Komenda `inspect` obrazów zwraca tylko moją wartość `maintainer`, gdyż nadpisuje etykietę obrazu bazowego o tej samej nazwie.

# 2. `RUN` działa raz

```
PS C:\Users\mtlc2\Documents\breach-checker> docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                   NAMES
e9e77ab74482   milosz-frontend:v2   "/docker-entrypoint.…"   2 seconds ago    Up 2 seconds    0.0.0.0:32769->80/tcp   frontend-drugi
ba36ce498a0c   milosz-frontend:v2   "/docker-entrypoint.…"   51 seconds ago   Up 50 seconds   0.0.0.0:32768->80/tcp   frontend
```

`build.txt` z kontenera `frontend`:
```
Wed Nov 25 19:57:15 UTC 2026
```

`build.txt` z kontenera `frontend-drugi`:
```
Wed Nov 25 19:57:15 UTC 2026
```

> **Dlaczego czas w `build.txt` jest taki sam w obu kontenerach i nie jest to czas ich uruchomienia?**
>
> Czas zapisany w plikach obu kontenerów jest taki sam, gdyż jest on zapisywany do plików przy okazji wykonania instrukcji `RUN`, która uruchamiana jest przy budowie obrazu, a nie każdorazowo przy starcie kontenera.

# 3. Nadpisanie `CMD`

```
root   /usr/share/nginx/html;
```

> **Dlaczego kopiujemy pliki właśnie do `/usr/share/nginx/html`?**
>
> Kopiujemy pliki do `/usr/share/nginx/html`, ponieważ jest to domyślny dokument `root` zdefiniowany w konfiguracji `nginx` w oficjalnym obrazie, `nginx` serwuje stamtąd pliki statyczne.

# 4. Diagnoza błędnego `COPY`

```
PS C:\Users\mtlc2\Documents\breach-checker> docker exec frontend ls /usr/share/nginx
html
index.html
script.js
style.css

PS C:\Users\mtlc2\Documents\breach-checker> docker exec frontend ls /usr/share/nginx/html
50x.html
build.txt
index.html
```

> **Który plik `index.html` sprawdził `htmlhint` i dlaczego `build` przeszedł?**
>
> `htmlhint` sprawdził domyślny plik `index.html`, czyli stronę powitalną `nginx`, a nie zrobioną przez nas. `build` przeszedł, ponieważ `htmlhint` nie wykrył we wspomnianym pliku żadnego błędu.

# 5. Usunięcie tagu a usunięcie obrazu:

```
PS C:\Users\mtlc2\Documents\breach-checker> docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest

PS C:\Users\mtlc2\Documents\breach-checker> docker rmi breach-checker-frontend:v2
Untagged: breach-checker-frontend:v2
Deleted: sha256:3d84fb01dfb7a75e7cc0809c560cc70fb07e3cfcd4595bbad48e9df38a43429d
```

> **Kiedy docker `rmi` faktycznie usuwa obraz z dysku?**
>
> Dzieje się to kiedy usunięty zostanie ostatni tag wskazujący na dany obraz.
PS C:\Users\Miłosz\Downloads\breach-checker> docker inspect --format '{{json .Config.Labels}}' milosz-frontend:v1
>>
{"maintainer":"milosz.wybranski@example.com"}
PS C:\Users\Miłosz\Downloads\breach-checker> docker inspect --format '{{json .Config.Labels}}' milosz-frontend:latest
{"maintainer":"milosz.wybranski@example.com"}

PS C:\Users\Miłosz\Downloads\breach-checker> docker inspect milosz-frontend:v1 milosz-frontend:latest
[
    {
        "Id": "sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429",
        "RepoTags": [
            "milosz-frontend:latest",
            "milosz-frontend:v1"
        ],
        "RepoDigests": [
            "milosz-frontend@sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429"
        ],
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2026-11-05T01:28:42.431351699Z",
        "Config": {
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.31.6",
                "PKG_RELEASE=1",
                "DYNPKG_RELEASE=1",
                "NJS_VERSION=1.0.1",
                "NJS_RELEASE=1",
                "ACME_VERSION=0.4.1"
            ],
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "WorkingDir": "/",
            "Labels": {
                "maintainer": "milosz.wybranski@example.com"
            },
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 229013908,
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:74d97c428c51a828f9051a7a40a53ff1fc99e54fc30323ce36760701b0b7f711",
                "sha256:379f51bf5b58bf458dfe39fe97ba49d63950929004af1808361bfe01ba73d6d0",
                "sha256:641b74319bab4250ece7d99c1e71fba36e67931d64b62a1d574a56986d45dae0",
                "sha256:2a7ed9afc1c38e245bbe331a4bbf99010cc9ff2ae546ee9507373a3a2dc80f0c",
                "sha256:149ab1e5d6caa8add417a7dc26743baa867bf078b3c6aafc96b8197221a87a1c",
                "sha256:41473198c04ae23f0434ac3a73f8e52549312be4638d2979bc1df9d209bdb9c0",
                "sha256:c86f6810da0b81ee848c1e6cc4bc22c0b7bd4f553f81ef3a33c41791b90631a2",
                "sha256:001b5608a9297e16ed0253a4694ca1cc98fa554c7e39c36ca652742cfc294efc",
                "sha256:9281d31fa6ba68ff7ef8ecaa0e1830313ce1f4fb119cbfe68c550682e710ca75",
                "sha256:88e912d79b315735d67142cbbbfa260a4054f60fc94227ce590f814c6854f008",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef"
            ]
        },
        "Metadata": {
            "LastTagTime": "2026-11-05T01:46:38.733727414Z"
        },
        "Descriptor": {
            "mediaType": "application/vnd.oci.image.index.v1+json",
            "digest": "sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429",
            "size": 856
        },
        "Identity": {
            "Build": [
                {
                    "Ref": "pzbm2xk8owlos52kspuuyk0q9",
                    "CreatedAt": "2026-11-05T01:46:38.761760714Z"
                }
            ]
        }
    },
    {
        "Id": "sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429",
        "RepoTags": [
            "milosz-frontend:latest",
            "milosz-frontend:v1"
        ],
        "RepoDigests": [
            "milosz-frontend@sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429"
        ],
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2026-11-05T01:28:42.431351699Z",
        "Config": {
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.31.6",
                "PKG_RELEASE=1",
                "DYNPKG_RELEASE=1",
                "NJS_VERSION=1.0.1",
                "NJS_RELEASE=1",
                "ACME_VERSION=0.4.1"
            ],
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "WorkingDir": "/",
            "Labels": {
                "maintainer": "milosz.wybranski@example.com"
            },
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 229013908,
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:74d97c428c51a828f9051a7a40a53ff1fc99e54fc30323ce36760701b0b7f711",
                "sha256:379f51bf5b58bf458dfe39fe97ba49d63950929004af1808361bfe01ba73d6d0",
                "sha256:641b74319bab4250ece7d99c1e71fba36e67931d64b62a1d574a56986d45dae0",
                "sha256:2a7ed9afc1c38e245bbe331a4bbf99010cc9ff2ae546ee9507373a3a2dc80f0c",
                "sha256:149ab1e5d6caa8add417a7dc26743baa867bf078b3c6aafc96b8197221a87a1c",
                "sha256:41473198c04ae23f0434ac3a73f8e52549312be4638d2979bc1df9d209bdb9c0",
                "sha256:c86f6810da0b81ee848c1e6cc4bc22c0b7bd4f553f81ef3a33c41791b90631a2",
                "sha256:001b5608a9297e16ed0253a4694ca1cc98fa554c7e39c36ca652742cfc294efc",
                "sha256:9281d31fa6ba68ff7ef8ecaa0e1830313ce1f4fb119cbfe68c550682e710ca75",
                "sha256:88e912d79b315735d67142cbbbfa260a4054f60fc94227ce590f814c6854f008",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef"
            ]
        },
        "Metadata": {
            "LastTagTime": "2026-11-05T01:46:38.733727414Z"
        },
        "Descriptor": {
            "mediaType": "application/vnd.oci.image.index.v1+json",
            "digest": "sha256:1b14649ff3176e97d0d964cf49b58c25bbbf20f313973df80832a82a28f76429",
            "size": 856
        },
        "Identity": {
            "Build": [
                {
                    "Ref": "pzbm2xk8owlos52kspuuyk0q9",
                    "CreatedAt": "2026-11-05T01:46:38.761760714Z"
                }
            ]
        }
    }
]

PS C:\Users\Miłosz\Downloads\breach-checker> docker history milosz-frontend:v1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
1b14649ff317   20 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      20 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      20 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      13 days ago      RUN /bin/sh -c apk add --no-cache nodejs npm…   102MB     buildkit.dockerfile.v0
<missing>      13 days ago      LABEL maintainer=milosz.wybranski@example.com   0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      RUN /bin/sh -c set -x     && apkArch="$(cat …   52.4MB    buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV NJS_VERSION=1.0.1                           0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      6 weeks ago      COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      6 weeks ago      COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      6 weeks ago      COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      6 weeks ago      COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      6 weeks ago      RUN /bin/sh -c set -x     && addgroup -g 101…   5.72MB    buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ENV NGINX_VERSION=1.31.6                        0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      6 weeks ago      ADD alpine-minirootfs-3.24.2-x86_64.tar.gz /…   9.08MB    buildkit.dockerfile.v0

Thu Nov  5 01:50:49 UTC 2026

PS C:\Users\Miłosz\Downloads\breach-checker> docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED              STATUS              PORTS                                     NAMES
d81b7be80ba5   milosz-frontend:v2   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:32769->80/tcp                     frontend1
c321ccceb70e   milosz-frontend:v2   "/docker-entrypoint.…"   3 minutes ago        Up 3 minutes        0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   frontend
PS C:\Users\Miłosz\Downloads\breach-checker> docker run -d --name frontend1 -P milosz-frontend:v2

        root   /usr/share/nginx/html;

Kopiujemy pliki do /usr/share/nginx/html, ponieważ jest to domyślny dokument root zdefiniowany w konfiguracji nginx w oficjalnym obrazie, nginx serwuje stamtąd pliki statyczne.

PS C:\Users\Miłosz\Downloads\breach-checker> docker exec frontend ls /usr/share/nginx
html
index.html
script.js
style.css
PS C:\Users\Miłosz\Downloads\breach-checker> docker exec frontend ls /usr/share/nginx/html
50x.html
build.txt
index.html

htmlhint sprawdził plik index.html znajdujący się w kontekście budowania (na hoście), a nie ten, który ostatecznie znalazł się w obrazie.

Build przeszedł bez błędu, ponieważ instrukcja COPY zawsze się udaje (pliki zostały skopiowane tylko w złe miejsce), a hinter działa na plikach źródłowych przed/w trakcie kopiowania, a nie na finalnym systemie plików kontenera.

PS C:\Users\Miłosz\Downloads\breach-checker> docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest
PS C:\Users\Miłosz\Downloads\breach-checker> docker images
                                                                         i Info →   U  In Use
IMAGE                           ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest                   71e9b7bae160         13MB         3.94MB
breach-checker-backend:v1       6dcb27f28f8d        198MB         48.3MB    U
breach-checker-frontend:v1      1f8d3b0fb6a4       93.6MB         26.3MB
breach-checker-frontend:v2      f761817c4246       93.6MB         26.3MB
breach-checker-frontend:v3      3fd9ea5a4e4d        229MB         59.5MB
breach-checker-frontend:v4      c500eeb80c7f        229MB         59.5MB
dziennik-ocen-backend:latest    e23534a576f0        254MB         66.9MB
dziennik-ocen-frontend:latest   b5d3409b3793       93.6MB         26.3MB
milosz-frontend:latest          1b14649ff317        229MB         59.5MB
milosz-frontend:v1              1b14649ff317        229MB         59.5MB
milosz-frontend:v2              3a6b2f74bdf0        229MB         59.5MB    U
nginx:alpine                    df221db836e1       94.4MB         27.2MB
postgres:15-alpine              f7d23353e1b1        417MB          116MB
PS C:\Users\Miłosz\Downloads\breach-checker> docker rmi breach-checker-frontend:v2
Untagged: breach-checker-frontend:v2
Deleted: sha256:f761817c4246e03fea4abe529378315d98104435909226b7fb410aa4da422d0d

docker rmi faktycznie usuwa obraz z dysku dopiero wtedy, gdy usuwamy ostatni tag wskazujący na dany IMAGE ID.  
Dopóki istnieje choć jeden tag (w tym przypadku v2), obraz pozostaje na dysku, usuwany jest tylko tag.
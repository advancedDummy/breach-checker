PS C:\Users\Miłosz\Downloads\breach-checker> docker compose up -d --build
[+] Building 3.3s (24/24) FINISHED
 => [internal] load local bake definitions                                              0.0s
 => => reading from stdin 1.08kB                                                        0.0s
 => [frontend internal] load build definition from Dockerfile                           0.1s
 => => transferring dockerfile: 327B                                                    0.0s
 => [backend internal] load build definition from Dockerfile                            0.1s
 => => transferring dockerfile: 207B                                                    0.0s
 => [frontend internal] load metadata for docker.io/library/nginx:alpine                0.2s
 => [backend internal] load metadata for docker.io/library/python:3.12-slim             1.9s
 => [frontend internal] load .dockerignore                                              0.0s
 => => transferring context: 66B                                                        0.0s
 => [frontend 1/5] FROM docker.io/library/nginx:alpine@sha256:df221db836e1754089190208  0.0s
 => => resolve docker.io/library/nginx:alpine@sha256:df221db836e1754089190208cee7eeda9  0.0s
 => [frontend internal] load build context                                              0.0s
 => => transferring context: 120B                                                       0.0s
 => CACHED [frontend 2/5] RUN apk add --no-cache nodejs npm && npm install -g htmlhint  0.0s
 => CACHED [frontend 3/5] COPY index.html style.css script.js /usr/share/nginx/html/    0.0s
 => CACHED [frontend 4/5] COPY nginx.conf /etc/nginx/conf.d/default.conf                0.0s
 => CACHED [frontend 5/5] RUN htmlhint /usr/share/nginx/html/index.html                 0.0s
 => [frontend] exporting to image                                                       0.2s
 => => exporting layers                                                                 0.0s
 => => exporting manifest sha256:4d48cea524622ccdfab46e293540111dc5a7de8f32fe416fd2c25  0.0s
 => => exporting config sha256:343ae325dbc77309a6087e674e6b6d445e9b0c1bd1abb1b62ad3dbf  0.0s
 => => exporting attestation manifest sha256:4976bb61cbc4af5098787f591a30e64d5da764463  0.0s
 => => exporting manifest list sha256:c491898e73b92866a49d9f109a12555725a277a144a10b1c  0.0s
 => => naming to docker.io/library/breach-checker-frontend:latest                       0.0s
 => => unpacking to docker.io/library/breach-checker-frontend:latest                    0.0s
 => [frontend] resolving provenance for metadata file                                   0.0s
 => [auth] library/python:pull token for registry-1.docker.io                           0.0s
 => [backend internal] load .dockerignore                                               0.0s
 => => transferring context: 79B                                                        0.0s
 => [backend 1/5] FROM docker.io/library/python:3.12-slim@sha256:e695ab189fa2d082b1c61  0.1s
 => => resolve docker.io/library/python:3.12-slim@sha256:e695ab189fa2d082b1c61c69a6b88  0.0s
 => [backend internal] load build context                                               0.0s
 => => transferring context: 63B                                                        0.0s
 => CACHED [backend 2/5] WORKDIR /app                                                   0.0s
 => CACHED [backend 3/5] COPY requirements.txt .                                        0.0s
 => CACHED [backend 4/5] RUN pip install --no-cache-dir -r requirements.txt             0.0s
 => CACHED [backend 5/5] COPY app.py .                                                  0.0s
 => [backend] exporting to image                                                        0.1s
 => => exporting layers                                                                 0.0s
 => => exporting manifest sha256:fe4f0506c176d3c0cc5b3114b431bca38f4345a6bac5da8ad135e  0.0s
 => => exporting config sha256:245893365e6ca6f76d9995f020a3ecb5700b7354650d9bf1569a5ce  0.0s
 => => exporting attestation manifest sha256:5232a9cb38e7638bb61b6fb37898056b8b654e51a  0.0s
 => => exporting manifest list sha256:d26fd8ba4c39f96ec1a78aaa83ed879af642f44bb086318c  0.0s
 => => naming to docker.io/library/breach-checker-backend:latest                        0.0s
 => => unpacking to docker.io/library/breach-checker-backend:latest                     0.0s
 => [backend] resolving provenance for metadata file                                    0.0s
[+] up 6/6
 ✔ Image breach-checker-backend        Built                                             3.7s
 ✔ Image breach-checker-frontend       Built                                             3.7s
 ✔ Network breach-checker_default      Created                                           0.0s
 ✔ Container breach-checker-db-1       Healthy                                           6.5s
 ✔ Container breach-checker-backend-1  Healthy                                          12.1s
 ✔ Container breach-checker-frontend-1 Started                                          12.2s
PS C:\Users\Miłosz\Downloads\breach-checker> docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    17 seconds ago   Up 10 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         17 seconds ago   Up 16 seconds (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   17 seconds ago   Up 5 seconds              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

PS C:\Users\Miłosz\Downloads\breach-checker> docker compose exec db psql -U breach -d breachdb -c "INSERT INTO leaked_hashes VALUES ('bc45408273d7e1914b741c3407b0b559764f7876');"
>>
INSERT 0 1

PS C:\Users\Miłosz\Downloads\breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
{"found":true}

PS C:\Users\Miłosz\Downloads\breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
{"found":false}

PS C:\Users\Miłosz\Downloads\breach-checker> docker compose up -d --scale backend=3
>> docker compose ps
>> docker compose exec frontend nslookup backend
>>
[+] up 5/5
 ✔ Container breach-checker-frontend-1 Running                                           0.0s
 ✔ Container breach-checker-backend-1  Healthy                                           6.7s
 ✔ Container breach-checker-db-1       Healthy                                           0.7s
 ✔ Container breach-checker-backend-3  Healthy                                           6.7s
 ✔ Container breach-checker-backend-2  Healthy                                           6.7s
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    10 minutes ago   Up 10 minutes (healthy)   5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    7 seconds ago    Up 6 seconds (healthy)    5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    7 seconds ago    Up 6 seconds (healthy)    5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         10 minutes ago   Up 10 minutes (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   10 minutes ago   Up 7 minutes              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:
Name:   backend
Address: 172.19.0.3
Name:   backend
Address: 172.19.0.5
Name:   backend
Address: 172.19.0.6

Non-authoritative answer:

backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:45] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:41:50] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:51] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:51] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:51] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:41:52] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:00] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:10] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:41] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:42:51] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:42:59] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:42:59] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:43:01] "GET /health HTTP/1.1" 200 -

backend-2  | 127.0.0.1 - - [05/Nov/2026 13:42:27] "GET /health HTTP/1.1" 200 -
backend-3  |  * Running on all addresses (0.0.0.0)
backend-3  |  * Running on http://127.0.0.1:5000
backend-2  | 127.0.0.1 - - [05/Nov/2026 13:42:37] "GET /health HTTP/1.1" 200 -
backend-1  | Press CTRL+C to quit
backend-3  |  * Running on http://172.19.0.6:5000
backend-2  | 127.0.0.1 - - [05/Nov/2026 13:42:47] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [05/Nov/2026 13:42:57] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:42:59] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:32:20] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-1  | 172.19.0.4 - - [05/Nov/2026 13:32:26] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:32:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:32:41] "GET /health HTTP/1.1" 200 -
backend-3  | Press CTRL+C to quit
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-3  | 127.0.0.1 - - [05/Nov/2026 13:42:27] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [05/Nov/2026 13:42:37] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [05/Nov/2026 13:42:47] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [05/Nov/2026 13:42:58] "GET /health HTTP/1.1" 200 -
backend-2  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:32:51] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [05/Nov/2026 13:33:01] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-3  | check: hash=bc454...
backend-3  | 172.19.0.4 - - [05/Nov/2026 13:43:00] "POST /check HTTP/1.1" 200 -


FROM nginx:alpine
LABEL maintainer="Miłosz Wybrański"
LABEL autor="Miłosz Wybrański" data="2026-11-25"
RUN apk add --no-cache nodejs npm && npm install -g htmlhint
COPY . /usr/share/nginx/
RUN date > /usr/share/nginx/html/build.txt
RUN htmlhint /usr/share/nginx/html/index.html
EXPOSE 80
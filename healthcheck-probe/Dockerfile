FROM alpine:3.20
RUN apk add --no-cache curl
CMD ["sh", "-c", "if curl -fsS -o /dev/null --max-time 3 http://backend:5000/health; then echo 'backend: OK'; else echo 'backend: UNREACHABLE'; fi"]
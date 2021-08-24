FROM selaworkshops/alpine_new:3.4
RUN apk add --no-cache python
ARG port=5000
ENV port=$port
WORKDIR /app
COPY index.html /app/
ENTRYPOINT python -m SimpleHTTPServer $port
EXPOSE $port
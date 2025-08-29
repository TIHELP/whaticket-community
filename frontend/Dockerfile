FROM node:18-alpine as build-deps   # (recomendado atualizar do 14 -> 18)
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY .env* ./
COPY src/ ./src/
COPY public/ ./public/
RUN npm run build

FROM nginx:alpine
RUN apk add --no-cache jq openssl

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

ENV PUBLIC_HTML=/var/www/public/

# suas configs do nginx (do repo)
COPY .docker/nginx /etc/nginx/

# 👇 FIX: remove o site padrão do nginx base para não duplicar "listen 80"
RUN rm -f /etc/nginx/conf.d/default.conf

# app estático
COPY --from=build-deps /usr/src/app/build ${PUBLIC_HTML}
EXPOSE 80

COPY .docker/add-env-vars.sh /docker-entrypoint.d/01-add-env-vars.sh
RUN chmod +x /docker-entrypoint.d/01-add-env-vars.sh

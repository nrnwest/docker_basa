FROM nginx:stable-alpine

ARG COMPOSE_PROJECT_NAME=name_project
ARG PHP_FPM_PORT=9000
ARG ROOT_DIR=/docker_local

COPY .${ROOT_DIR}/nginx/nginx.conf /etc/nginx/nginx.conf
COPY .${ROOT_DIR}/nginx/default.conf /etc/nginx/conf.d/default.conf

RUN apk update && apk upgrade && apk add --no-cache bash

RUN set -x ; \
    addgroup -g 1000 -S www ; \
    adduser -u 1000 -D -S -G www www && exit 0 ; exit 1

ARG PHP_UPSTREAM_CONTAINER=${COMPOSE_PROJECT_NAME}-php-fpm
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_FPM_PORT}; }" > /etc/nginx/conf.d/upstream.conf

EXPOSE 80

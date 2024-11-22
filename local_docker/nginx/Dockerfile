FROM nginx:stable-alpine

ARG COMPOSE_PROJECT_NAME=name_project
ARG PHP_FPM_PORT=9000
ARG DOCKER_DIR=/docker_local
ARG USER_GROUP=www-data

COPY .${DOCKER_DIR}/nginx/nginx.conf /etc/nginx/nginx.conf
COPY .${DOCKER_DIR}/nginx/default.conf /etc/nginx/conf.d/default.conf

RUN apk update && apk upgrade && apk add --no-cache bash

RUN set -x ; \
    addgroup -g 1000 -S ${USER_GROUP} ; \
    adduser -u 1000 -D -S -G ${USER_GROUP} ${USER_GROUP} && exit 0 ; exit 1

ARG PHP_UPSTREAM_CONTAINER=${COMPOSE_PROJECT_NAME}-php-fpm
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_FPM_PORT}; }" > /etc/nginx/conf.d/upstream.conf

EXPOSE 80

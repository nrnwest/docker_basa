PHP_FPM=php-fpm
DOCKER_COMPOSE = docker compose -f ./docker-compose.yml --env-file ./.env

.PHONY: up down build-no-cache php_fpm composer_install composer_update init sleep passport swagger_generate clean_docker

up:
	${DOCKER_COMPOSE} up -d

down:
	${DOCKER_COMPOSE} down -v

build-no-cache:
	${DOCKER_COMPOSE} build --no-cache
	${DOCKER_COMPOSE} up -d

build:
	${DOCKER_COMPOSE} build
	${DOCKER_COMPOSE} up -d

php_fpm:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} bash

composer_install:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} composer install --optimize-autoloader

composer_update:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} composer update

migrated:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} php artisan migrate

passport:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} php artisan passport:keys --force

init:
	make build-no-cache sleep composer_install migrated passport swagger_generate cache_clear

cache_clear:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} php artisan optimize:clear

swagger_generate:
	${DOCKER_COMPOSE} exec -it ${PHP_FPM} php artisan l5-swagger:generate

clean_docker:
	docker system prune -a --volumes

sleep:
	sleep 4

FROM php:8.2-fpm

ARG PUID=1000
ARG PGID=1000
ARG ROOT_DIR=/docker
ARG PHP_FPM_PORT=9000
ARG WORK_DIR=/app
ARG USER_GROUP=www-data

WORKDIR $WORK_DIR

# Install dependencies and PHP extensions
RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
    vim \
    unzip \
    zlib1g-dev \
    libzip-dev \
    libpq-dev \
    procps \
    tzdata \
    libicu-dev \
    pkg-config \
    curl \
    gnupg \
    postgresql-client && \
    docker-php-ext-install \
    pdo_pgsql \
    bcmath \
    zip \
    pcntl \
    intl && \
    pecl install redis && docker-php-ext-enable redis && \
    rm -rf /var/lib/apt/lists/*

# Create and configure the www user
RUN if getent passwd $USER_GROUP; then userdel $USER_GROUP; fi && \
    if getent group $USER_GROUP; then groupdel $USER_GROUP; fi && \
    groupadd -g $PGID $USER_GROUP && \
    useradd -u $PUID -g $USER_GROUP -m $USER_GROUP


# Copy application files
COPY --chown=$USER_GROUP:$USER_GROUP ./ $WORK_DIR
COPY ./.env.dev .env
COPY .$ROOT_DIR/php-fpm/config/custom.ini $PHP_INI_DIR/conf.d/custom.ini
COPY .$ROOT_DIR/php-fpm/config/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./oauth-private.key $WORK_DIR/storage/oauth-private.key
COPY ./oauth-public.key $WORK_DIR/storage/oauth-public.key

# Set Composer environment variables
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_MEMORY_LIMIT=-1
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# Ensure required directories exist and set correct permissions
RUN mkdir -p $WORK_DIR/vendor $WORK_DIR/storage/logs $WORK_DIR/bootstrap/cache && \
    chown -R $USER_GROUP:$USER_GROUP $WORK_DIR && \
    chmod -R 775 $WORK_DIR

# Install Composer dependencies
RUN composer install --optimize-autoloader --no-dev --prefer-dist --no-interaction --no-progress
RUN php artisan optimize:clear

USER $USER_GROUP

EXPOSE $PHP_FPM_PORT

CMD ["php-fpm"]

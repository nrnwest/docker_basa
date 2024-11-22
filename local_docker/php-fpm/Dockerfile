FROM php:8.2-fpm

# If the image is being built and an .env file is specified during the build, it will overwrite these variables.
# PUID and PGID should match your user's values on the host machine; otherwise, you may encounter access issues.
ARG PUID=1000
ARG PGID=1000
ARG DOCKER_DIR=/local_docker
ARG WORK_DIR=/root_dir
ARG USER_GROUP=www-data
ARG INSTALL_XDEBUG=0
ARG TZ=Europe/Kyiv
ARG PHP_FPM_PORT=9000

WORKDIR $WORK_DIR

# Create a user and a group and assign them $PGID and $PUID.
RUN if getent passwd $USER_GROUP; then userdel $USER_GROUP; fi && \
    if getent group $USER_GROUP; then groupdel $USER_GROUP; fi && \
    groupadd -g $PGID $USER_GROUP && \
    useradd -u $PUID -g $USER_GROUP -m $USER_GROUP

# Copy all files and assign them to the owner and their group.
COPY --chown=$USER_GROUP:$USER_GROUP . $WORK_DIR

# Installation system with 3 layers
# 1. Installing system libraries
RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
        unzip \
        zlib1g-dev \
        libzip-dev \
        libpq-dev \
        procps \
        tzdata \
        curl \
        postgresql-client

# 2. Installing PHP extensions
RUN docker-php-ext-install \
        pdo_pgsql \
        bcmath \
        zip \
        pcntl && \
    pecl install redis && \
    docker-php-ext-enable redis

# 3. Clearing the apt cache
RUN rm -rf /var/lib/apt/lists/*

# End of system installation

# Installing xDebug
RUN if [ "$INSTALL_XDEBUG" = 1 ]; then \
        pecl install xdebug && docker-php-ext-enable xdebug; \
    fi

# Setting the time zone
ENV TZ=$TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo "$TZ" > /etc/timezone

COPY .$DOCKER_DIR/php-fpm/config/custom.ini $PHP_INI_DIR/conf.d/custom.ini
COPY .$DOCKER_DIR/php-fpm/config/xdebug.ini $PHP_INI_DIR/conf.d/xdebug.ini
COPY .$DOCKER_DIR/php-fpm/config/www.conf /usr/local/etc/php-fpm.d/www.conf

# Installing Composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_MEMORY_LIMIT=-1
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# Check for the presence of composer.json and execute commands; if the file is missing, simply skip.
RUN if [ -f "composer.json" ]; then \
        composer install --optimize-autoloader; \
    else \
        echo "No composer.json found, skipping installation."; \
    fi

USER $USER_GROUP

EXPOSE $PHP_FPM_PORT

CMD ["php-fpm"]

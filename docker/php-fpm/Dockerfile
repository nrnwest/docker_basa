FROM php:8.2-fpm

# If the image is being built and an .env file is specified during the build, it will overwrite these variables.
# PUID and PGID should match your user's values on the host machine; otherwise, you may encounter access issues.
ARG PUID=1000
ARG PGID=1000
ARG DOCKER_DIR=/docker
ARG WORK_DIR=/root_dir
ARG USER_GROUP=www-data
ARG INSTALL_XDEBUG=0
ARG TZ=Europe/Kyiv
ARG PHP_FPM_PORT=9000
ARG ENV_FILE=/.env.example

WORKDIR $WORK_DIR

# Create a user and a group and assign them $PGID and $PUID.
RUN if getent passwd $USER_GROUP; then userdel $USER_GROUP; fi && \
    if getent group $USER_GROUP; then groupdel $USER_GROUP; fi && \
    groupadd -g $PGID $USER_GROUP && \
    useradd -u $PUID -g $USER_GROUP -m $USER_GROUP

# Copy all files and assign them to the owner and their group.
COPY --chown=$USER_GROUP:$USER_GROUP . $WORK_DIR

# Installing system libraries
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
        postgresql-client \
        mc

# Installing PHP extensions
RUN docker-php-ext-install \
        pdo_pgsql \
        bcmath \
        zip \
        pcntl && \
    pecl install redis && \
    docker-php-ext-enable redis

# Clearing the apt cache
RUN rm -rf /var/lib/apt/lists/*


# Setting the time zone
ENV TZ=$TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo "$TZ" > /etc/timezone

# copy configs
COPY  $ENV_FILE .env
COPY .$DOCKER_DIR/php-fpm/config/custom.ini $PHP_INI_DIR/conf.d/custom.ini
COPY .$DOCKER_DIR/php-fpm/config/www.conf /usr/local/etc/php-fpm.d/www.conf

# Creating directories and setting permissions for writing
# $WORK_DIR/vendor $WORK_DIR/storage/logs $WORK_DIR/bootstrap/cache
# TODO по идеи эта секция не нужна, потому что эти все директории должны быть уже созданы и они будут скопированы с
# TODO новыми правами если потому будут отличатьсяч uid и нужно будем порпавить в сборке имиджа указав другие не 1000 а
# TODO текущие. С этим блокм поексперементируем на проекте с ларавлеь, скорее всего он не нужен.
RUN mkdir -p $WORK_DIR/vendor $WORK_DIR/storage $WORK_DIR/bootstrap && \
    chown -R $USER_GROUP:$USER_GROUP $WORK_DIR/vendor $WORK_DIR/storage $WORK_DIR/bootstrap && \
    chmod -R 775 $WORK_DIR/vendor $WORK_DIR/storage $WORK_DIR/bootstrap

# Installing Composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_MEMORY_LIMIT=-1
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# The file composer.json must already exist
RUN composer install --optimize-autoloader --no-dev --prefer-dist --no-interaction --no-progress

# laravel del all cache
#RUN php artisan optimize:clear

# remove the excess from the container
RUN rm -r  $WORK_DIR/docker $WORK_DIR/local_docker

USER $USER_GROUP

EXPOSE $PHP_FPM_PORT

CMD ["php-fpm"]

# Общие описание назначения и работы local_docker

## Максиальная моя сборка для работы php (моя)

```dockerfile
# Установка системы 3 слоя
# 1. Установка системных библиотек
RUN apt-get update -y && \
apt-get upgrade -y && \
apt-get install -y \
mc \
unzip \
zlib1g-dev \
libzip-dev \
libfreetype6-dev \
libpng-dev \
libjpeg-dev \
libpq-dev \
procps \
tzdata \
libicu-dev \
pkg-config \
curl \
gnupg \
postgresql-client

# 2. Установка PHP-расширений
RUN docker-php-ext-configure gd --with-jpeg && \
docker-php-ext-install \
gd \
pdo_pgsql \
bcmath \
zip \
pcntl \
intl && \
pecl install redis && \
docker-php-ext-enable redis

# 3. Очистка кеша apt
RUN rm -rf /var/lib/apt/lists/*

# Конец установки системы
```
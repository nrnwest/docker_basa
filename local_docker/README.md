# Общие описание назначения и работы local_docker
Данная сборка нужна будет для локальной разработки, 
то есть тут будут добавляться различные вещи для тестирования, и експерементов, и 
базы для контейнеров в docker.

## Как рботает эта сборка и в чем отлчие от docker (dev,stage, prod) 
Данная сброка для розрабоктки, главное отличие мы поключаем валюм ко все текущей директории проекта . !
Это делаеться для того чтоб данные автоматом попадали в контенер, файлы которые мы правим или создаем
автоматом попадают в контейнер. 
Это также накладывает огрничение мы не моожем в контейре задавать какие команды, типа развернуть композер
создать директории и так дадльше, потому что вальюм все застрет когда будет монтироватсья потому
мы после установки всех контейнеров локально устаналиваем композел и все остальное черзе командру 
make init
а она уже вызывает все методы которые нужы!


# Opcache
Невключаться по умолчанию ни  в каой сброки и это сделано специально чтоб в случаи чего не нарушало роаботу
потому мы и создаем кофниг который его включит.

# Volume базы данных
Так как мы соханяем базу данных локально, между сборками контейнера базы данных. 
Сами файлы мы выносим на уровень выше текущего проеокта в директории *db_названия_проекта*.
Выносим с директорий проекта так как мы назначаем права для всех файлов, этоавтоматически их примянло и для базы
данных что не коректно потому выносим на один уровень верх.

## Максиальная моя сборка для работы php (моя):

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
FROM php:8.5-fpm

# Set the working directory
WORKDIR /var/www/html

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    libicu-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libmagickwand-dev \
    imagemagick \
    zip \
    unzip \
    supervisor \
    nginx \
    netcat-openbsd \
    libcurl4-openssl-dev \
    libpq-dev \
    lsb-release \
    gnupg \
    wget \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add PGDG repo and install postgresql-client matching PostgreSQL 18
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/postgresql.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
    && apt-get update && apt-get install -y postgresql-client-18 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Configure PHP extensions
RUN docker-php-ext-configure intl
RUN docker-php-ext-configure gd --with-freetype --with-jpeg

# Install PHP extensions
RUN docker-php-ext-install \
    bcmath \
    exif \
    gd \
    intl \
    mbstring \
    pcntl \
    pdo_mysql \
    pdo_pgsql \
    sockets \
    zip

# Install Redis (build from source — PECL stable release may lag behind PHP 8.5 support)
RUN git clone --depth 1 https://github.com/phpredis/phpredis.git /tmp/phpredis \
    && cd /tmp/phpredis \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && docker-php-ext-enable redis \
    && rm -rf /tmp/phpredis

# Install Swoole (for Octane)
RUN pecl install swoole && docker-php-ext-enable swoole

# Install Imagick
RUN pecl install imagick && docker-php-ext-enable imagick

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

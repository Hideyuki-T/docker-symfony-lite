FROM php:8.2-fpm

RUN apt-get update && apt-get install -y \
    libonig-dev \
    libzip-dev \
    zip \
    unzip \
    curl \
  && docker-php-ext-install pdo_mysql mbstring zip \
  && apt-get clean

RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
 && apt-get install -y nodejs

COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer


WORKDIR /var/www/html

CMD ["php-fpm"]

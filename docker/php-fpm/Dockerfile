FROM php:7.4-fpm-alpine

RUN apk upgrade --update \
    && apk --no-cache add icu-dev autoconf make g++ gcc curl libzip-dev

RUN docker-php-ext-configure opcache --enable-opcache \
    && docker-php-ext-install pdo_mysql zip opcache

#RUN { \
#  echo 'fastcgi.logging = 1'; \
#  echo 'opcache.enable=1'; \
#  echo 'opcache.optimization_level=0x7FFFBBFF' ; \
#  echo 'opcache.revalidate_freq=180'; \
#  echo 'opcache.validate_timestamps=1'; \
#  echo 'opcache.memory_consumption=128'; \
#  echo 'opcache.interned_strings_buffer=8'; \
#  echo 'opcache.max_accelerated_files=4000'; \
#  echo 'opcache.fast_shutdown=1'; \
#} > /usr/local/etc/php/conf.d/overrides.ini

# composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# conf
COPY ./docker/php-fpm/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./docker/php-fpm/conf.d/customize.ini /usr/local/etc/php/conf.d/customize.ini

# Composer 依存パッケージ定義のコピー
COPY composer.* /var/www/html/
RUN composer install --working-dir=/var/www/html --no-scripts --no-autoloader

# app
COPY . /var/www/html
WORKDIR /var/www/html

RUN composer dump-autoload --working-dir=/var/www/html --no-scripts

RUN set -eux \
    && chmod -R a=rwX storage/ bootstrap/cache \
#    && composer install --no-scripts --no-autoloader \
#    && composer install --optimize-autoloader \
    && cp .env.example .env \
    && php artisan config:clear \
    && php artisan route:cache \
    && php artisan view:cache
#    && composer dump-autoload --working-dir=/var/www/html --no-scripts
EXPOSE 9000

# syntax=docker/dockerfile:1

ARG PHP_VERSION=8.3

FROM php:${PHP_VERSION}-apache-bookworm AS phpserver

ENV SYS_UID_MAX=1001 \
    APACHE_PORT=9000 \
    USER=application

# https://getcomposer.org/doc/00-intro.md#docker-image
COPY --from=composer/composer:2-bin /composer /usr/bin/composer

# zip is needed for composer
RUN useradd -u 1000 -g www-data -mr ${USER} \
    && a2dismod -f autoindex status env alias \
    && a2enmod rewrite headers expires session \
    && apt-get update -q \
    && apt-get install -q -y --no-install-recommends zip unzip libpq-dev \
    && docker-php-ext-install pdo_pgsql pgsql

# production ini and apache configs
COPY ./apache2/php.ini /usr/local/etc/php/php.ini
COPY ./apache2/ /etc/apache2/

USER ${USER}
WORKDIR /var/www/html

RUN echo '<?php phpinfo();' > index.php

EXPOSE 9000

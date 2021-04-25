FROM php:8.0.3-fpm AS base

ARG SERVICE_NAME=api
ENV SERVICE_NAME ${SERVICE_NAME}
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libzip-dev \
        libcurl4-openssl-dev \
        unzip \
        zip \
        vim \
        zlib1g-dev \
        && rm -rf /var/lib/apt/lists/* \
        && docker-php-ext-install -j$(nproc) gd zip curl

COPY .docker/scripts/ scripts/
RUN chmod -R +x ./scripts

RUN ./scripts/composer_install.sh

COPY .docker/config/php/php.ini.development $PHP_INI_DIR/php.ini

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

WORKDIR /var/www/${SERVICE_NAME}


FROM composer AS dependencies
COPY app/composer.json /app
COPY app/composer.lock /app
COPY app/symfony.lock /app
RUN composer install --ignore-platform-reqs --no-scripts
LABEL image=dependencies


FROM base
ARG BUILD_ENV=dev
ARG SERVICE_NAME=api
ENV SERVICE_NAME ${SERVICE_NAME}

COPY app /var/www/${SERVICE_NAME}
WORKDIR /var/www/${SERVICE_NAME}

COPY --from=dependencies app/vendor vendor

RUN bin/console ca:cl
RUN chmod -R 777 var/log var/cache

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD php-fpm -t || exit 1

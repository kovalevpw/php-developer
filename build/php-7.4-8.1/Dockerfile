# Define build arguments
ARG BASE_IMAGE \
    INSTALL_XDEBUG_VERSION=3.1.3 \
    INSTALL_COMPOSER_VERSION=2.2.6 \
    INSTALL_MEMCACHED_VERSION=3.1.5 \
    INSTALL_IMAGICK_VERSION=3.7.0

# Base image
FROM $BASE_IMAGE as base

ARG INSTALL_XDEBUG_VERSION \
    INSTALL_COMPOSER_VERSION

ENV COMPOSER_HOME /tmp

ADD https://raw.githubusercontent.com/composer/getcomposer.org/master/web/installer /tmp/composer-setup.php

RUN apt-get update && apt-get install -y --no-install-recommends \
        # For composer work
        unzip \
        git \
        # For build zip extension
        libzip-dev \
    && apt-get purge -y --auto-remove \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install zip \
    && docker-php-ext-install calendar \
    && pecl install xdebug-$INSTALL_XDEBUG_VERSION \
    && docker-php-ext-enable xdebug \
    && docker-php-source delete \
    && cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini \
    && sed -i 's/short_open_tag = Off/short_open_tag = On/' /usr/local/etc/php/php.ini \
    && sed -i 's/expose_php = On/expose_php = Off/' /usr/local/etc/php/php.ini \
    && php /tmp/composer-setup.php --install-dir=/usr/bin --filename=composer --version=$INSTALL_COMPOSER_VERSION

WORKDIR /app

EXPOSE 9000/tcp

# Yii2 framework image
FROM base as yii2

ARG INSTALL_MEMCACHED_VERSION \
    INSTALL_IMAGICK_VERSION

RUN apt-get update && apt-get install -y --no-install-recommends \
        # For build imap extension
        libc-client-dev \
        libkrb5-dev \
        # For build gd extension
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        # For build pdo_pgsql extension
        libpq-dev \
        # For build memcached extension
        libmemcached-dev \
        # For build imagick extension
        libmagickwand-dev \
    && apt-get purge -y --auto-remove \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd \
    && docker-php-ext-install intl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pdo_pgsql \
    && pecl install memcached-$INSTALL_MEMCACHED_VERSION \
    && docker-php-ext-enable memcached \
    && pecl install imagick-$INSTALL_IMAGICK_VERSION \
    && docker-php-ext-enable imagick \
    && docker-php-source delete

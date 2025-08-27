FROM php:8.4-fpm AS php

LABEL maintainer="Sakir Mehmetoglu <sakir.mehmetoglu@gmail.com>"
LABEL description="Bu imaj PHP 8.2 sürümünü içermektedir. PHP-FPM ile birlikte çalışır ve Composer, gerekli PHP uzantıları ve diğer araçlar ile önceden yapılandırılmıştır."
LABEL version="1.0"

ARG DEBIAN_FRONTEND=noninteractive
ENV COMPOSER_ALLOW_SUPERUSER=1

ENV PHP_OPCACHE_ENABLE=1
ENV PHP_OPCACHE_ENABLE_CLI=0
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS=0
ENV PHP_OPCACHE_REVALIDATE_FREQ=60

# Projenin konumu belirleniyor
WORKDIR /var/www
RUN rm -rf /var/www/*

# Gerekli Kaynaklar Hazırlanıyor..
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && sync

# Composer yükleniyor..
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Tüm kurulum işlemleri yapılıyor
RUN \
    # Temizlik Yapılıyor..
    apt-get clean autoclean && apt-get -y autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    \
    # Güncelleme Yapılıyor..
    && apt-get update -y \
    \
    # PHP Kütüphanelerinin Kurulması İçin Gerekli Hazırlık Yapılıyor..
    && chmod +x /usr/local/bin/install-php-extensions && sync \
    \
    # Gerekli Yazılımlar Kuruluyor..
    && apt-get install -y \
        procps \
        iputils-ping \
        telnet \
        openssl \
        libcurl4-gnutls-dev \
        build-essential \
        libpng-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
        libonig-dev \
        jpegoptim optipng pngquant gifsicle \
        vim \
        git \
        nano \
        gnupg \
        libssl-dev \
        apt-utils \
        libsodium-dev  \
        gosu \
        ca-certificates \
        supervisor \
        sqlite3 \
        libcap2-bin \
        nodejs npm \
        default-mysql-client \
        libmagickwand-dev \
    \
    # Zip
    && apt-get -y install libzip-dev zip unzip && docker-php-ext-configure zip && docker-php-ext-install zip \
    \
    ## PHP İçin Gerekli Kurulumlar Yapılıyor
    && install-php-extensions curl exif mbstring \
    \
    # ImageMagick
    && yes "" | pecl install imagick && docker-php-ext-enable imagick \
    \
    # Curl
    && apt-get install -y libcurl4-dev curl && docker-php-ext-install curl \
    \
    # GD
    && apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ && docker-php-ext-install gd \
   \
    # Locales
    && apt-get update -y && apt-get install -y locales && locale-gen tr_TR.UTF-8 && dpkg-reconfigure locales \
    \
    # Human Language and Character Encoding Support
    # Install PHP extensions intl (intl requires to be configured) \
    # Install icu dev libs support
    && apt-get install -y zlib1g-dev libicu-dev g++ && docker-php-ext-configure intl && docker-php-ext-install intl \
    \
    # BC Math
    && docker-php-ext-install bcmath \
    \
    # PCNTL (Process Control Extensions)
    && docker-php-ext-configure pcntl --enable-pcntl && docker-php-ext-install pcntl \
    \
    # Yarn
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/yarn.gpg && echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list && apt-get update -y && apt-get install -y yarn \
    \
    # PDO, MySQL and Postgres/pgsql
    && docker-php-ext-install pdo mysqli pdo_mysql && docker-php-ext-enable pdo_mysql \
    && apt-get install -y libpq-dev && docker-php-ext-configure pgsql --with-pgsql=/usr/local/pgsql && docker-php-ext-install pdo_pgsql pgsql \
    \
    # XDebug
    && pecl install xdebug && docker-php-ext-enable xdebug \
    \
    # OpCache
    && docker-php-ext-install -j$(nproc) opcache \
    \
    # Redis
    && pecl install redis && docker-php-ext-enable redis \
    \
    # Temizlik Yapılıyor..
    && apt-get clean autoclean && apt-get -y autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Locale Kurulumu Tamamlandıktan Sonra Gerekli Atamalar Yapılıyor
ENV LC_ALL=tr_TR.UTF-8 LANG=tr_TR.UTF-8 LANGUAGE=tr_TR.UTF-8


COPY ./php-fpm.ini /usr/local/etc/php/php.ini
COPY ./php-fpm.conf /usr/local/etc/php-fpm.d/www.conf

RUN echo '<?php\n\nphpinfo();' > index.php

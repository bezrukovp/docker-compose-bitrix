FROM php:7.1-fpm-alpine

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>

ENV TERM xterm


#####################################
# Non-Root User:
#####################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
RUN addgroup -g $PGID -S bitrix && \
    adduser -u $PUID -D -S -G bitrix bitrix && \
    echo "bitrix:$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)" | chpasswd


#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

RUN apk add --no-cache --virtual .persistent-deps \
        mc \
        icu-dev \
        libmcrypt-dev \
        libxml2-dev \
        zeromq \
        git \
        libmemcached \
        curl \
        libcurl \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        imagemagick-dev \
        pcre-dev \
    && apk add --no-cache --virtual .build-deps \
        autoconf \
        cmake \
        file \
        g++ \
        gcc \
        libc-dev \
        make \
        git \
        pkgconf \
        re2c \
        openssl-dev \
        zeromq-dev \
        libmemcached-dev \
        cyrus-sasl-dev \
        openssl-dev \
        curl-dev \
        libtool \
    && docker-php-source extract \
    && docker-php-ext-configure intl --enable-intl \
    && docker-php-ext-configure pcntl --enable-pcntl \
    && docker-php-ext-configure mbstring --enable-mbstring \
    && docker-php-ext-configure soap --enable-soap \
    && docker-php-ext-install \
        intl \
        mcrypt \
        pcntl \
        mbstring \
        mysqli \
        soap \
        zip \
    && git clone --branch php7 https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached/ \
    && docker-php-ext-configure memcached --disable-memcached-sasl \
    && docker-php-ext-install memcached \
    && docker-php-ext-configure curl \
    && docker-php-ext-install curl \
    && docker-php-ext-configure opcache --enable-opcache \
    && docker-php-ext-install opcache \
    && docker-php-ext-install iconv \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && curl -sS https://getcomposer.org/installer | php -- --filename=composer --install-dir=/usr/local/bin \
    && docker-php-source delete \
    && apk del .build-deps

COPY ./conf.d/ /usr/local/etc/php/conf.d/
COPY ./fpm.d/ /usr/local/etc/php/php-fpm.d/


#####################################
# Set msmtp
#####################################

RUN apk add --no-cache msmtp && \
    mkdir -p /var/log/msmtp && \
    chown bitrix:adm /var/log/msmtp && \
    mkdir -p /var/log/msmtp && \
    chown bitrix:adm /var/log/msmtp

ADD ./msmtprc /etc/msmtprc


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
RUN rm -rf /tmp/* /var/cache/apk/* /usr/src/*

# Set default work directory
WORKDIR /home/bitrix/www

USER bitrix

CMD ["php-fpm"]

EXPOSE 9000

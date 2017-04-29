FROM php:7.1-fpm

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>

RUN DEBIAN_FRONTEND=noninteractive
RUN locale-gen ru_RU.UTF-8

ENV LANGUAGE=ru_RU.UTF-8
ENV LC_ALL=ru_RU.UTF-8
ENV LC_CTYPE=ru_RU.UTF-8
ENV LANG=ru_RU.UTF-8
ENV TERM xterm

# Add the "PHP 7" ppa
RUN apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:ondrej/php

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

# Install "PHP Extentions", "libraries", "Software's"
RUN apt-get update && \
    apt-get install -y --allow-downgrades --allow-remove-essential \
        --allow-change-held-packages \
        php7.1-cli \
        php7.1-common \
        php7.1-curl \
        php7.1-json \
        php7.1-xml \
        php7.1-mbstring \
        php7.1-mcrypt \
        php7.1-mysql \
        php7.1-pgsql \
        php7.1-sqlite \
        php7.1-sqlite3 \
        php7.1-zip \
        php7.1-bcmath \
        php7.1-memcached \
        php7.1-gd \
        php7.1-dev \
        pkg-config \
        libcurl4-openssl-dev \
        libedit-dev \
        libssl-dev \
        libxml2-dev \
        xz-utils \
        libsqlite3-dev \
        sqlite3 \
        git \
        curl \
        vim \
        nano \
        mc \
        postgresql-client \
    && apt-get clean

COPY ./conf.d/ /usr/local/etc/php/conf.d/
COPY ./fpm.d/ /usr/local/etc/php/php-fpm.d/


#####################################
# Non-Root User:
#####################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
RUN groupadd -g $PGID bitrix && \
    useradd -u $PUID -g bitrix -m bitrix -s /bin/bash && \
    echo "developer:$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)" | chpasswd


#####################################
# Set Timezone
#####################################

ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#####################################
# Set msmtp
#####################################

RUN apt-get install -q -y msmtp && \
    mkdir -p /var/log/msmtp && \
    chown www-data:adm /var/log/msmtp && \
    mkdir -p /var/log/msmtp && \
    chown www-data:adm /var/log/msmtp && \

COPY ./msmtprc /etc/msmtprc
RUN sed -i 's/host mail/host '$(python -c 'import socket; print socket.gethostbyname("mail")')'/g' /etc/msmtprc


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Set default work directory
WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
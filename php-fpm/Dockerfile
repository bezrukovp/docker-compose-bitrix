FROM php:7.1-fpm

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>


ENV DEBIAN_FRONTEND noninteractive
ENV TERM xterm

#####################################
# Set Location & Timezone
#####################################

ARG lang=ru_RU.UTF-8
ARG language=ru_RU:ru
ARG tz=Europe/Moscow

RUN apt-get update && apt-get install -y apt-utils locales && \
    locale-gen --purge $lang en_US.UTF-8 && \
    sed -i -e "s/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/" /etc/locale.gen && \
    sed -i -e "s/# $lang UTF-8/$lang UTF-8/" /etc/locale.gen && \
    echo -e "LANG=\"$lang\"\nLANGUAGE=\"$language\"\n" > /etc/default/locale && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=$lang && \
    ln -sf /usr/share/zoneinfo/$tz /etc/localtime && \
    echo "$tz" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata

ENV LANG ${lang}
ENV LANGUAGE ${language}
ENV LC_ALL ${lang}
ENV LC_CTYPE=${lang}

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

# Install "PHP Extentions", "libraries", "Software's"
RUN apt-get install -y lsb-release software-properties-common python-software-properties \
    apt-transport-https ca-certificates wget curl git vim nano mc && \
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list && \
    apt-get update -yqq && \
    apt-get install -y \
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
    echo "bitrix:$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)" | chpasswd


#####################################
# Set msmtp
#####################################

RUN apt-get install -q -y msmtp && \
    mkdir -p /var/log/msmtp && \
    chown bitrix:adm /var/log/msmtp && \
    mkdir -p /var/log/msmtp && \
    chown bitrix:adm /var/log/msmtp

ADD ./msmtprc /etc/msmtprc
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

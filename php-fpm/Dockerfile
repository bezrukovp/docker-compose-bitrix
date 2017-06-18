FROM bezrukovp/php-fpm:7.1

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
    echo "bitrix:$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)" | chpasswd && \
    chown bitrix:adm /var/log/msmtp


#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

RUN rm /usr/local/etc/php-fpm.d/www.conf && \
    mkdir -p /var/tmp/php_sessions/www && \
    chown bitrix:adm /var/tmp/php_sessions/www && \
    mkdir -p /var/tmp/php_upload/www && \
    chown bitrix:adm /var/tmp/php_upload/www

COPY ./conf.d/ /usr/local/etc/php/conf.d/
COPY ./fpm.d/ /usr/local/etc/php-fpm.d/


#####################################
# xDebug:
#####################################

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    apt-get update && apt-get install -y php7.1-xdebug \
;fi

# Copy xdebug configration for remote debugging
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Set default work directory
WORKDIR /home/bitrix/www

EXPOSE 9000

CMD ["php-fpm"]


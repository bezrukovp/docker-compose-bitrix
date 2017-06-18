#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM bezrukovp/workspace-php:7.1

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

RUN dpkg-reconfigure openssh-server

#####################################
# Crontab
#####################################
USER root

COPY ./crontab /etc/cron.d
RUN chmod -R 644 /etc/cron.d


#####################################
# xDebug:
#####################################

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    apt-get update && apt-get install -y php7.1-xdebug && \
    echo "GatewayPorts=clientspecified" >> /etc/ssh/sshd_config \
;fi

# Copy xdebug configration for remote debugging
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
USER root
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set default work directory
WORKDIR /home/bitrix/www

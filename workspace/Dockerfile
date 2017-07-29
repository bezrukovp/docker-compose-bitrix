#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM bezrukovp/workspace-php:7.1

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>

ENV PHP_VERSION=7.1


#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

ADD ./php-fpm/ssmtp /etc/ssmtp
ADD ./php-fpm/conf.d/ /etc/php/${PHP_VERSION}/mods-available/
ADD ./workspace/supervisor/conf.d/* /etc/supervisor/conf.d/

RUN ln -s /etc/php/${PHP_VERSION}/mods-available/bitrixenv.ini /etc/php/${PHP_VERSION}/cli/conf.d/

#####################################
# ssh:
#####################################

RUN rm -f /etc/service/sshd/down && \
    dpkg-reconfigure openssh-server && \

    cat /dev/zero | ssh-keygen -q -N "" && \
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys && \
    chmod 644 /root/.ssh/authorized_keys /root/.ssh/id_rsa.pub && \
    chmod 400 /root/.ssh/id_rsa && \

    cat /dev/zero | sudo -u developer ssh-keygen -q -N "" && \
    cat /home/developer/.ssh/id_rsa.pub >> /home/developer/.ssh/authorized_keys && \
    chown developer:developer /home/developer/.ssh/authorized_keys && \
    chmod 644 /home/developer/.ssh/authorized_keys /home/developer/.ssh/id_rsa.pub && \
    chmod 400 /home/developer/.ssh/id_rsa


#####################################
# Crontab
#####################################
USER root

ADD ./workspace/crontab /etc/cron.d
RUN chmod -R 644 /etc/cron.d


#####################################
# xDebug:
#####################################

# Copy xdebug configration for remote debugging
ADD ./workspace/xdebug.ini /etc/php/${PHP_VERSION}/mods-available/xdebug-ext.ini

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    apt-get update && apt-get install -y php${PHP_VERSION}-xdebug && \
    sed -i "s/xdebug\.remote_enable=0/xdebug.remote_enable=1/" /etc/php/${PHP_VERSION}/mods-available/xdebug-ext.ini && \
    ln -s /etc/php/${PHP_VERSION}/mods-available/xdebug-ext.ini /etc/php/${PHP_VERSION}/cli/conf.d/ && \
    echo "GatewayPorts=clientspecified" >> /etc/ssh/sshd_config \
;fi


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set default work directory
WORKDIR /home/bitrix

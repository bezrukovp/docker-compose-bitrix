#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM phusion/baseimage:latest

MAINTAINER Pavel Bezrukov <bezrukov.ps@gmail.com>

RUN DEBIAN_FRONTEND=noninteractive
RUN locale-gen ru_RU.UTF-8

ENV LANGUAGE=ru_RU.UTF-8
ENV LC_ALL=ru_RU.UTF-8
ENV LC_CTYPE=ru_RU.UTF-8
ENV LANG=ru_RU.UTF-8
ENV TERM xterm

ARG NGINX_PUSH_STREAM_MODULE_VERSION=0.4.1


#####################################
# Nginx User:
#####################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
RUN groupadd -g $PGID bitrix && \
    useradd -u $PUID -g bitrix -m bitrix -s /bin/bash && \
    echo "bitrix:$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 13)" | chpasswd


#####################################
# Install base apps & Nginx Build:
#####################################

COPY ./sources.list.d/* /etc/apt/sources.list.d/

RUN apt-get update && apt-get install -y sudo git curl vim nano mc rsync tar \
    iputils-ping telnet dnsutils tcpdump htop \
    dpkg-dev build-essential zlib1g-dev libpcre3 libpcre3-dev && \
    mkdir -p ~/workspace/ && \
    cd ~/workspace/ && \
    wget https://github.com/wandenberg/nginx-push-stream-module/archive/${NGINX_PUSH_STREAM_MODULE_VERSION}.tar.gz && \
    tar -zxf ${NGINX_PUSH_STREAM_MODULE_VERSION}.tar.gz && \
    git clone https://github.com/openresty/headers-more-nginx-module.git && \
    apt-get source nginx && \
    apt-get build-dep nginx && \
    cd nginx-*/debian && \
    sed -i.orig 's#--with-cc-opt# --add-module=../../nginx-push-stream-module-${NGINX_PUSH_STREAM_MODULE_VERSION} --add-module=../../headers-more-nginx-module --with-cc-opt#g' rules && cd .. && \
    dpkg-buildpackage -b && \
    dpkg -i *.deb && \
    nginx -V

COPY ./bitrixenv_error/ /var/www/bitrixenv_error/

CMD ["nginx"]

EXPOSE 80 443 8893 8894 8895

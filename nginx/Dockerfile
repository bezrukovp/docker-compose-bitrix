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

ADD ./sources.list.d/* /etc/apt/sources.list.d/

RUN apt-get update && apt-get install -y sudo git curl vim nano mc rsync tar wget \
    iputils-ping telnet dnsutils tcpdump htop debian-keyring \
    dpkg-dev build-essential zlib1g-dev libpcre3 libpcre3-dev && \
    mkdir -p /opt/nginx/ && \
    cd /opt/nginx/ && \
    wget https://github.com/wandenberg/nginx-push-stream-module/archive/${NGINX_PUSH_STREAM_MODULE_VERSION}.tar.gz && \
    tar -zxf ${NGINX_PUSH_STREAM_MODULE_VERSION}.tar.gz && \
    git clone https://github.com/openresty/headers-more-nginx-module.git
RUN wget https://nginx.ru/keys/nginx_signing.key && apt-key add nginx_signing.key && rm nginx_signing.key && \
    echo "install key" && \
    apt-get update && echo "success update" && apt-get source nginx && echo "success source" && \
    apt-get build-dep -y nginx && \
    cd nginx-*/debian && \
    sed -i.orig 's#--with-cc-opt# --add-module=/opt/nginx/nginx-push-stream-module-${NGINX_PUSH_STREAM_MODULE_VERSION} --add-module=/opt/nginx/headers-more-nginx-module --with-cc-opt#g' rules && cd .. && \
    dpkg-buildpackage -b && cd .. && \
    dpkg -i nginx_*.deb && \
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log && \
    nginx -V

ADD ./bitrixenv_error/ /var/www/bitrixenv_error/

CMD ["nginx", "-g", "daemon off;"]

EXPOSE 80 443 8893 8894 8895

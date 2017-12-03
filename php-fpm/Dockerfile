#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM alpine:3.7

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

ARG INSTALL_BCMATH=false
ARG INSTALL_EXIF=false
ARG INSTALL_GD=false
ARG INSTALL_IMAGEMAGICK=false
ARG INSTALL_INTL=false
ARG INSTALL_LDAP=false
ARG INSTALL_MEMCACHED=false
ARG INSTALL_MYSQLI=false
ARG INSTALL_OPCACHE=false
ARG INSTALL_OPENSSL=false
ARG INSTALL_PDO_MYSQL=false
ARG INSTALL_PDO_PGSQL=false
ARG INSTALL_PDO_SQLITE=false
ARG INSTALL_POSIX=false
ARG INSTALL_REDIS=false
ARG INSTALL_SOAP=false
ARG INSTALL_TOKENIZER=false
ARG INSTALL_XDEBUG=false
ARG INSTALL_ZIP_ARCHIVE=false

# Build file for install php extensions
RUN set -e \
    ; echo \
        php7-ctype \
        php7-curl \
        php7-dom \
        php7-fileinfo \
        php7-fpm \
        php7-iconv \
        php7-json \
        php7-mbstring \
        php7-pdo \
        php7-pear \
        php7-phar \
        php7-session \
        php7-simplexml \
        php7-xml \
        php7-xmlreader \
        php7-xmlwriter \
        > /tmp/php-extensions \
    \
    ; if [ ${INSTALL_BCMATH} = true ]; then \
        echo php7-bcmath >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_EXIF} = true ]; then \
        echo php7-exif >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_GD} = true ]; then \
        echo php7-gd >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
        echo php7-imagick >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_INTL} = true ]; then \
        echo php7-intl >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_LDAP} = true ]; then \
        echo php7-ldap >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_MEMCACHED} = true ]; then \
        echo php7-memcached >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_MYSQLI} = true ]; then \
        echo php7-mysqli >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_OPCACHE} = true ]; then \
        echo php7-opcache >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_OPENSSL} = true ]; then \
        echo php7-openssl >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_PDO_MYSQL} = true ]; then \
        echo php7-pdo_mysql >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_PDO_PGSQL} = true ]; then \
        echo php7-pdo_pgsql >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_PDO_SQLITE} = true ]; then \
        echo php7-pdo_sqlite >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_POSIX} = true ]; then \
        echo php7-posix >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_REDIS} = true ]; then \
        echo php7-redis >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_SOAP} = true ]; then \
        echo php7-soap >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_TOKENIZER} = true ]; then \
        echo php7-tokenizer >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_XDEBUG} = true ]; then \
        echo php7-xdebug >> /tmp/php-extensions \
    ; fi \
    ; if [ ${INSTALL_ZIP_ARCHIVE} = true ]; then \
        echo php7-zip >> /tmp/php-extensions \
    ; fi

# Install dependenses, php extentions, build dependenses
RUN set -e \
    && addgroup -g 1000 www \
    && adduser -D -H -u 1000 -s /bin/bash -G www www \
    && apk update \
    && apk upgrade \
    && apk add --no-cache \
        ca-certificates \
        openssl \
        bash \
        curl \
        tar \
        xz \
    && apk add --no-cache `cat /tmp/php-extensions` \
    && apk add --no-cache --virtual .build-deps \
        autoconf \
        coreutils \
        curl-dev \
        dpkg-dev dpkg \
        file \
        g++ \
        gcc \
        libc-dev \
        libedit-dev \
        libxml2-dev \
        make \
        openssl-dev \
        pkgconf \
        re2c \
        sqlite-dev \
        php7-dev

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
# Example:
#   - INSTALL_MONGO=true
#

#####################################
# MongoDB:
#####################################

ARG INSTALL_MONGO=false
RUN if [ ${INSTALL_MONGO} = true ]; then \
    pecl install mongodb \
    && echo 'extension=mongodb.so' > /etc/php7/conf.d/02_mongodb.ini \
;fi

#####################################
# Opcache:
#####################################

# Copy opcache configration
COPY ./opcache.ini /etc/php7/conf.d/00_opcache.ini
RUN if [ ${INSTALL_OPCACHE} = false ]; then \
    rm -f /etc/php7/conf.d/00_opcache.ini \
;fi

#####################################
# xDebug:
#####################################

# Copy xdebug configuration for remote debugging
COPY ./xdebug.ini /etc/php7/conf.d/00_xdebug.ini
RUN if [ ${INSTALL_XDEBUG} = false ]; then \
    rm -f /etc/php7/conf.d/00_xdebug.ini \
;fi

#
#--------------------------------------------------------------------------
# Clean
#--------------------------------------------------------------------------
#

RUN set -e \
    && apk del .build-deps \
    && rm -f /etc/php7/php-fpm.d/www.conf \
    && rm -f /tmp/php-extensions \
    && rm -rf /var/cache/apk/* \
    && rm -rf /tmp/pear

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

ARG PHP_CONFIG_ENV=production
COPY "php.ini-${PHP_CONFIG_ENV}" /etc/php7/php.ini

ADD ./www.ini /etc/php7/conf.d
ADD ./www.pool.conf /etc/php7/php-fpm.d/

WORKDIR /var/www

CMD ["php-fpm7", "-F"]

EXPOSE 9000

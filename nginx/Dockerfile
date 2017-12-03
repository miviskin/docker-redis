#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM nginx:alpine

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

RUN set -e \
    && apk update \
    && apk upgrade \
    && apk add --no-cache \
		ca-certificates \
        bash \
    && addgroup -g 1000 www \
    && adduser -D -H -u 1000 -s /bin/bash -G www www 

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

ARG PHP_UPSTREAM_CONTAINER=php-fpm
ARG PHP_UPSTREAM_PORT=9000

# Set upstream conf and remove the default conf
RUN set -e \
    && rm -f /etc/nginx/conf.d/default.conf \
    && echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf

COPY ./nginx.conf /etc/nginx/nginx.conf

EXPOSE 80 443

FROM alpine:3.15

ARG PHP_VERS=7.4.29-r0

RUN apk add --no-cache \
        gettext=0.21-r0 \
        curl=7.80.0-r1 \
        nginx=1.20.2-r1 \
        php7=$PHP_VERS \
        php7-fpm=$PHP_VERS \
        php7-opcache=$PHP_VERS \
        php7-pdo=$PHP_VERS \
        php7-pdo_sqlite=$PHP_VERS \
        php7-openssl=$PHP_VERS && \
    mkdir /var/www/html

COPY . /var/www/html

RUN chmod -R 775 /var/www/html && \
    chown -R nginx:nginx /var/www/html

COPY docker/php-fpm.conf /etc/php7/php-fpm.conf
COPY docker/nginx.conf /etc/nginx/nginx.conf
COPY docker/entrypoint.sh /entrypoint.sh

RUN mkdir -p /var/run/nginx && \
    mkdir -p /var/run/php-fpm && \
    chown -R nginx:nginx /var/run/ && \
    chmod +x /entrypoint.sh
VOLUME /var/www/html/data
ENTRYPOINT ["/bin/ash", "/entrypoint.sh"]

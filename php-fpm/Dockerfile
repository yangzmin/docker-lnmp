###########################################################################
# PHP-FPM Version Of Alpine:
###########################################################################

ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="Yangzm"


###########################################################################
# Alpine Aliyun Mirrors:
###########################################################################

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories


###########################################################################
# System Extensions:
###########################################################################

RUN apk --update add \
        autoconf \
        build-base \
        linux-headers \
        libaio-dev \
        zlib-dev \
        curl \
        git \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libtool \
        libbz2 \
        bzip2 \
        bzip2-dev \
        libstdc++ \
        libxslt-dev \
        openldap-dev \
        imagemagick-dev \
        libzip-dev \
        make \
        zip \
        unzip \
        wget \
        vim


###########################################################################
# PHP Core Extensions:
###########################################################################

# Core Extensions For Default Installation:

RUN docker-php-ext-install bcmath
RUN docker-php-ext-install gd
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install exif

# Core Extensions pcntl:
ARG INSTALL_PCNTL=false
RUN if [ ${INSTALL_PCNTL} = true ]; then \
    docker-php-ext-install pcntl \
;fi


# Core Extensions zip:
ARG INSTALL_ZIP=false
RUN if [ ${INSTALL_ZIP} = true ]; then \
    if [ ${PHP_VERSION} = "7.3" ] || [ ${PHP_VERSION} = "7.4" ]; then \
        docker-php-ext-configure zip \
    else \
        docker-php-ext-configure zip --with-libzip \
    ;fi \
    && docker-php-ext-install zip \
;fi


###########################################################################
# PHP PECL Extensions:
###########################################################################

# PECL Extensions reids:
ARG INSTALL_REDIS=false
ARG INSTALL_REDIS_VERSION
RUN if [ ${INSTALL_REDIS} = true ]; then \
    pecl install redis-${INSTALL_REDIS_VERSION} \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
;fi


# PECL Extensions swoole:
# ARG INSTALL_SWOOLE=false
# ARG INSTALL_SWOOLE_VERSION
# RUN if [ ${INSTALL_SWOOLE} = true ]; then \
#     pecl install swoole-${INSTALL_SWOOLE_VERSION} \
#     && rm -rf /tmp/pear \
#     && docker-php-ext-enable swoole \
# ;fi


###########################################################################
# PHP Thrid Extensions:
###########################################################################


# 安装 Swoole
RUN cd /home \
    && git clone https://gitee.com/swoole/swoole.git \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl -with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini

# Composer:
ARG INSTALL_COMPOSER=false
RUN if [ ${INSTALL_COMPOSER} = true ]; then \
    curl -sS http://getcomposer.org/installer | php \
    && mv ./composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
;fi

RUN apk del build-base \
    	linux-headers \
    	libaio-dev \
    && rm -rf /var/cache/apk/*


VOLUME /var/www
WORKDIR /var/www


EXPOSE 9000
CMD ["php-fpm"]
# COPY run.sh /run.sh
# ENTRYPOINT ["/bin/bash", "/run.sh"]
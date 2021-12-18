FROM php:8.1.1-fpm

ARG ATOMIC_UID
ARG ATOMIC_GID

USER root
WORKDIR /srv

# https://laravel.com/docs/8.x/deployment#server-requirements
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update --fix-missing

# Composer
COPY --from=composer:2.2 /usr/bin/composer /usr/local/bin/composer

# Git
RUN apt-get install -y git

# Supervisor
RUN apt-get install -y supervisor

# Intl Extension
RUN apt-get install -y libicu-dev \
 && docker-php-ext-configure intl \
 && docker-php-ext-install intl

# PDO Extension
RUN apt-get install -y libsqlite3-dev \
 && docker-php-ext-install pdo \
 && docker-php-ext-install pdo_mysql \
 && docker-php-ext-install pdo_sqlite

# GD Extension
RUN apt-get install -y libpng-dev \
 && docker-php-ext-configure gd \
 && docker-php-ext-install -j$(nproc) gd

# Imagick Extension
ARG IMAGICK_LAST_COMMIT='c9ac1bc8e20266775d1032b11bf5f58a5b04551c'
RUN apt-get install -y libmagickwand-dev \
 && mkdir -p /usr/src/php/ext/imagick \
 && curl -fsSL https://github.com/Imagick/imagick/archive/${IMAGICK_LAST_COMMIT}.tar.gz | tar xvz -C /usr/src/php/ext/imagick --strip 1 \
 && docker-php-ext-install imagick

# ZIP Extension
RUN apt-get install -y libzip-dev \
 && docker-php-ext-install zip

# Redis Extension
RUN pecl install -o -f redis \
 && rm -rf /tmp/pear \
 && docker-php-ext-enable redis

# Exif Extension
RUN docker-php-ext-install exif \
 && docker-php-ext-enable exif

# Process Control Extension
RUN docker-php-ext-install pcntl \
 && docker-php-ext-enable pcntl

# BC Math Extension (installed by base image)
# RUN docker-php-ext-install bcmath

# Ctype Extension (installed by base image)
# RUN docker-php-ext-install ctype

# Fileinfo Extension (installed by base image)
# RUN docker-php-ext-install fileinfo

# JSON Extension (installed by base image)
# RUN docker-php-ext-install json

# Multibyte String Extension (installed by base image)
# RUN docker-php-ext-install mbstring

# OpenSSL Extension (installed by base image)

# Tokenizer Extension (installed by base image)
# RUN docker-php-ext-install tokenizer

# XML Extension (installed by base image)
# RUN apt-get install -y libxml2-dev \
#  && docker-php-ext-install xml \
#  && docker-php-ext-install simplexml

# User Management
RUN groupadd --force -g "${ATOMIC_GID}" supervisor \
 && useradd -ms /bin/bash --no-user-group -g "${ATOMIC_GID}" -u "${ATOMIC_UID}" atomic \
 && usermod -a atomic -G supervisor \
 && mkdir -p /home/atomic/.composer \
 && chown -R atomic:supervisor /home/atomic/.composer

RUN docker-php-source delete

CMD ["/usr/bin/supervisord"]

# Set the base image for subsequent instructions 
FROM php:7.4-apache
# Update packages
RUN apt-get -y update --fix-missing --no-install-recommends
RUN apt-get -y upgrade

# Install useful tools
RUN apt-get -yq install apt-utils nano wget dialog

# Install PHP and composer dependencies
RUN apt-get install -qq git curl sudo zip unzip libzip-dev libjpeg-dev libpng-dev libfreetype6-dev libbz2-dev libc-client-dev libkrb5-dev

# Install xdebug
RUN pecl install xdebug-2.6.0
RUN docker-php-ext-enable xdebug

# Clear out the local repository of retrieved package files
RUN apt-get -y clean all && apt-get -y update && apt-get -y dist-upgrade

# 1. development packages
RUN apt-get install -y  --fix-missing \
    libicu-dev \
    libmcrypt-dev \
    libreadline-dev \
    software-properties-common\
    g++

RUN apt-get clean

# 2. apache configs + document root
ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

#Fixing "Package requirements (oniguruma) were not met" for mbstring
RUN apt-get -y install libonig-dev

# Install needed extensions
# Here you can install any other extension that you need during the test and deployment process
RUN apt-get update \
 && apt-get install -y git zlib1g-dev \
 && docker-php-ext-install mysqli pdo pdo_mysql && docker-php-ext-enable pdo_mysql

#Enable PDO extenstion from php.ini, we areleady in development mode
RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
RUN sed -i 's/;extension=pdo_mysql/extension=pdo_mysql/g' /usr/local/etc/php/php.ini


RUN docker-php-ext-install pdo_sqlite
RUN docker-php-ext-install mysqli

RUN docker-php-ext-install tokenizer
RUN docker-php-ext-install json

RUN docker-php-ext-install -j$(nproc) intl
RUN docker-php-ext-install gettext

RUN apt-get install -y libjpeg62-turbo-dev
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-install mbstring zip && rm -r /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap

# Enable apache modules
RUN a2enmod rewrite headers

RUN docker-php-ext-install \
    bz2 \
    iconv \
    bcmath \
    opcache \
    calendar

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
# show that both Composer and PHP run as expected
RUN composer --version && php -v
#COPY . /var/www/html

WORKDIR /var/www/html

RUN useradd -G www-data,root -u 1000 -d /home/devuser devuser

WORKDIR /var/www/html/

#FROM egahmad/phpforlaravel AS Builder
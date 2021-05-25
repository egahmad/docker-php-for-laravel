# Set the base image for subsequent instructions
FROM php:7.2-apache

# Update packages
RUN apt-get -y update --fix-missing --no-install-recommends
RUN apt-get -y upgrade

#COPY conf/000-default.conf /etc/apache2/sites-available/
#COPY conf/default-ssl.conf /etc/apache2/sites-available/
#COPY conf/ssl-params.conf /etc/apache2/conf-available/
#COPY conf/dir.conf /etc/apache2/mods-enabled/
#COPY conf/ports.conf /etc/apache2/
 
RUN apt-get update && apt-get install -y \
  cron \
  git \
  gzip \
  libbz2-dev \
  libfreetype6-dev \
  libicu-dev \
  libjpeg62-turbo-dev \
  libmcrypt-dev \
  libonig-dev \
  libpng-dev \
  libsodium-dev \
  libssh2-1-dev \
  libxslt1-dev \
  libzip-dev \
  lsof \
  default-mysql-client \
  vim \
  zip \
  procps

#RUN docker-php-ext-configure gd --with-freetype --with-jpeg

RUN docker-php-ext-install \
  bcmath \
  bz2 \
  calendar \
  exif \
  gd \
  gettext \
  intl \
  mbstring \
  mysqli \
  opcache \
  pcntl \
  pdo_mysql \
  soap \
  sockets \
  sodium \
  sysvmsg \
  sysvsem \
  sysvshm \
  xsl \
  zip

# Enable SSL in Apache
RUN a2enmod ssl \
    && a2enmod headers \
    && a2enmod rewrite \
    #&& a2dismod mpm_prefork \
    #&& a2dismod mpm_event \
    #&& a2enmod mpm_worker \
    #&& a2enmod proxy_fcgi \
    #&& a2enmod http2 \
    #&& a2enmod proxy \
    #&& a2enmod proxy_http \
    && a2enmod remoteip
    #&& a2ensite default-ssl \
    #&& a2enconf ssl-params
    #&& a2enconf php7.4-fpm \
    #&& c_rehash /etc/ssl/certs/

# Install useful tools
RUN apt-get -yq install \
    apt-utils \
    dialog \
    nano \
    wget \
    cron \
    sudo \
    zip \
    unzip

# Install PHP and composer dependencies
RUN apt-get install -qq \
    git \
    libzip-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libbz2-dev \
    libc-client-dev \
    libkrb5-dev

# Add Easy installation of PHP extensions in official PHP Docker images - The fucking problems solver for me
# https://github.com/mlocati/docker-php-extension-installer
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Install needed extensions
# Here you can install any other extension that you need during the test and deployment process
RUN apt-get update \
    && apt-get install -y zlib1g-dev \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install gd \
    && docker-php-ext-install zip \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl && docker-php-ext-install imap

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd

# Install xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

# Install Composer
COPY --from=composer:1.10.20 /usr/bin/composer /usr/local/bin/composer

RUN apt-get clean
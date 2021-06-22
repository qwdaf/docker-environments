FROM php:7.4.20-apache as base

EXPOSE 80

#
#--------------------------------------------------------------------------
# update api-get module
#--------------------------------------------------------------------------
#

RUN apt-get update -y
RUN apt-get install -y pkg-config
RUN apt-get install -y libcurl4-openssl-dev
RUN apt-get install -y libssl-dev
RUN apt-get install -y libmcrypt-dev
RUN apt-get install -y libjpeg62-turbo-dev
RUN apt-get install -y libpng-dev
RUN apt-get install -y libzip-dev
RUN apt-get install -y libfreetype6-dev
RUN apt-get install -y libonig-dev
RUN apt-get install -y libicu-dev

#
#--------------------------------------------------------------------------
# Install basic extensions
#--------------------------------------------------------------------------
#

RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install gd
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install zip
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install pcntl
RUN docker-php-ext-install intl
RUN docker-php-ext-install opcache
RUN docker-php-ext-install pdo_mysql
RUN apt-get clean

#
#--------------------------------------------------------------------------
# Install other extensions
#--------------------------------------------------------------------------
#

#####################################
# PHP MongoDB:
#####################################

RUN pecl install mongodb --with-ssl
RUN docker-php-ext-enable mongodb

#####################################
# PHP Redis:
#####################################

RUN pecl install redis 
RUN docker-php-ext-enable redis

#
#--------------------------------------------------------------------------
# Enable rewrite
#--------------------------------------------------------------------------
#
RUN a2enmod rewrite

FROM base as builder

WORKDIR /var/www/html

#
#--------------------------------------------------------------------------
# 安装 PHP composer
#--------------------------------------------------------------------------
#
RUN curl -sL https://getcomposer.org/installer | php -- --install-dir /usr/bin --filename composer

ENV APACHE_DOCUMENT_ROOT /var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

CMD [ "apache2-foreground" ]

# Dockerfile
FROM php:5.6-apache

#Update Packages
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libxml2-dev

#Install PHP modules
RUN docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install soap
RUN a2enmod rewrite

#Copy Zend.SO for PHP 5.6
COPY ./zendconf /zendconf
COPY /zendconf/php.ini  /usr/local/etc/php/
COPY /zendconf/ZendGuardLoader.so /home
COPY /zendconf/opcache.so /home

#Restart Apache2 and it's done.
RUN service apache2 restart
EXPOSE 80
FROM       php:8.2-fpm
WORKDIR /var/www

################################################################################
##
##      Install dependencies
##
RUN apt-get update \
     && apt-get install -y sqlite3 nano rsync mariadb-client iputils-ping libssh2-1-dev libssh2-1 \
     && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pecl install ssh2-1.3.1 \
    && docker-php-ext-enable ssh2




# Install composer
RUN         curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


################################################################################
##
##      Set up the file system
##

USER    1000
EXPOSE  9000
CMD     ["php-fpm"]
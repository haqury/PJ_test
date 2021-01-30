FROM php:7.3-fpm
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libxml2-dev \
        zlib1g-dev \
        libzip-dev \
        mariadb-client \
        git
RUN pecl install xdebug-2.9.6 && docker-php-ext-enable xdebug
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN pecl install mcrypt-1.0.3 && docker-php-ext-enable mcrypt
RUN docker-php-ext-install mbstring pdo_mysql tokenizer gd iconv mysqli zip soap
RUN usermod -u 1000 www-data
RUN groupmod -g 1000 www-data
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#nodejs
RUN apt-get install -my wget gnupg
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs build-essential
RUN npm install -g webpack
RUN npm install -g webpack-cli

# Добавим свой php.ini, можем в нем определять свои значения конфига
ADD php.ini /usr/local/etc/php/conf.d/40-custom.ini

#понижаем версию TLS
RUN sed -i "s|MinProtocol =|#MinProtocol =|g" /usr/lib/ssl/openssl.cnf
RUN sed -i "s|CipherString =|#CipherString =|g" /usr/lib/ssl/openssl.cnf

# Запускаем контейнер
# Из документации: The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable,
# or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.
CMD ["php-fpm"]

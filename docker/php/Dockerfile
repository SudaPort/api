FROM php:7.0-fpm

ENV PHPREDIS_VERSION php7

RUN mkdir -p /usr/share/man/man1 /usr/share/man/man7
RUN apt-get update && apt-get install -y git libssl-dev cron libpq-dev postgresql-client libxml2-dev memcached libmemcached-dev \
    && apt-get install -y libzip-dev zlib1g-dev wget \
    && docker-php-ext-install pgsql \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-ext-install soap \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install zip \
    && git clone --depth=1 -b 3.0.x git://github.com/phalcon/cphalcon.git /usr/local/src/cphalcon \
    && cd /usr/local/src/cphalcon/build && ./install \
    && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/25-phalcon.ini \
    && git clone git://github.com/wedancedalot/php-ed25519-ext.git \
    && cd php-ed25519-ext && phpize && ./configure && make && make install \
    && echo "extension=ed25519.so" > /usr/local/etc/php/conf.d/30-ed25519.ini \
    && git clone -b php7 https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached \
    && cd /usr/src/php/ext/memcached && phpize && ./configure && make && make install \
    && echo "extension=memcached.so" > /usr/local/etc/php/conf.d/30-memcached.ini

    # Redis
RUN curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/5.3.4.tar.gz \
    && mkdir -p /usr/src/php/ext/ \
    && tar xfz /tmp/redis.tar.gz \
    && rm -r /tmp/redis.tar.gz \
    && mv phpredis-5.3.4 /usr/src/php/ext/redis \
    && docker-php-ext-install redis \
    && wget https://getcomposer.org/installer && php installer --install-dir=/usr/local/bin --filename=composer

# Add crontab file in the cron directory
ADD config/crontab /crontab_tmp

# Add php.ini
ADD config/php.ini /usr/local/etc/php/php.ini

ADD ./entrypoint.sh /

ENV PATH $PATH:/root/.composer/vendor/bin
RUN chmod u+x+r+w /entrypoint.sh
CMD ["/entrypoint.sh"]
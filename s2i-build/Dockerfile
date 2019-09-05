FROM php:7.2-fpm

LABEL maintainer="Lakshmi Narasimhan <lakshmi@shapeblock.com>"

LABEL io.k8s.description="Base image for Drupal 8" \
      io.k8s.display-name="OpenShift Drupal 8" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.openshift.tags="builder, php-fpm, php-7.2, Drupal 8"

RUN apt-get update \
    && apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev wget git curl python unzip mysql-client

RUN  docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
     && docker-php-ext-install gd \
     && :\
     && docker-php-ext-install pdo pdo_mysql opcache zip \
     && docker-php-ext-enable pdo pdo_mysql opcache zip

COPY ./s2i/bin/ /usr/libexec/s2i

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN set -ex; \
    \
    # Drush
    composer global require drush/drush; \
    \
    # Drush launcher
    wget -O drush.phar \
        "https://github.com/drush-ops/drush-launcher/releases/download/0.6.0/drush.phar"; \
    chmod +x drush.phar; \
    mv drush.phar /usr/local/bin/drush; \
    \
    # Drupal console
    curl https://drupalconsole.com/installer -L -o drupal.phar; \
    mv drupal.phar /usr/local/bin/drupal; \
    chmod +x /usr/local/bin/drupal; \
    \
    # Clean up
    composer clear-cache;

# /app will map to nginx container
RUN useradd -u 1001 -r -g 0 -d /app -s /bin/bash -c "Default Application User" default \
    && mkdir -p /app \
    && chown -R 1001:0 /app && chmod -R g+rwX /app

# This is where the source code will be cloned.
RUN mkdir /code && chown -R 1001:0 /code && chmod -R g+rwX /code

# Drupal files directory will map to this.
RUN mkdir /shared && chown -R 1001:0 /shared && chmod -R g+rwX /shared

# Bump up PHP limitations
RUN sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 100M/g' $PHP_INI_DIR/php.ini-production \
    && sed -i 's/post_max_size = 8M/post_max_size = 100M/g' $PHP_INI_DIR/php.ini-production \
    && sed -i 's/max_execution_time = 30/max_execution_time = 300/g' $PHP_INI_DIR/php.ini-production \
    && mv $PHP_INI_DIR/php.ini-production $PHP_INI_DIR/php.ini

USER 1001

WORKDIR /app

CMD ["/usr/libexec/s2i/usage"]

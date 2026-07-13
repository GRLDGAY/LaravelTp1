FROM php:8.4-apache

RUN apt-get update \
    && apt-get install -y git zip unzip \
    && docker-php-ext-install pdo_mysql \
    && a2enmod rewrite \
    && rm -rf /var/lib/apt/lists/*

COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

RUN sed -i 's#/var/www/html#/workspace/projetLaravel/public#g' \
    /etc/apache2/sites-available/000-default.conf

RUN printf '<Directory /workspace/projetLaravel/public>\n\
    AllowOverride All\n\
    Require all granted\n\
</Directory>\n' \
    > /etc/apache2/conf-available/laravel.conf \
    && a2enconf laravel

RUN printf '#!/bin/bash\n\
while [ ! -f /workspace/projetLaravel/artisan ] || [ ! -d /workspace/projetLaravel/storage/framework/views ]; do\n\
    sleep 1\n\
done\n\
sleep 2\n\
chown -R www-data:www-data /workspace/projetLaravel/storage /workspace/projetLaravel/bootstrap/cache\n\
chmod -R 775 /workspace/projetLaravel/storage /workspace/projetLaravel/bootstrap/cache\n' \
    > /usr/local/bin/laravel-permissions \
    && chmod +x /usr/local/bin/laravel-permissions

WORKDIR /workspace/projetLaravel

CMD ["sh", "-c", "/usr/local/bin/laravel-permissions & exec apache2-foreground"]

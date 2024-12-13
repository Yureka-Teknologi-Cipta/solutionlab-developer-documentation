# Laravel
This documentation explains the purpose of each section in a typical Dockerfile for a Laravel application.

## Directory Structure
```
root
|-- deploy
|   |-- nginx.conf
|   |-- php.ini
|-- Dockerfile
```

## API only
This document provides a Dockerfile configuration specifically for a Laravel API-only application. It sets up a PHP-FPM environment with Composer for dependency management and Nginx as the web server. The configuration ensures proper permissions for Laravel's storage and cache directories and includes steps to install necessary PHP dependencies. For fullstack laravel app see #here.

```Dockerfile
FROM ghcr.io/faytranevozter/php:8.4-fpm AS base

FROM base AS build

# install composer
COPY --from=composer:2.7.6 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# copy necessary files and change permissions
COPY . .
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 775 /var/www/html/storage \
    && chmod -R 775 /var/www/html/bootstrap/cache

# install php dependencies
RUN composer install --no-dev --prefer-dist

RUN chown -R www-data:www-data /var/www/html/vendor \
    && chmod -R 775 /var/www/html/vendor

# stage 2: production stage
FROM base

# install nginx
RUN apk add --no-cache \
    nginx \
    && rm -rf /var/cache/apk/*

# copy files from the build stage
COPY --from=build /var/www/html /var/www/html
COPY ./deploy/nginx.conf /etc/nginx/http.d/default.conf
COPY ./deploy/php.ini "$PHP_INI_DIR/conf.d/app.ini"

WORKDIR /var/www/html

# add all folders where files are being stored that require persistence. if needed, otherwise remove this line.
# VOLUME ["/var/www/html/storage/app"]

CMD ["sh", "-c", "nginx && php-fpm"]
```

## Fullstack
This document provides a Dockerfile configuration specifically for a fullstack Laravel application. It sets up a PHP-FPM environment with Composer for dependency management, Nginx as the web server, and Node.js for handling frontend assets. The configuration ensures proper permissions for Laravel's storage and cache directories and includes steps to install necessary PHP and Node.js dependencies.

```Dockerfile
FROM ghcr.io/faytranevozter/php:8.4-fpm AS base

FROM base AS build

# installing nodejs and npm
RUN apk add --no-cache \
    nodejs \
    npm

# install composer
COPY --from=composer:2.7.6 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# copy necessary files and change permissions
COPY . .
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 775 /var/www/html/storage \
    && chmod -R 775 /var/www/html/bootstrap/cache

# install php and node.js dependencies
RUN composer install --no-dev --prefer-dist \
    && npm install \
    && npm run build

RUN chown -R www-data:www-data /var/www/html/vendor \
    && chmod -R 775 /var/www/html/vendor

# stage 2: production stage
FROM base

# install nginx
RUN apk add --no-cache \
    nginx \
    && rm -rf /var/cache/apk/*

# copy files from the build stage
COPY --from=build /var/www/html /var/www/html
COPY ./deploy/nginx.conf /etc/nginx/http.d/default.conf
COPY ./deploy/php.ini "$PHP_INI_DIR/conf.d/app.ini"

WORKDIR /var/www/html

# add all folders where files are being stored that require persistence. if needed, otherwise remove this line.
# VOLUME ["/var/www/html/storage/app"]

CMD ["sh", "-c", "nginx && php-fpm"]
```

## nginx.conf
This nginx.conf file is a configuration for an Nginx web server to serve a Laravel application.

```nginx
server {
  listen 80 default_server;
  listen [::]:80 default_server;

  root /var/www/html/public;
  client_max_body_size 10M;
  add_header X-Frame-Options "SAMEORIGIN";
  add_header X-Content-Type-Options "nosniff";

  index index.php;

  charset utf-8;

  location / {
    try_files $uri $uri/ /index.php?$query_string;
  }

  location = /favicon.ico {
    access_log off; log_not_found off;
  }
  location = /robots.txt {
    access_log off; log_not_found off;
  }

  error_page 404 /index.php;

  location ~ \.php$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
    include fastcgi_params;
  }
}
```

## php.ini
This php.ini file is a configuration for PHP settings to be used in the Docker container for the Laravel application.

```ini
upload_max_filesize = 10M
post_max_size = 12M
memory_limit = 256M
max_execution_time = 30
max_input_time = 60
```
